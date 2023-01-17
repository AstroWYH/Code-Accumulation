- 对于mediapipe的ImageFrame、Mat、RGB buffer，都可以最终转换为Mat来dump。最终是通过cv::cvtColor转换为BGR Mat，然后通过imwrite写入。
- 对于raw buffer，则采用文件的形式写入，比如yuv buffer（nv21/nv12）。

```cpp
#include <opencv2/opencv.hpp>
#include <opencv2/core/mat.hpp>

#include "log.h"
#include "mediapipe/image_frame.h"
#include <string>

void DumpImageFrame(const std::string &path, const ImageFrame &dump);
void DumpMat(const std::string& path, cv::Mat& dump, int cv_transform = 0);
void DumpRGBxBuffer(const std::string file, void *buffer, size_t len, int width, int height, int cv_type = CV_8UC4);
void DumpRawBuffer( void const *buf, size_t size, char const *path);

void DumpImageFrame(const std::string &path, const ImageFrame &dump) {
    int cv_type = 0;
    int cv_format = 0;
    switch (dump.format_) {
        case IMAGE_FORMAT_SRGB:
            cv_format = cv::COLOR_RGB2BGR;
            cv_type = CV_8UC3;
            break;
        case IMAGE_FORMAT_GRAY8:
            cv_format = cv::COLOR_GRAY2BGR;
            cv_type = CV_8UC1;
            break;
        case IMAGE_FORMAT_SRGBA:
            cv_format = cv::COLOR_RGBA2BGR;
            cv_type = CV_8UC4;
            break;
        default:
            LOGE(0, "not support format %d", dump.format_);
            break;
    }
    cv::Mat rgb_dump(dump.height_, dump.width_, cv_type, dump.pixel_data_.get());
    cv::Mat bgr_dump;
    cv::cvtColor(rgb_dump, bgr_dump, cv_format);
    bool ret = cv::imwrite(path, bgr_dump);
    if (ret)
        LOGD("DumpImageFrame %s done!", path.c_str());
    else
        LOGE("DumpImageFrame imwrite ret:%d", ret);
}

void DumpMat(const std::string &path, cv::Mat &dump, int cv_transform) {
    cv::Mat bgr_transfrom;
    if (cv_transform != 0) {
        cv::cvtColor(dump, bgr_transfrom, cv_transform);
    } else {
        bgr_transfrom = dump;
    }
    cv::imwrite(path, bgr_transfrom);
}

void DumpRGBxBuffer(const std::string file, void *buffer, size_t len, int width,
                         int height, int cv_type) {
    int bpp = 0;
    if (cv_type == CV_8UC3) {
        bpp = 3;
    } else if (cv_type == CV_8UC4) {
        bpp = 4;
    } else if (cv_type == CV_8UC1){
        bpp = 1;
    }else {
        LOGE("not support cv type %d", cv_type);
        assert(0);
    }
    auto expected = width * height * bpp;
    if (len < expected) {
        LOGE("invalid memory size %zu, expected %d", len, expected);
        assert(0);
    }
    cv::Mat rgb_dump(height, width, cv_type, buffer);
    cv::Mat bgr_dump;
    if (cv_type == CV_8UC1) {
        cv::cvtColor(rgb_dump, bgr_dump,  cv::COLOR_GRAY2BGR);
    } else {
        cv::cvtColor(rgb_dump, bgr_dump, (cv_type == CV_8UC3 ? cv::COLOR_RGB2BGR : cv::COLOR_RGBA2BGR));
    }
    bool ret = cv::imwrite(file, bgr_dump);
    if (ret)
        LOGD("DumpRGBxBuffer %s done!", file.c_str());
    else
        LOGE("DumpRGBxBuffer imwrite ret:%d", ret);
}

void DumpRawBuffer(void const *buf, size_t size, char const *path) {
    FILE *fp = fopen(path, "wb");
    if(!fp) {
        LOGE("save_file %s error: %s", path, strerror(errno));
        return;
    }
    size_t len = fwrite(buf, 1, size, fp);
    LOGD("save_file %s size: %zu len: %zu", path, size, len);
    fclose(fp);
}

```

