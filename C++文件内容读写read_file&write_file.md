```cpp
#include <stdio.h>

vector<unsigned char> read_file(char const* path) {
    vector<unsigned char> data;
    FILE* fin = fopen(path, "rb");

    if (fin) {
        fseek(fin, 0, SEEK_END);
        size_t len = ftell(fin);
        rewind(fin);

        data.resize(len);
        fread(data.data(), 1, len, fin);
        fclose(fin);
    }

    return data;
}

void write_file(string & file_name, unsigned char *data, int file_length) {
    string file_path("./output/");
    file_path += file_name;
    FILE* fout = fopen(file_path.c_str(), "wb");

    if (fout) {
        fwrite(data, sizeof(char), file_length, fout);
        fclose(fout);
    }
}
```

