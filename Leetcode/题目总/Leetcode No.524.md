# Leetcode No.524



方法一：就是求 s 的子序列。由于我们要对字典里多个字符串有进行子序列的判断

对于本解法 19 行出现[&] 属于Lambda表达式 
$$
sort(a, a + n, [](int a, int b) {return a > b});
$$
由于Lambda的类型是单一的，不能通过类型名来显式声明对应的对象，但可以利用auto关键字和类型推导：
$$
auto f=[](int a,int b){return a>b;};
$$
和其它语言的一个较明显的区别是Lambda和C++的类型系统结合使用，如

```c++
auto f = [x](int a,int b){return a > x;};//x被捕获复制
int x = 0, y = 1;
auto g = [&](int x){return ++y;};//y被捕获引用，调用g后会修改y，需要注意y的生存期
bool(*fp)(int, int)=[](int a,int b){return a > b;};//不捕获时才可转换为函数指针
```

[capture]：捕捉列表。捕捉列表总是出现在lambda表达式的开始处。事实上，[]是lambda引出符。编译器根据该引出符判断接下来的代码是否是lambda函数。捕捉列表能够捕捉上下文中的变量供lambda函数使用。

```c++
class Solution {
public:
    bool isSubsequence(string s, string t) {
        //判断 t 是否 s 的子序列
        int n = s.size(), m = t.size();
        //如果 t 长度大于 s，一定不是子序列
        if (m > n) return false;
        int i = 0;
        for (char ch : t) {
            while (i < n && s[i] != ch) i++;
            if (i >= n) return false;
            i++;
        }
        return true;
    }

    string findLongestWord(string s, vector<string>& dictionary) {
        // 更长的、字典序更小的排在前面，这样一旦找到，就是结果
        sort(dictionary.begin(), dictionary.end(), [&](string &a, string &b) {
            if (a.size() == b.size()) return a < b;
            return a.size() > b.size();
        });

        for (string &t : dictionary) {
            if (isSubsequence(s, t)) return t;
        }

        //如果没有找到
        return "";
    }
};
```

