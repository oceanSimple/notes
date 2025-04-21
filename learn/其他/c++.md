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

erase()
count()

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

# ♾️stack
push
pop
top

# ♾️queue
push
pop
front


# ♾️导包

```c
#include <algorithm>   // sort算法
#include <string>  
#include <unordered_map>  
#include <vector>
#include <iostream> // cout cin
```

```c
// 对字符串进行排序处理  
sort(strs[i].begin(), strs[i].end());
```

# ♾️转换
- int -> string
	- to_string(i)
- string -> int
	- stoi(i)
- char -> string
	- string(1, str[0])


# ♾️代码
## 💫树的高度


```c
#include <iostream>
#include <unordered_map>
using namespace std;

class TreeNode {
public:
    int value;
    TreeNode *left;
    TreeNode *right;

    TreeNode(int v) {
        value = v;
        left = nullptr;
        right = nullptr;
    }
};


int height(TreeNode *root) {
    if (root == nullptr)
        return 0;
    int left = height(root->left);
    int right = height(root->right);

    return left > right ? left + 1 : right + 1;
}

int main() {
    unordered_map<int, TreeNode *> tree;

    int nodeCount;
    cin >> nodeCount;

    int a, b;
    while (cin >> a && cin >> b) {
        TreeNode *father;
        TreeNode *child;

        bool fatherFlag = tree.count(a);
        bool childFlag = tree.count(b);

        if (fatherFlag) {
            father = tree[a];
        } else {
            father = new TreeNode(a);
        }
        if (childFlag) {
            child = tree[b];
        } else {
            child = new TreeNode(b);
        }

        if (father->left == nullptr) {
            father->left = child;
        } else {
            father->right = child;
        }
        tree[a] = father;
        tree[b] = child;
    }
    cout << height(tree[1]);

    return 0;
}
```

## 💫构建树
```c
#include <iostream>
#include <unordered_map>

using namespace std;

class TreeNode {
public:
    int value;
    TreeNode *left;
    TreeNode *right;

    TreeNode(int v) {
        value = v;
        left = nullptr;
        right = nullptr;
    }
};

void preOrder(TreeNode *root) {
    if (root == nullptr)
        return;
    cout << root->value << " ";
    preOrder(root->left);
    preOrder(root->right);
}

void inOrder(TreeNode *root) {
    if (root == nullptr)
        return;
    inOrder(root->left);
    cout << root->value << " ";
    inOrder(root->right);
}

void postOrder(TreeNode *root) {
    if (root == nullptr)
        return;
    postOrder(root->left);
    postOrder(root->right);
    cout << root->value << " ";
}

int main() {
    unordered_map<int, TreeNode *> tree;

    int lineCount;
    cin >> lineCount;

    int a, b, c;
    while (cin >> a && cin >> b && cin >> c) {
        TreeNode *father;
        if (tree.count(a)) {
            father = tree[a];
        } else {
            father = new TreeNode(a);
        }

        TreeNode *lchild = nullptr;
        if (b != -1) {
            bool flag = tree.count(b);
            if (flag) {
                lchild = tree[b];
            } else {
                lchild = new TreeNode(b);
            }
        }

        TreeNode *rchild = nullptr;
        if (c != -1) {
            bool flag = tree.count(c);
            if (flag) {
                rchild = tree[c];
            } else {
                rchild = new TreeNode(c);
            }
        }

        father->left = lchild;
        father->right = rchild;

        tree[a] = father;
        tree[b] = lchild;
        tree[c] = rchild;
    }

    cout << "Preorder" << endl;
    preOrder(tree[0]);
    cout << endl;

    cout << "Inorder" << endl;
    inOrder(tree[0]);
    cout << endl;

    cout << "Postorder" << endl;
    postOrder(tree[0]);
}
```

```c
#include <iostream>
#include <vector>
#include <unordered_map>
#include <unordered_set>
using namespace std;

struct TreeNode {
    int left;
    int right;
    TreeNode(int l = -1, int r = -1) : left(l), right(r) {}
};

// 前序遍历
void preTraverse(const unordered_map<int, TreeNode>& tree, int root){
    if (root == -1) return;
    cout << " " << root;
    preTraverse(tree, tree.at(root).left);
    preTraverse(tree, tree.at(root).right);
}

// 中序遍历
void inTraverse(const unordered_map<int, TreeNode>& tree, int root){
    if (root == -1) return;
    inTraverse(tree, tree.at(root).left);
    cout << " " << root;
    inTraverse(tree, tree.at(root).right);
}

// 后序遍历
void postTraverse(const unordered_map<int, TreeNode>& tree, int root){
    if (root == -1) return;
    postTraverse(tree, tree.at(root).left);
    postTraverse(tree, tree.at(root).right);
    cout << " " << root;
}

int main(){
    int n;
    cin >> n;
    unordered_map<int, TreeNode> tree;
    unordered_set<int> children;

    for(int i = 0; i < n; ++i){
        int node, left, right;
        cin >> node >> left >> right;
        tree[node] = TreeNode(left, right);
        if(left != -1) children.insert(left);
        if(right != -1) children.insert(right);
    }

    // 确定根节点
    int root = -1;
    for(int i = 0; i < n; ++i){
        if(children.find(i) == children.end()){
            root = i;
            break;
        }
    }

    // 输出遍历结果
    cout << "Preorder";
    preTraverse(tree, root);
    cout << endl;

    cout << "Inorder";
    inTraverse(tree, root);
    cout << endl;

    cout << "Postorder";
    postTraverse(tree, root);
    cout << endl;

    return 0;
}
```


## 💫map遍历
```c
int main() {  
    unordered_map<int, int> tree;  
    tree[1] = 1;  
    tree[2] = 2;  
    tree[3] = 3;  
  
    for (auto pair: tree) {  
        cout << pair.first << ": " << pair.second << endl;  
    }  
}
```





