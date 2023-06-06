### 1 直接获取main的命令行参数

```cpp
#include <iostream>

int main(int argc, char* argv[]) {
    std::cout << "命令行参数个数： " << argc << std::endl;

    for (int i = 0; i < argc; ++i) {
        std::cout << "参数 " << i << ": " << argv[i] << std::endl;
    }

    return 0;
}
```

### 2 使用getopt解析main的命令行参数（无法在windows平台使用）

```cpp
#include <iostream>
#include <unistd.h>

int main(int argc, char* argv[]) {
    int opt;
    int tValue = 0;
    int iValue = 0;

    // 使用循环和getopt函数解析命令行参数
    while ((opt = getopt(argc, argv, "t:i:")) != -1) {
        switch (opt) {
        case 't':
            tValue = std::stoi(optarg);  // 将参数转换为整数
            break;
        case 'i':
            iValue = std::stoi(optarg);  // 将参数转换为整数
            break;
        default:
            std::cerr << "未知选项: " << static_cast<char>(optopt) << std::endl;
            return 1;
        }
    }

    // 输出解析后的参数值
    std::cout << "tValue: " << tValue << std::endl;
    std::cout << "iValue: " << iValue << std::endl;

    return 0;
}

```

