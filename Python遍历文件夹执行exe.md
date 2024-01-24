output_png_folder全是png，将其全部通过exe，转换成dds

```c
import os
import subprocess

# 指定OutputPng文件夹路径
output_png_folder = 'D:/path/to/OutputPng'

# 遍历OutputPng文件夹中的所有.png文件
for png_file in os.listdir(output_png_folder):
    if png_file.endswith('.png'):
        # 构建输入和输出路径
        input_png_path = os.path.join(output_png_folder, png_file)
        output_dds_path = os.path.join(output_png_folder, os.path.splitext(png_file)[0] + '.dds')

        # 构建命令行参数
        command = ['ConvertPngToDDS.exe', input_png_path, output_dds_path]

        # 执行外部命令
        subprocess.run(command)

print("Conversion completed.")

import os

# 获取当前脚本文件的绝对路径
current_script_path = os.path.abspath(__file__)

# 获取当前脚本文件所在的目录
current_script_directory = os.path.dirname(current_script_path)

print("当前脚本文件路径:", current_script_path)
print("当前脚本所在目录:", current_script_directory)

```

