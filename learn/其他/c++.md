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


