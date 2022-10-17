```python
import os
import shutil # 本例用到其文件拷贝的功能

filepath = r'D:\Projects\Issues\Video\xxx' # 多帧.png输入路径
filenames = [file for file in os.walk(filepath)]
outpath = r'D:\Projects\Issues\Video\xxx\output' # 多帧序列化.png输出路径

os.makedirs(outpath) # 新建文件夹

print("[wangyuhang] filenames:", filenames)

cnt = 0

for each in filenames[0][-1]: # 使用同样的方式遍历当前文件路径，得到每个文件名each
    print("[wangyuhang] each:", each)
    each_whole = os.path.join('D:\\Projects\\Issues\\Video\\xxx\\', each) # 多帧.png输入路径，双反斜杠
    print("[wangyuhang] each_whole:", each_whole)
    print("[wangyuhang] each reverse 3 words:", each[::-1][0:3]) # 注意字符串str的截取等，反向+截取

    if each[::-1][0:3] == "gnp":
        cnt += 1
        print("[wangyuhang] cnt:", cnt)
        shutil.copyfile(each_whole, outpath + '\\' + str(cnt) + '.' + 'png') # 文件拷贝+重命名
    

```

