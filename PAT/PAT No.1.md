# PAT No.1

```c++
#include <cstdio>
#include <string>
#include <cstring>
#include <iostream>

using namespace std;

int main() {
    int a, b, i = 0, count = 1;
    cin >> a >> b;
    string res = to_string(a + b);
    int len = res.size();
    if (res[i] == '-') {
        printf("-");
        i++;
    }
    for (i; i < len; i++) {
        if((len - i) % 3 == 1 && i != len -1)
            printf("%c,", res[i]);
        else printf("%c", res[i]);
    }
    printf("\n");
    return 0;
}
```

**问题处理棘手在于输出结果要求按照英文数字格式，使用“ ，”。**

