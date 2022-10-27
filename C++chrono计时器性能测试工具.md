```cpp
// 用steady_clock
#include <chrono>
#define START_LOG_TIME()                                                   \
    std::chrono::steady_clock::time_point t1 =                             \
            std::chrono::steady_clock::now();                              \
    std::chrono::steady_clock::time_point t2 = t1;                         \
    std::chrono::duration<double> time_used =                              \
            std::chrono::duration_cast<std::chrono::duration<double>>(t2 - \
                                                                      t1);
#define PRINT_COST_TIME(name)                                                  \
    t2 = std::chrono::steady_clock::now();                                     \
    time_used = std::chrono::duration_cast<std::chrono::duration<double>>(t2 - \
                                                                          t1); \
    std::cout << name << " TIME COST: " << (time_used.count() * 1000)          \
              << " ms." << std::endl;                                          \
    t1 = t2;
// 返回秒，乘10的3方，返回毫秒
//.................................................................................
{
    START_LOG_TIME();
    for(int i=0;i<100;i++)
    	// ... 处理
    PRINT_COST_TIME("Name");
}
//.................................................................................
// 用high_resolution_clock
std::chrono::high_resolution_clock::time_point m_start = std::chrono::high_resolution_clock::now();
for(int i=0;i<100;i++)
    // ... 处理
auto m_end = std::chrono::high_resolution_clock::now();
draw_frame_us = std::chrono::duration_cast<std::chrono::nanoseconds>(m_end - m_start).count() * 1e-3;
// 返回纳秒，乘10的-3方，返回微秒
```

