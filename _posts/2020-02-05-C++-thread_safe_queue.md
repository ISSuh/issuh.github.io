---
title: Thread Safe Queue
date: 2020-02-05 21:25:00
categories: [C++,  etc]
tags:
- C++
- Thead
- Thread Safe
- Queue
- Mutex
- Condition Variable
---

# Thread Safe Queue

mutex와 condition_variable을 통한 Thread Safe Queue Class 예제

```cpp
#include <queue>
#include <mutex>
#include <condition_variable>

template<typename DataType>
class Buffer{
public:
  /**
  * Create thread safe queue.
  * \param[in] Max queue size.
  **/
  Buffer(int maxSize = 0) : maxSize(m_maxSize) {};
  
  /*! Destroy thread safe queue. */
  ~Buffer(){
    std::lock_guard<std::mutex> lock(m_mutex);
  }
  
  /**
  * Push a data into the queue.
  * \param[in] Max queue size.
  * \return true if a data was pushed into the queue.
  **/
  bool push(const DataType& data){
    std::lock_guard<std::mutex> lock(m_mutex);
    
    if (m_maxSize > 0 && m_queue.size() > m_maxSize)
      return false;
        
    m_queue.push(data);
    m_cv.notify_one();
    
    return true;
  };
  
  /**
  * Pop data from the queue. 
  * If queue is empty, this function blocks until item becomes available.
  * \param[out] The data was poped in params variable.
  */
  void pop(DataType& data){
    std::unique_lock<std::mutex> lock(m_mutex);
    
    m_condition.wait (lock, [this]()
    {
        return !m_queue.empty();
    });
    
    data = m_queue.front();
    m_queue.pop();
  };
  
  /**
  * Try pop data from the queue like aysn function. 
  * \param[out] The data was poped in params variable.
  * \return False is returned if no item is available.
  */
  bool try_pop(DataType& data){
    std::unique_lock<std::mutex> lock(m_mutex);
    
    if (m_queue.empty())
      return false;
    
    data = m_queue.front();
    m_queue.pop();
    
    return true;
  };
  
  /**
  *  Gets the number of items in the queue.
  * \return Number of items in the queue.
  */
  int size const(){
    std::lock_guard<std::mutex> lock(m_mutex);
    return m_queue.size();
  };
  
  /**
  *  Check if the queue is empty.
  * \return true if queue is empty.
  */
  bool empty const(){
    std::lock_guard<std::mutex> lock(m_mutex);
    return m_queue.empty();
  };
  
private:
  std::queue<DataType> m_queue;
  std::mutex m_mutex;
  std::condition_variable m_cv;
  int m_maxSize = 0;
};

```
