# ♾️知识点
## 💫map使用
```c
int main(int argc, char *argv[]) {  
    auto map = unordered_map<string, string>();  
  
    map.insert({"1", "first"});  
    map.insert({"2", "second"});  
    map.insert({"3", "third"});  
    auto it = map.find("1");  
    if (it != map.end()) {  
        cout << it->first << ": " << it->second << endl;  
    } else {  
        cout << "1: Not Found" << endl;  
    }  
  
    it = map.find("4");  
    if (it != map.end()) {  
        cout << it->first << ": " << it->second << endl;  
    } else {  
        cout << "4: Not Found" << endl;  
    }  
}
```

## 💫split函数
```c
vector<string> split(string s, char delimiter) {  
    vector<string> tokens;  
  
    int slow = 0, fast = 0;  
    while (fast < s.size()) {  
        // slow先找到非delimiter字符  
        while (slow < s.size() && s[slow] == delimiter) {  
            slow++;  
        }  
        fast = slow;  
        // fast先找到delimiter字符  
        while (fast < s.size() && s[fast] != delimiter) {  
            fast++;  
        }  
        // 将slow:fast存入tokens  
        if (slow < fast) {  
            tokens.push_back(s.substr(slow, fast - slow));  
        }  
        slow = fast;  
    }  
  
    return tokens;  
}
```

## 💫vector使用
```c
class Solution {
public:
    vector<vector<int> > merge(vector<vector<int> > &intervals) {
        sort(intervals.begin(), intervals.end(),
             [](auto &x, auto &y) { return x[0] < y[0]; });

        vector<vector<int> > result;
        for (int i = 0; i < intervals.size(); ++i) {
            if (result.empty())
                result.push_back(intervals[i]);
            else {
                auto before = result.back();
                if (before[1] < intervals[i][0]) {
                    result.push_back(intervals[i]);
                } else {
                    if (before[1] >= intervals[i][1]) {
                        continue;
                    }
                    result.pop_back();
                    vector<int> temp = {before[0], intervals[i][1]};
                    result.push_back(temp);
                }
            }
        }
        return result;
    }
};
```

# ♾️vector
> 创建

```c
vector<int> vec1 = {5, 4, 1, 3, 4};
```

> 访问

```c
vec[0]
vec.at(1)
```

> 数组的前和后

注意是`front`和`back`

```c
cout << "front: " << vec.front() << endl;  
cout << "end: "  << vec.back() << endl;
```

> 尾部: 添加和删除

```c
vec.push_back(100);
vec.pop_back();
```

> 指定位置插入

注意是`begin`和`end`
```c
vec.insert(vec.begin(), 0);  
vec.insert(vec.end(), 101);
```

> 排序

```c
// 升序
sort(vec.begin(), vec.end());

// 降序
sort(vec.begin(), vec.end(), greater<int>());
```

> 遍历

```c
// 使用范围 for 循环遍历  
for (int num : vec) {  
    std::cout << num << " ";  
}  
std::cout << std::endl;
```

# ♾️unordered_map

> 创建

```c
unordered_map<int, string> m;
```

> 插入

注意, 插入是覆盖的!
```c
m[1] = "one";  
m[2] = "two";  
  
m[1] = "new one";
```

还有一种非覆盖插入
```c
m.insert({1, "new one"});
```

> 删除

```c
m.erase(1);
```

> 查找

```c
auto it = m.find(2);  
if (it != m.end()) {  
    cout << it->first << ": "<< it->second;  
} else {  
    cout << "fail to find 2";  
}
```

> 遍历

```c
for (const auto& pair : m) {  
    cout << pair.first << ": " << pair.second << endl;  
}
```


# ♾️导包

```c
#include <algorithm>   // sort算法
#include <string>  
#include <unordered_map>  
#include <vector>
#include <iostream> // cout
```

```c
// 对字符串进行排序处理  
sort(strs[i].begin(), strs[i].end());
```
