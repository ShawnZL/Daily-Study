# Leetcode No.295

###### [295. 数据流的中位数](https://leetcode-cn.com/problems/find-median-from-data-stream/)

使用两个优先队列queMax和queMin分别记录大于中位数的数字和小于中位数的数字，保证queMin的数字比queMax多一个，中位数就是queMin.top()，

num≤max{queMin}

此时num 小于等于中位数，我们需要将该数添加到 queMin 中。新的中位数将小于等于原来的中位数，因此我们可能需要将queMin 中最大的数移动到 queMax 中。

num>max{queMin}

此时 num 大于中位数，我们需要将该数添加到queMin 中。新的中位数将大于等于原来的中位数，因此我们可能需要将queMax 中最小的数移动到 queMin 中。

```c++
class MedianFinder {
public:
    //大根堆
    priority_queue<int, vector<int>, less<int>> queMin;
    //小根堆
    priority_queue<int, vector<int>, greater<int>> queMax;
    MedianFinder() {}
    
    void addNum(int num) {
        if (queMin.empty() || num <= queMin.top()) {
            queMin.push(num);
            if (queMax.size() + 1 < queMin.size()) {
                queMax.push(queMin.top());
                queMin.pop();
            }
        }
        else {
            queMax.push(num);
            if (queMax.size() > queMin.size()) {
                queMin.push(queMax.top());
                queMax.pop();
            }
        }
    }
    
    double findMedian() {
        if (queMin.size() > queMax.size()) 
            return queMin.top();
        return (queMin.top() + queMax.top()) / 2.0;
    }
};

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder* obj = new MedianFinder();
 * obj->addNum(num);
 * double param_2 = obj->findMedian();
 */
```

