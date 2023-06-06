```cpp
#include <string>
#include <iostream>

template<typename T>
T Get(T val) {
	return T();
}

int main() {
	cout << "-" << Get<int>(5) << "-" << endl;
	cout << "-" << Get<string>("abc") << "-" << endl;
	return 0;
}

// 输出
-0-
--

G:\notes\Test\Test\x64\Debug\Test.exe (进程 21380)已退出，代码为 0。
要在调试停止时自动关闭控制台，请启用“工具”->“选项”->“调试”->“调试停止时自动关闭控制台”。
按任意键关闭此窗口. . .

```

