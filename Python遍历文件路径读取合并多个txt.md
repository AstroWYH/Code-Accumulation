```python
import copy # 注意copy的使用，注意浅拷贝和深拷贝的问题
import os
import json # 本例用到json包非常好用的加引号功能，将python dict转换成json格式

filepath = r'G:\work\code_test\python_xxx' # 遍历多个.txt的文件路径
filenames = [file for file in os.walk(filepath)] # 注意当前文件夹遍历的方式
print("[wangyuhang] filenames:", filenames)

double_list = []
single_list = []
tmp_list = []

for each in filenames[0][-1]: # 注意当前文件夹遍历的方式
    print("[wangyuhang] each:", each) # each是当前文件夹的每个.txt文件名
    if each == "combine_txt.py" or each == "combined.txt" or each == "str2json.py":
        print("[wangyuhang] meet combine_txt.py, continue!")
        continue
    with open(filenames[0][0]+'\\'+each, mode='r', encoding='utf-8') as f: # 对每个each即点.txt进行open
        print("[wangyuhang] open:", filenames[0][0]+'\\'+each)
        txt = f.readlines() # 对每个each即点.txt进行read
        # print("[wangyuhang] txt:", txt)
        double_list.append(txt) # 将每个.txt读取到的内容加入到double_list

# print("[wangyuhang] double_list:", double_list) # double_list已经是多个.txt合并的结果，但外层还有一层[]包装

frame_cnt = 0
flag_cnt = 0
# is_need_update = true
# af_x = "7"
# af_y = "7"

af_left = ""
af_top = ""
af_right = ""
af_bottom = ""

for i in double_list:
    for j in i:
        if len(j) > 0: # 通过两层遍历，在循环内用single_list.append(j)，single_list得到的就是纯净的多个.txt合并的结果，j是每行.txt内容
            if j[0:10] == "image info":
                flag_cnt += 1
                frame_cnt += 1
            if flag_cnt == 2: # image info遇到后没遇到Face，又直接遇到下一image info时，补充这一帧信息
                flag_cnt -= 1
                # is_need_update = true
                tmp_list.append(af_left)
                tmp_list.append(af_top)
                tmp_list.append(af_right)
                tmp_list.append(af_bottom) # 使用AF补充7*7的框，用于无Face帧使用
                tmp_list.append("frameIndex:" + str(frame_cnt - 1) + "\n")
                tmp_list.append("isFocusChanged:false\n")
                print("[wangyuhang] tmp_list:", tmp_list)
                single_list.append(tmp_list)
                # print("[wangyuhang] single_list:", single_list)
                tmp_list = []
            if j[0:8] == "afLeft_R":
                af_left = j
            if j[0:7] == "afTop_R":
                af_top = j
            if j[0:9] == "afRight_R":
                af_right = j
            if j[0:10] == "afBottom_R":
                af_bottom = j
                # if is_need_update == true:
                #     cache_af_x = af_x
                #     cache_af_y = af_y
                #     is_need_update = false
            if j[0:10] == "FaceLeft_R" and flag_cnt == 1:
                tmp_list.append(j)
            if j[0:9] == "FaceTop_R" and flag_cnt == 1:
                tmp_list.append(j)
            if j[0:11] == "FaceRight_R" and flag_cnt == 1:
                tmp_list.append(j)
            if j[0:12] == "FaceBottom_R" and flag_cnt == 1:
                tmp_list.append(j)
                if flag_cnt == 1:
                    tmp_list.append("frameIndex:" + str(frame_cnt) + "\n")
                    flag_cnt = 0
                tmp_list.append("isFocusChanged:false\n")
                print("[wangyuhang] tmp_list:", tmp_list)
                single_list.append(tmp_list)
                # print("[wangyuhang] single_list:", single_list)
                tmp_list = []

            print("[wangyuhang] frame_cnt:", frame_cnt)
            print("[wangyuhang] flag_cnt:", flag_cnt)

if flag_cnt != 0:
    tmp_list.append("frameIndex:" + str(frame_cnt) + "\n")
    tmp_list.append("isFocusChanged:false\n")
    single_list.append(tmp_list)
    flag_cnt = 0

print("[wangyuhang] single_list:", single_list)
print("[wangyuhang] type(single_list):", type(single_list))

with open("combined.txt", 'w', encoding='utf-8') as f:
    for i in single_list:
        for j in i:
            f.write(j)

dict_list = []
dict_outer = {}
dict_inner = {}

cnt_i = 0
cnt_j = 0

tmp_left = ""
tmp_top = ""
tmp_right = ""
tmp_bottom = ""

# is_assign_tmp_bottom = 1

for i in single_list: # 遍历每1帧
    cnt_i += 1
    print("[wangyuhang] cnt_i:", cnt_i)

    dict_inner.clear()
    dict_inner["bottom"] = ""
    dict_inner["left"] = ""
    dict_inner["right"] = ""
    dict_inner["top"] = ""

    is_enter = True

    for j in i: # 遍历每1行，1帧包括6行
        cnt_j += 1
        print("[wangyuhang] cnt_j:", cnt_j)

        is_face = True

        # 提供face的帧，使用face参数
        if j[0:10] == "FaceLeft_R":
            # print("[wangyuhang] j:", j.split()[-1])
            tmp_left = j.split()[-1] # 反向截取face数据
            # tmp_left = format(float(j.split()[-1]) / 2432, '.6f')
        if j[0:9] == "FaceTop_R":
            tmp_top = j.split()[-1]
            # tmp_top = format(float(j.split()[-1]) / 1408, '.6f')
        if j[0:11] == "FaceRight_R":
            tmp_right = j.split()[-1]
            # tmp_right = format(float(j.split()[-1]) / 2432, '.6f')
        if j[0:12] == "FaceBottom_R":
            tmp_bottom = j.split()[-1]
            # tmp_bottom = format(float(j.split()[-1]) / 1408, '.6f')
            is_face = True

        # 不提供face的帧，使用af参数
        if j[0:8] == "afLeft_R":
            # print("[wangyuhang] j:", j.split()[-1])
            tmp_left = j.split()[-1]
            # tmp_left = format(float(j.split()[-1]) / 2432, '.6f')
        if j[0:7] == "afTop_R":
            tmp_top = j.split()[-1]
            # tmp_top = format(float(j.split()[-1]) / 1408, '.6f')
        if j[0:9] == "afRight_R":
            tmp_right = j.split()[-1]
            # tmp_right = format(float(j.split()[-1]) / 2432, '.6f')
        if j[0:10] == "afBottom_R":
            tmp_bottom = j.split()[-1]
            # tmp_bottom = format(float(j.split()[-1]) / 1408, '.6f')
            is_face = False

        # dict_inner["bottom"] = (float)tmp_bottom / 2432
        # dict_inner["left"] = tmp_left / 1408
        # dict_inner["right"] = tmp_right / 1408
        # dict_inner["top"] = tmp_top / 2432

        # if tmp_bottom != "":
        #     print("[wangyuhang] test before tmp_bottom:", tmp_bottom)
        #     print("[wangyuhang] test before type(tmp_bottom):", type(tmp_bottom))
        #     tmp_bottom = format(float(tmp_bottom) / 2432, '.6f')
        #     print("[wangyuhang] test after tmp_bottom:", tmp_bottom)
        #     print("[wangyuhang] test after type(tmp_bottom):", type(tmp_bottom))

        # print("[wangyuhang] tmp_bottom:", tmp_bottom)
        # print("[wangyuhang] type(tmp_bottom):", type(tmp_bottom))

        if dict_inner["bottom"] == "":
            dict_inner["bottom"] = tmp_bottom
        if dict_inner["left"] == "":
            dict_inner["left"] = tmp_left
        if dict_inner["right"] == "":
            dict_inner["right"] = tmp_right
        if dict_inner["top"] == "":
            dict_inner["top"] = tmp_top

        tmp_bottom = ""
        tmp_left = ""
        tmp_right = ""
        tmp_top = ""

        print("[wangyuhang] test inner dict_inner[bottom]:", dict_inner["bottom"])
        print("[wangyuhang] test inner dict_inner[left]:", dict_inner["left"])
        print("[wangyuhang] test inner dict_inner[right]:", dict_inner["right"])
        print("[wangyuhang] test inner dict_inner[top]:", dict_inner["top"])

        if dict_inner["bottom"] != "" and dict_inner["left"] != "" and dict_inner["right"] != "" and dict_inner["top"] != "" and is_enter == True:
            is_enter = False # 不允许重复进入

            if is_face == False: # af数据转换成为30*30
                tmp_af_bottom = dict_inner["bottom"]
                tmp_af_left = dict_inner["left"]
                tmp_af_right = dict_inner["right"]
                tmp_af_top = dict_inner["top"]
                dict_inner["bottom"] = format((float(tmp_af_bottom) + float(tmp_af_top)) / 2 + 30, '.6f')
                dict_inner["left"] = format((float(tmp_af_left) + float(tmp_af_right)) / 2 - 30, '.6f')
                dict_inner["right"] = format((float(tmp_af_left) + float(tmp_af_right)) / 2 + 30, '.6f')
                dict_inner["top"] = format((float(tmp_af_bottom) + float(tmp_af_top)) / 2 - 30, '.6f')
                print("[wangyuhang] dict_inner AF value!!! cnt_i:{} bottom:{} left:{} right:{} top:{}".format(cnt_i, dict_inner["bottom"], dict_inner["left"], dict_inner["right"], dict_inner["top"]))
            else: # face数据无需转换
                print("[wangyuhang] dict_inner FACE value!!! cnt_i:{} bottom:{} left:{} right:{} top:{}".format(cnt_i, dict_inner["bottom"], dict_inner["left"], dict_inner["right"], dict_inner["top"]))

            # 将face或af数据normalize为0-1
            dict_inner["bottom"] = format(float(dict_inner["bottom"]) / 1080, '.6f')
            dict_inner["left"] = format(float(dict_inner["left"]) / 1920, '.6f')
            dict_inner["right"] = format(float(dict_inner["right"]) / 1920, '.6f')
            dict_inner["top"] = format(float(dict_inner["top"]) / 1080, '.6f')

            print("[wangyuhang] dict_inner all DEFAULT value!!! cnt_i:{} bottom:{} left:{} right:{} top:{}".format(cnt_i, dict_inner["bottom"], dict_inner["left"], dict_inner["right"], dict_inner["top"]))
            tmp_swap_bottom = dict_inner["bottom"]
            tmp_swap_left = dict_inner["left"]
            tmp_swap_right = dict_inner["right"]
            tmp_swap_top = dict_inner["top"]

            dict_inner["bottom"] = tmp_swap_right
            dict_inner["left"] = format(1 - float(tmp_swap_bottom), '.6f')
            dict_inner["right"] = format(1 - float(tmp_swap_top), '.6f')
            dict_inner["top"] = tmp_swap_left

            # dict_inner["bottom"] = format(1 - float(tmp_swap_left), '.6f')
            # dict_inner["left"] = tmp_swap_top
            # dict_inner["right"] = tmp_swap_bottom
            # dict_inner["top"] = format(1 - float(tmp_swap_right), '.6f')
            print("[wangyuhang] dict_inner all SWAP value!!! bottom:{} left:{} right:{} top:{}".format(dict_inner["bottom"], dict_inner["left"], dict_inner["right"], dict_inner["top"]))

        if j[0:10] == "frameIndex":
            # print("[wangyuhang] j:", j.split(':')[-1].split('\n')[0])
            dict_idx =  j.split(':')[-1].split('\n')[0]

    print("[wangyuhang] test outer dict_inner[bottom]:", dict_inner["bottom"])
    print("[wangyuhang] test outer dict_inner[left]:", dict_inner["left"])
    print("[wangyuhang] test outer dict_inner[right]:", dict_inner["right"])
    print("[wangyuhang] test outer dict_inner[top]:", dict_inner["top"])

    if dict_inner["bottom"] != "":
        dict_outer["focusRect"] = dict_inner.copy() # 注意copy的使用，注意浅拷贝和深拷贝的问题
        print("[wangyuhang] enter frame {} line {}".format(str(cnt_i), str(cnt_j)))
    else:
        print("[wangyuhang] the empty line:", cnt_j)

    dict_outer["frameIndex"] = dict_idx
    dict_outer["isFocusChanged"] = "false"

    print("[wangyuhang] dict_outer:", dict_outer.copy()) # 注意copy的使用，注意浅拷贝和深拷贝的问题
    dict_list.append(dict_outer.copy()) # 注意copy的使用，注意浅拷贝和深拷贝的问题
    dict_outer.clear()
    print("[wangyuhang] dict_list:", dict_list)

# print("[wangyuhang] dict_list:", dict_list)

out = json.dumps(dict_list) # json包非常好用的加引号功能，将python dict转换成json格式
print("[wangyuhang] out:", out)

f = open("out.txt", "w", encoding='utf-8')
f.write(str(out))
f.close()
```

