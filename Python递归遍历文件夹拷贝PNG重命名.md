![image-20240124103249821](Images/Python遍历文件夹拷贝PNG重命名/image-20240124103249821.png)

![image-20240124103300275](Images/Python遍历文件夹拷贝PNG重命名/image-20240124103300275.png)

```python
import os
import shutil

def copy_and_rename_png(input_folder, output_folder):
    # 删除已存在的文件夹
    shutil.rmtree(output_folder, ignore_errors=True)

    # 创建新的文件夹
    os.makedirs(output_folder)

    for root, dirs, files in os.walk(input_folder):
        if 'OutputPng' in root:
            continue
        for file in files:
            if file.endswith(".png"):
                source_path = os.path.join(root, file)
                
                # 获取文件夹名作为前缀
                folder_name = os.path.basename(root)
                if 'mapdata' == folder_name:
                    continue
                
                # 构造新的文件名
                new_file_name = f"{folder_name}-{file}"
                
                destination_path = os.path.join(output_folder, new_file_name)

                # 创建目标文件夹（如果不存在）
                os.makedirs(os.path.dirname(destination_path), exist_ok=True)

                # 复制并重命名文件
                shutil.copy(source_path, destination_path)
                print(f"复制 {source_path} 到 {destination_path}")

if __name__ == "__main__":
    input_folder = "D:/ue/project/Test/mapdata"  # 替换为实际的输入文件夹路径
    output_folder = "D:/ue/project/Test/mapdata/OutputPng"  # 替换为实际的输出文件夹路径

    copy_and_rename_png(input_folder, output_folder)

```

