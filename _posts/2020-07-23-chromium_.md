---
title: Chromium - Multi-process Architecture
date: 2020-07-21 23:56:00
categories:
- Chromium
tags:
- Chromium
---

# Multi-process Architecture

This document describes Chromium's high-level architecture.

## Problem

절대 Crash 나 hang이 발생하지 않는 Rendering engine을 구성하는것은 거의 불가능 하다. 완벽하게 안전한 Rendering engine을 구성하는 것 또한 거의 불가능 하다.

2006년도 무렵의 Web browser는 단일 사용자인 co-operatively한 multi-tasked 운영체제와 비슷하다. 잘못 동작하는 application으로 인하여 운영체제의 모든 시스템이 멈추는 것 처럼 잘못 동작하는 web page가 web browser의 동작이 멈췄다. 하나의 browser 또는 plug-in의 버그로 인하여 전체 browser 그리고 실행중인 모든 탭이 다운될수 있었다.

현대의 운영체제는 application이 서로가 분리된 프로세스로 동작하기 때문에 더 강력하다. 하나의 application의 crash는 다른 application이나 운영체제의 무결성을 손상시키지 않고, 각각의 사용자는 다른 사용자 데이터의 접근이 제한되어 진다.

### Architectural overview

Chromium은 rendering engine에서의 버그나 결함으로 부터 전체 application을 보호하기 위해 browser tab에 별도의 프로세스를 사용한다. 또한 rendering engine 프로세스가 다른 프로세스나 나머지 시스템에 접근하는 것을 금지한다. 이는 web browsing하는데 있어 memory protection과 access control에 대한 이점을 준다. 

