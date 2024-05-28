```cpp
/******************************************************************************

                              Online C++ Compiler.
               Code, Compile, Run and Debug C++ program online.
Write your code in this editor and press "Run" button to compile and execute it.

*******************************************************************************/

#include <iostream>
#include <vector>
#include <algorithm>

enum class ItemType {
    Grass,
    Sky,
    Flower
};

ItemType GetType(int Id)
{
   if (Id == 1) return ItemType::Flower;
   if (Id == 2) return ItemType::Sky;
   if (Id == 5) return ItemType::Grass;
   if (Id == 8) return ItemType::Flower;
}

bool CompareByItemType(int A, int B) {
    ItemType ItemTypeA = GetType(A);
    ItemType ItemTypeB = GetType(B);

    // 根据 ItemType 的大小关系进行排序
    return ItemTypeA < ItemTypeB;
}

int main()
{
    std::vector<int> MyArray {1,2,5,8};
    std::sort(MyArray.begin(), MyArray.end(), CompareByItemType);
    for (const int val : MyArray)
    {
        std::cout << val << std::endl;
    }
    return 0;
}
```
