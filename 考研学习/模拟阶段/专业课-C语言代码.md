# ♾️注意事项
- 字符串末尾的'\0'不要忘了!

# ♾️文件处理
## 💫打开文件
```c
```c
	FILE *fp = fopen("1.txt", "r");
	if (fp == NULL) {
		perror("file open error");
		exit(1);
	}
	fclose(fp);
```


## 💫字符读取
```c
	char buf;
	while(fscanf(fp, "%c%, &buf) > 0) {
		printf("%c", buf);
	}
```


## 💫例题

> 按字符读取
> 10-1: 显示指定的文本文件中的内容, 要求每行中显示的字符数不超过30个字符
```c
#include <stdio.h>

int main(){
	FILE *fp = fopen("1.txt", "r");
	if (fp == NULL) {
		perror("file open error");
		exit(1);
	}
	char buf;
	int count = 0;
	
	while(fscanf(fp, "%c%, &buf) > 0) {
		count++;
		printf("%c", buf);
		if(buf == '\n') {
			count = 0;
			continue;
		}
		if (count == 30) {
			printf("\n");
			count = 0;
		}
	}
	
	fclose(fp);
}
```

> 
> 13-3
> - 字符串读取
> - 读文件
> - 写文件
> - 字符串比较, 排序
> 
> ![[Pasted image 20241124160521.png|650]]

```c
#include <stdio.h>
#include <string.h>

#define ROW_MAX 50;
#define COLUMN_MAX 50;

int main() {
	char dataList[ROW_MAX][COLUMN_MAX];
	int dataLen = 0;
	readData(dataList, &dataLen);
	sortData(dataList, dataLen);
	writeData(dataList, dataLen);
}

void readData(char dataList[ROW_MAX][COLUMN_MAX], int *dataLen) {
	FILE *fp = fopen("file1.txt", "r");
	if (p != NULL) {
		while((fscanf(fp, "%s", dataList[*dataLen])) > 0) {
			*dataLen+=1;
		}
	}
	fclose(fp);
}

// 冒泡排序
void sortData(char dataList[][COLUMN_MAX], int dataLen) {
	for(int i = 0; i < dataLen; i++) {
		char temp[COLUMN_MAx];
		for(int j = 0; j < dataLen-1-i; j++) {
			if(strcmp(dataList[j], dataList[j+1]) > 0) {
				strcpy(temp, dataLit[j]);
				strcpy(dataList[j], dataList[j+1]);
				strcpy(dataList[j+1], temp);
			}
		}
	}
}

void writeData(char dataList[][COLUMN_MAX], int dataLen) {
	FILE *fp = fopen("file2.txt", "w");
	if (fp != NULL) {
		for(int i = 0; i < dataLen; i++){
			fprintf(fp, "%s ", dataList[i]);
		}
	}
	fclose(fp);
}
```




# ♾️字符串处理
## 💫实现strlen
```c
int strLen(char *str) {
	char *p = str;
	int strLen = 0;
	while(*p != '\0') {
		strLen++;
		p++;
	}
	return strLen;
}
```
10-2, 讲义24,25

## 💫实现strcmp
```c
int strcmp(char *s1, char *s2) {
	int d = 0;
	while(*s1 != '\0' && *s2 != '\0') {
		d = *s1 - *s2;
		if(d != 0)
			break;
		s1++;
		s2++;
	}
	return *s1 - *s2;
}
```




## 💫指针遍历字符串
```c
int findIndex(char *str, char c) {
	int i = 0;
	char *p = str;
	while(*p) {
		if(*p == c) {
			return i;
		}
		i++;
		p++;
	}
	return -1;
}
```

## 💫倒序遍历字符串

```c
int printUpper(char *str) {
	// 将p指针移到字符串的最后
	char *p = str;
	while((*p) != '\0') p++;
	
	// 倒叙遍历
	while(p >= str) {
		if(*p >= 'A' && *p <= 'Z')
			printf("%c", *p);
			
		p--;
	}
}
```


# ♾️其他
## 💫int最值
```c
#include <limits.h>

INT_MAX;
INT_MIN;
```

## 💫冒泡排序
```c
// 排序
void sortArr(int arr[], int length) {
	for(int i = 0; i < length - 1; i++) {
		for(int j = 0; j < length - i - 1; j++) {
			if(arr[j] > arr[j+1]) {
				int temp = arr[j];
				arr[j] = arr[j+1];
				arr[j+1] = temp;
			}
		}
	}
}
```

## 💫malloc
```c
LNode *node = (LNode*)malloc(sizeof(LNode));
```

## 💫将数字的每位存入int[]
```c
void restore(int arr[], int num, int *len) {
	while(num > 0) {
		arr[*len] = num % 10;
		*len++;
		num /= 10;
	}
}
```




# ♾️常用函数
## 💫math.h
- 根号: sqrt(int)

## 💫string.h
- strcmp(s1, s2): s1 > s2返回1
- strcpy(temp, s): 将s复制到temp


# ♾️讲义
## 💫4: 最小公倍数
> - 最小公倍数 = 最大公倍数 / 最小公约数
> - 最大公倍数 = n1 x n2
> - 最小公约数: 辗转相除法

```c
// 进入函数前, 先保证n1 > n2
int getLowestComMul(int n1, int n2) {
	int maxComMul = n1 * n2;
	
	int remain = 1;
	while(remain != 0) {
		remain = n1 % n2;
		n1 = n2;
		n2 = remain;
	}
	
	return maxComMul / n1;
}
```

> 辗转相除法: 令(n1, n2)
> 例如求36和30的最大公因数
> (36, 30) -> (30, 6) -> (6, 0) === 6

## 💫6: 斐波拉契数列
```c
void fn(int a, int b, int n) {
	if(n <= 0) return;
	
	printf("%d ", a+b);
	fn(b, a+b, n-1);
}

void solution(int n) {
	if(n == 1)
		printf("%d ", 1);
	else
		printf("%d %d ", 1, 1);
		
	fn(1, 1, n -1)
}
```

## 💫7: 空格分隔单词
![[Pasted image 20241125162953.png]]

```c
int solution(char *string) {
	int count = 0;
	char *pre = string;
	char *tail = string;
	
	while(tail != '\0') {
		// tail先找到一个非空格的字符
		while(*tail != '\0' && *tail == ' ')
			tail++;
		pre = tail;
		
		// tail找到空格, 代表单词结尾
		while(*tail != '\0' && *tail != ' ')
			tail++;
		
		// 特殊情况, pre == tail == '\0'
		if(pre == tail)
			continue;
		// 排除冠词情况
		if(tail - pre == 1) {
			if(*pre == 'a' || *pre == 'A')
				continue;
		}
		
		count++;
	}
	return count;
}
```

## 💫20: static使用

![[Pasted image 20241125170145.png]]

```c
int process(int a, int b) {
	static int times = 0;
	times++;
	
	...
}
```


## 💫29
![[Pasted image 20241126151211.png]]
![[Pasted image 20241126151240.png]]

## 💫32: 综合题


# ♾️真题
## 💫14-2
> 反序输出, 可以从数组末端开始遍历
![[Pasted image 20241124161046.png]]
```c
#include <stdio.h>

int main() {
	char str[MAX_LEN];
	int big = 0;
	int small = 0;
	gets(str);
	count(str, &big, &small);
	printUpper(str);
}

int count(char *str, int *bigNumber, int *smallNumber) {
	char *p = str;
	while(p != '\0') {
		if(*p >= 'A' && *p <= 'Z')
			(*bigNumber)++;
		if(*p >= 'a' && *p <= 'z')
			(*smallNumber)++;
		p++;
	}
}

int printUpper(char *str) {
	// 将p指针移到字符串的最后
	char *p = str;
	while((*p) != '\0') p++;
	
	// 倒叙遍历
	while(p >= str) {
		if(*p >= 'A' && *p <= 'Z')
			printf("%c", *p);
			
		p--;
	}
}
```

## 💫15-2
> switch的巧用

![[Pasted image 20241124161301.png]]


```c
int solution(Data data) {
	int sum = 0;
	switch(data.month - 1) {
		case 11: num += 30;
		case 10: num += 31;
		case 9: num += 30;
		case 8: num += 31;
		case 7: num += 31;
		case 6: num += 30;
		case 5: num += 31;
		case 4: num += 30;
		case 3: num += 31;
		case 2: num += 28;
		case 1: num += 31;
	}
	return sum;
}
```

## 💫16-1
![[Pasted image 20241125102840.png]]
```c
int calculation(int n) {
	if(n == 0) {
		return 0;
	} else {
		int begin = n * (n-1) / 2 + 1;
		int tempSum = 1;
		for(int i = 0; i < n; i++) {
			tempSum *= begin;
			begin++;
		}
		
		return tempSum + calculation(n-1);
	}
}

int main() {
	int n;
	scanf("%d", &n);
	printf("n: %d\n", n);
	printf("result: %d", calculation(n));
}
```

## 💫16-2(综合题)
> 计算每个选手的平均分(求所有分数的和, 并记录最大值和最小值, 最后减去)
![[Pasted image 20241125103119.png]]

```c
int main() {
	for(int i = 1; i <= 20; i++) {
		// 记录最大值和最小值
		int max = 1;
		int min = 10;
		
		double sum = 0;
		double avg = 0;
		for(int j = 0; j<8; j++) {
			int grade;
			scanf("%d", &grade)
			
			sum += grade;
			
			if(grade > max) {
				max = grade;
			}
			if(grade < min) {
				min = grade;
			}
		}
		sum = sum -min - max; // 减去最高分, 最低分
		avg = sum / 6; 
	}
}
```

> 完整代码

```c

double top3[][] = {{0.0, 0.0},{0.0, 0.0},{0.0, 0.0}}

void isTop3(int code, double grade) {
	for(int i = 0; i < 3; i++) {
		if(grade > top3[i][1]) {
			top3[i][0] = code;
			top3[i][1] = grade;
			return;
		}
	}
}

void printTop3() {
	for(int i = 0; i < 3; i++) {
		// 注意%d打印double数据时, 一定要强转!
		printf("编号: %d, 分数: %f\n", (int)top3[i][0], top3[i][1]);
	}
}

int main() {
	for(double i = 1; i <= 20; i++) {
		// 记录最大值和最小值
		int max = 1;
		int min = 10;
		
		double sum = 0;
		double avg = 0;
		for(int j = 0; j<8; j++) {
			int grade;
			scanf("%d", &grade)
			
			sum += grade;
			
			if(grade > max) {
				max = grade;
			}
			if(grade < min) {
				min = grade;
			}
		}
		sum = sum -min - max; // 减去最高分, 最低分
		avg = sum / 6; 
		
		isTop3(i, avg);
	}
	
	// 如果题目要求前三名也要排序, 那就加个函数将top3数组排序就行了
	// sortTop3();
	printTop3();
}

```

## 💫17-2
![[Pasted image 20241125103939.png]]

```c
int arr[200] = {0};
int len = 0;

int getArr(int n) {
	while( (n/10) != 0) {
		int temp = n % 10;
		arr[len] = temp;
		len++;
		
		n = n/10;
	}
}

int isHuiwen() {
	int pre = 0, tail = len - 1;
	while(pre < tail) {
		if(arr[pre] != arr[tail]) {
			return 0;
		}
		pre++;
		tail--;
	}
	return 1;
}

int isWanshu(int n) {
	int sum = 0;
	for(int i = 1; i < n; i++) {
		if( n % i == 0)
			sum += i;
	}
	return sum == n ? 1 : 0;
}

int main() {
	int n;
	scanf("%d", &n);
	getArr(n);
	int huiwenFlag = isHuiwen();
	int wanshuFlag = isWanshu(n);
	
	if(huiwenFlag && wanshuFlag) {
		printf("是回文完数!");
	} else {
		printf("不是回文完数!");
	}
}
```

## 💫18-1
![[Pasted image 20241125104236.png]]

```c
#include <stdio.h>  
  
int result[6][3];  
int resultLen[6] = {0};  
  
void insertResult(int n) {  
    int flag = n % 2;  
    if (flag == 0) {  
        for (int i = 1; i <= 5; i += 2) {  
            int row;  
            if (resultLen[i] != 3) {  
                row = i;  
                result[i][resultLen[i]] = n;  
                resultLen[i]++;  
                break;  
            }  
        }  
    } else {  
        for (int i = 0; i <= 4; i += 2) {  
            int row;  
            if (resultLen[i] != 3) {  
                row = i;  
                result[i][resultLen[i]] = n;  
                resultLen[i]++;  
                break;  
            }  
        }  
    }  
}  
  
void printResult() {  
    for (int i = 0; i < 6; i++) {  
        printf("%d: ", i + 1);  
        int len = resultLen[i];  
        if (len > 0) {  
            for (int j = 0; j < len; ++j) {  
                printf("%d ", result[i][j]);  
            }  
        }  
        printf("\n");  
    }  
}  
  
void solution(int arr[3][3]) {  
    for (int i = 0; i < 3; i++) {  
        for (int j = 0; j < 3; j++) {  
            insertResult(arr[i][j]);  
        }  
    }  
}  
  
int main() {  
    int arr[3][3] = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};  
    solution(arr);  
    printResult();  
}
```

## 💫18-2(字符串处理)
![[Pasted image 20241125104420.png]]

```c
int solution(char *string) {
	char maxText[3000];
	char max = 0;
	char minText[3000];
	int min = 3000;
	char *pre = string;
	char *tail = string;
	
	while(tail != '#') {
		// 先让tail找到一个非空格的字符
		while(tail != '#' && tail == ' ') {
			tail++;
		}
		pre = tail;
		// 找到下一个空格
		while(tail != '#' && tail != ' ') {
			tail++;
		}
		// 比较长度
		if(tail - pre > max) {
			max = tail - pre;
			copy(maxText);
		}
				// 比较长度
		if(tail - pre < min) {
			min = tail - pre;
			copy(minText);
		}
	}
	
	printf("min: %s \n max: %s", minText, maxText);
}
```

## 💫18-3(综合题)
![[Pasted image 20241125104705.png]]

```c
typedef struct Salary {
	char id[10];
	char name[20];
	float wage;
	struct Salary *next;
}Salary;

void printOneSalary(Salary *salary) {
	printf("id: %s, name: %s, wage: %f\n", salary->id, salary->name, salary->wage);
}

// 打印结点, 顺便把员工人数返回
int printAllSalary(Salary *salary) {
	int len = 0;
	Salary *p = salary
	while(p != NULL) {
		len++;
		printOneSalary(p);
		p = p->next;
	}
	return len;
}

void insertOne(Salary *head, Salary *salary) {
	Salary *p = head;
	while(p -> next != NULL && p->next->data < salary->data) {
		p = p->next;
	}
	salary->next = p->next;
	p->next= salary;
}

Salary* sortSalary(Salary *salary) {
	Salary *head = (Salary *)malloc(sizeof(Salary));
	Salary *p = salary;
	while( p != NULL) {
		insertOne(head, p);
		p = p->next;
	}
	return head->next;
}

int getMiddle(Salary *salary, int len) {
	Salary *p = salary
	for(int i = 1; i < (len/2); i++) {
		p = p->next;
	}
	if(len % 2 == 0) {
		return (p->data + p->next->data) / 2;
	} else {
		return p->data;
	}
}

int solution(Salary *salary) {
	// 打印所有员工的工资信息
	int len = printALlSalary(Salary *salary);
	// 获取中位数
	Salary *sorted = sortSalary(salary);
	int middle = getMiddle(sorted, len);
}
```


## 💫19-3(综合题)
![[Pasted image 20241125104949.png]]

```c
typedef struct Consume {
	int id;
	unsigned pid;
	char p[100];
	float cost;
}Consume;

// 根据pid将record进行排序
void sortRecords(struct Consume record[N]) {
	for(int i = 0; i < N-1; i++) {
		for(int j = 0; j < N - 1 - i; j++) {
			if(record[j].pid > record[j+1].pid) {
				struct temp = record[j];
				record[j] = record[j+1];
				record[j+1] = temp;
			}
		}
	}
}

void maxCost(struct Consume record[N]) {
	sortRecords(record);
	
	int maxPid = 0;
	char maxName[100];
	float maxSum = 0;
	
	int curPid = -1;
	char curName[100];
	float curSum = 0;
	for(int i = 0; i<N; i++) {
		if(record[i].pid != curPid) {
			curPid = record[i].pid;
			curName = record[i].p;
			curSum = record[i].cost;
		} else {
			curSum += record[i].cost;
			if(curSum > maxSum) {
				maxPid = record[i].pid;
				maxCurName = record[i].p;
				maxSum = curSum;
			}
		}
	}
	
	printf();
}
```


## 💫20-3(综合题)
![[Pasted image 20241125105222.png]]