Chromium은 UI를 실행하고 tab과 plugin processes를 "browser process"또는 "browser"로써 관리하는 main process를 제공한다. 아와 비슷하게, tab별 프로세스를 "render processes"또는 "renderers"라고 부른다. renderer는 HTML을 해석하고 표현하기위해 오픈소스 layout engine인 [Blink](https://www.chromium.org/blink)를 사용한다.

![picture](https://www.chromium.org/_/rsrc/1220197832277/developers/design-documents/multi-process-architecture/arch.png)

#### Managing render processes

각 render 프로세스는 parent browser 프로세스와 통신을 관리하고, global state를 유지하는 global *RenderProcess* object를 가지고 있다. browser는 각 render 프로세스와 대응되는 *RenderProcessHost*를 유지한다. *RenderProcessHost*는 browser state를 관리하고 renderer와 통신하는 역활을 한다. browser와 renderer들은 [Chromium의 IPC system](https://www.chromium.org/developers/design-documents/inter-process-communication)을 이용하여 통신한다.

#### Managing views

각 render 프로세스는 *RenderProcess*에 의해 관리되고 tab들의 content에 해당하는 *RenderView* objects를 하나이상 가지고 있다. 이 *RenderProcessHost*는 각 render가 가지고 있는 view와 대응되는 *RenderViewHost*를 유지한다. 각각의 view에는 동일한 renderer에서 여러 view들과 구별하기 위해, view ID가 주어진다. 이 view ID들은 하나의 renderer안에서는 유일하지만, 상위 영역인 browser안에서는 유일하지 않다. 그렇기 때문에 browser에서 view를 구별하기 위해 view ID뿐만아니라 *RenderProcessHost*도 필요하다. contents의 browser에서 특정 tab로의 통신은, *RenderProcessHost*에서 *RenderProcess*와 *RenderView*에게 message들을 보내는 방법을 알고 있는 *RenderViewHost* objects를 통해 이루어진다.

### Components and interfaces

In the render process:
 - *RenderProcess*는 browser상에서 *RenderProcess*와 대응되는 *RenderProcessHost*와의 IPC를 handling한다. 정확히 하나의 render process당 하나의 *RenderProcess* object가 있다. 이것이 모든 browser와 renderer간의 통신 방식이다.
 - *RenderView* object는 *RenderProcess*를 통해 자신과 대응되는 browser 프로세스에 속한 *RenderViewHost* 와 통신하고, WebKit의 embedding layer이다. 이 object는 tab이나 popup window의 한 web page의 contents를 표현한다.

In the browser process:
 - *Browser* object는 최상위 browser window을 나타낸다.
 - *RenderProcessHost* object는 browser side에서 하나의 browser ↔ renderer 간 IPC 연결을 나타낸다. browser 프로세스의 하나의 *RenderProcessHost*는 render 프로세스와 대응된다.
 - *RenderViewHost* object는 원격 *RenderView*를 encapsulates 하고, *RenderWidgetHost*는 browser안의 *RenderWidget*의 input과 painting을 다룬다.

이 동작에 대해 더 상세한 정보를 원하면, [How Chromium displays web pages design](https://www.chromium.org/developers/design-documents/displaying-a-web-page-in-chrome) 문서를 참고하여라.

## Sharing the render process

일반적으로, 새로운 window 또는 tab은 매번 새로운 프로세스에서 열린다. browser는 새로운 프로세스를 생성하고 단일 *RenderView*를 생성하도록 지시한다.

때때로 render 프로세스를 tab이나 window와 공유하도록 해야한다거나 하도록 하고싶을때가 있다. 하나의 web application은, 예를 들면 JavaScript의 window.open를 이용할때, 동기적으로 통신하는 것을 예상하는 새로운 window를 연다. 이와 같은 경우, 새로운 window또는 tab을 생성할때, 이미 열려있는 window와 함께 프로세스를 재사용할 필요가 있다. 또한 만약 프로세스의 수가 너무 많거나, 이미 가고자 하는 domain을 가리키는 프로세스에 새로운 tab을 할당하는 전략을 가지고 있다. 이러한 전략은 [Process Models](https://www.chromium.org/developers/design-documents/process-models) 에서 설명한다.

### Detecting crashed or misbehaving renderers

browser 프로세스에 대한 각 IPC 연결은 프로세스 handle을 감시한다. 만역 handle이 신호를 받으면, render 프로세스는 crash 되어지고 tab에 crash를 알린다. 이후, renderer가 crash되었다는 것을 유저에게 알리는 "sad tab" 화면을 보여준다. 이 페이지는 reload 버튼을 누르거나 새로운 탐색을 시작하여 다시 로드 될 수 있다. 이렇게 되면 새로운 프로세스가 없는 것을 알게되고, 새로운 프로세스를 만든다.

### Sandboxing the renderer

renderer가 별도의 분리된 프로세스로 운영되는 상황에서 [sandboxing](https://www.chromium.org/developers/design-documents/sandbox)를 통해 시스템 자원에 대한 접근을 제한하는 기회를 가진다. 예를 들어  renderer의 네트워크에 대한 접근은 오직  parent browser 프로세스만 가능하도록 보장한다. 이와 비슷하게 운영체제의 기본 접근권한을 이용하여 파일시스템에 대한 접근을 제한한다.

renderer의 파일시스템과 네트워크 접근제한 이외에, 사용자의 display와 관련된 object에 대한 접근도 제한할 수 있다. 각 render 프로세스는 사용자에게 보이지 않는 별도의 Windows "[Desktop](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682573(v=vs.85).aspx)"에서 실행되며, 이는 손상된 renderer가 새 창을 열거나 키 입력을 캡처하는 것을 방지할 수 있도록 한다.

## Giving back memory

분리된 프로세스에서 실행되는 renderer의 경우, 숨겨진 tab을 낮은 우선순위로 간주하는 것이 간단해 진다. 일반적으로 Windows상의 최소화된 프로세스는 "available memory" pool에 자동으로 메모리를 넣는다. low-memory 상황에서 Windows는 우선순위가 높은 메모리를 스왑하기 전에 이 메모리를 디스크로 교환하여 사용자가 볼 수 있는 프로그램의 응답성을 높힌다. 이와 같은 원칙을 숨겨진 tab에 적용할 수 있다. render 프로세스가 상위 tab이 없는 경우, 필요한 경우 먼저 메모리를 디스크로 스왑하기 위해 해당 프로세스의 "working set" 크기를 시스템에 힌트로 release 할 수 있다. "working set" 크기를 줄이면 사용자가 두 탭 사이를 전환할 때 탭 전환 성능도 떨어진다는 것을 알게 되었기 때문에, 우리는 이 메모리를 점진적으로 해제한다. 즉, 사용자가 최근에 사용한 탭으로 다시 전환하면 해당 탭의 메모리가 최근에 사용되지 않은 탭보다 호출될 가능성이 더 높다는 것을 의미한다. 모든 프로그램을 실행하기에 충분한 메모리를 가진 사용자들은 이 과정을 전혀 알아차리지 못할 것이다(Windows는 실제로 필요한 경우에만 그러한 데이터를 회수할 것이기 때문에 충분한 메모리가 있을 때 성능 적중 현상이 일어나지 않는다.)

이것은 우리가 low-memory 상황에서 더 최적의 메모리 공간을 얻을 수 있도록 도와준다. 거의 사용하지 않는 background tab과 관련된 메모리는 완전히 교환될 수 있고 foreground tab의 데이터는 메모리에 완전히 로드될 수 있다. 이와는 대조적으로, 단일 프로세스 browser는 모든 tab의 데이터를 메모리에 무작위로 분산시킬 것이며, 사용한 데이터와 사용하지 않은 데이터를 이렇게 깨끗하게 분리하는 것은 불가능하여 메모리와 성능을 모두 낭비하게 된다.

### Plug-ins and Extensions

파이어폭스 스타일의 NPAPI 플러그인은 renderer와 별도로 자체 프로세스로 실행되었다. 이것은 [Plugin Architecture](https://www.chromium.org/developers/design-documents/plugin-architecture)에 자세히 설명되어 있다. 

[Site Isolation](https://www.chromium.org/developers/design-documents/site-isolation) 프로젝트는 renderers간에 더 많은 격리를 제공하는 것을 목표로 하며, 이 프로젝트의 초기 결과물에는 분리된 프로세스에서 Chrome의 HTML/JavaScript 콘텐츠 확장을 실행하는 것이 포함된다.

---

> https://www.chromium.org/developers/design-documents/multi-process-architecture