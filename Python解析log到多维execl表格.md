```python
import xlwt
import sys, getopt
import ast
import time
import os

default_tags = {
                'test_1'   : 'test_1',
                'test_2'   : 'test_2',
                }

def check_charset(file_path):
    import chardet
    with open(file_path, "rb") as f:
        data = f.read(4)
        charset = chardet.detect(data)['encoding']
    return charset

def parse_file(tags, file_path, sheet):
    f = open(file_path, encoding=check_charset(file_path), errors='ignore')

    dict_check = {}

    line = f.readline()
    while line:
        if "gpu usage" in line and "gpu freq" in line:
            print("line:" + str(line))

            str_split = line.split(" ")
            case_name = str_split[len(str_split) - 8]
            case_time = str_split[len(str_split) - 6]
            case_usage = str_split[len(str_split) - 3]
            case_usage = str(case_usage[:-1].split(":")[1])
            print("hhh type(case_name)----", type(case_name))
            print("hhh case_name----" + str(case_name))
            print("hhh case_time----" + str(case_time))
            print("hhh case_usage----" + str(case_usage))

            list_each_line = []

            if case_name not in dict_check:
                list_each_line.append(1)
                list_each_line.append(case_time)
                list_each_line.append(case_usage)
                dict_check[case_name] = []
                dict_check[case_name].append(list_each_line)
            else:
                list_each_line.append(dict_check[case_name][-1][0] + 1)
                list_each_line.append(case_time)
                list_each_line.append(case_usage)
                dict_check[case_name].append(list_each_line)

        line = f.readline()
    
    print("1 dict_check----", dict_check)

    dict_conf = {}

    for i in dict_check.keys():
        if i[-6:] == "MEDIUM":
            for j in range(3):
                print("?????????????:", dict_check[i])
                dict_check[i[:-6] + "LOW"].append(dict_check[i][j])
            # dict_check[i[:-6] + "LOW"].append(dict_check[i])
        if i[-4:] == "HIGH":
            for j in range(3):
                dict_check[i[:-4] + "LOW"].append(dict_check[i][j])
            # dict_check[i[:-4] + "LOW"].append(dict_check[i])

    print("\n2 dict_check----", dict_check)

    rows = 2

    flag = 0

    for i in dict_check:
        if i[-6:] == "MEDIUM":
            flag = 1
            continue
        if i[-4:] == "HIGH":
            continue

        if flag == 1:
            rows = rows + 1
            flag = 0

        sheet.write(rows, 0, i[:-4])

        # 384*512
        sheet.write(rows, 1, dict_check[i][6][1] + "   " + dict_check[i][6][2])
        sheet.write(rows, 2, dict_check[i][3][1] + "   " + dict_check[i][3][2])
        sheet.write(rows, 3, dict_check[i][0][1] + "   " + dict_check[i][0][2])

        # 540*960
        sheet.write(rows, 5, dict_check[i][7][1] + "   " + dict_check[i][7][2])
        sheet.write(rows, 6, dict_check[i][4][1] + "   " + dict_check[i][4][2])
        sheet.write(rows, 7, dict_check[i][1][1] + "   " + dict_check[i][1][2])

        # 1080*1920
        sheet.write(rows, 9, dict_check[i][8][1] + "   " + dict_check[i][8][2])
        sheet.write(rows, 10, dict_check[i][5][1] + "   " + dict_check[i][5][2])
        sheet.write(rows, 11, dict_check[i][2][1] + "   " + dict_check[i][2][2])

        rows = rows + 1

def read_file(log_tags, log_path, out_file_name):
    tags = []

    xls = xlwt.Workbook(encoding="utf-8")
    sheet = xls.add_sheet('test', cell_overwrite_ok=True)

    sheet.write(0, 2, "384*512")
    sheet.write(0, 6, "540*960")
    sheet.write(0, 10, "1080*1920")

    sheet.write(1, 1, "High")
    sheet.write(1, 2, "Medium")
    sheet.write(1, 3, "Low")
    sheet.write(1, 5, "High")
    sheet.write(1, 6, "Medium")
    sheet.write(1, 7, "Low")
    sheet.write(1, 9, "High")
    sheet.write(1, 10, "Medium")
    sheet.write(1, 11, "Low")

    index = 1

    parse_file(tags, log_path, sheet)
    xls.save(out_file_name + '.xls')

    print("read_file log_path=%s out_file_name=%s end", log_path, out_file_name)


def main(argv):
    dirExists = os.path.isdir("PerformanceRecord")
    if not dirExists:
        os.mkdir("PerformanceRecord")
    input_path = ""
    output_name = "PerformanceRecord/" + time.strftime("%Y_%m_%d-%H_%M_%S", time.localtime()) #默认输出文件名为当前时间
    tags = default_tags #默认tag
    try:
        opts, args = getopt.getopt(argv, "hi:o:t:", ["ifile=", "oname=", "tags="])
    except getopt.GetoptError:
        print('python logcat_parser.py -i <inputfile> -o <outputfile> -t {"tag1": "title1", "tag2": "title2"}')

    for opt, arg in opts:
        if opt in ("-i", "--ifile"):
            input_path = arg
        elif opt in ("-o", "--ofile"):
            output_name = "PerformanceRecord/" + arg
        elif opt in ("-t", "--tags"):
            try:
                tags = ast.literal_eval(arg)
            except SyntaxError:
                print("Tags format error str=%s" % arg)

    if (input_path == ""):
        print("Input path is null!")
        sys.exit(2)

    read_file(tags, input_path, output_name)

if __name__ == '__main__':
    argv = sys.argv[1:]
    main(argv)
```

