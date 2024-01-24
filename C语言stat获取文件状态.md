在C语言中，`stat` 是一个系统调用（或库函数），用于获取文件的状态信息。通过 `stat` 函数，你可以获取文件的各种属性，例如文件大小、创建时间、修改时间等。

以下是 `stat` 函数的基本用法：

```c
#include <sys/stat.h>
#include <stdio.h>

int main() {
    const char *filename = "example.txt";
    struct stat fileStat;

    // 获取文件状态
    if (stat(filename, &fileStat) == 0) {
        printf("文件大小: %lld 字节\n", (long long)fileStat.st_size);
        printf("创建时间: %s", ctime(&fileStat.st_ctime));
        printf("修改时间: %s", ctime(&fileStat.st_mtime));
    } else {
        perror("获取文件状态失败");
    }

    return 0;
}

```

在这个示例中，`stat` 函数接受文件名和一个指向 `struct stat` 结构的指针，然后填充该结构以获取文件的状态信息。这样你就可以通过 `struct stat` 结构中的各个成员来获取不同的属性。注意，`stat` 函数的返回值为 0 表示成功，-1 表示失败，失败时可以使用 `perror` 函数输出错误信息。