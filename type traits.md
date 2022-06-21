# type traits

以下是几种类型萃取的方式：

```c++
#include "iostream"
using namespace std;

// 1. 利用模板参数推导获取型别，这种方式不能够获取到返回值的型别

template<typename T>
void func_impl(T) {
    T a = 1;
    cout << a << endl;
}

template <typename T>
inline void func(T iter) {
    func_impl(*iter);
}

// 2. 利用 nested type + 模板参数推导 获取返回值型别，这种方式不能够支持原生指针

template <typename T>
struct Iter {
    using typeName = T;
    T* ptr;
    // 必须是class type才能够通过构造函数将数据储存到struct内部
    // 此时我们并不能够知道这个值的类型是什么
    Iter(T* p) {
        ptr = p;
    };
    // 重载 Iter 的 * 操作
    T& operator*() const {
        return *ptr;
    }
};

template <typename T>
// 必须使用typename
// 萃取内嵌型别到函数返回值
typename T::typeName
fn(T iter) {
    return *iter;
}

// 3. 通过 nested type 和模板偏特化来获取型别

template <typename T>
struct Iter_traits {
    // nested type
    using value_type = T;
};

// 特化的 T* 版本
template <typename T>
struct Iter_traits<T*> {
    using value_type = T;
};

// 特化的 const T* 版本
template <typename T>
struct Iter_traits<const T*> {
    using value_type = T;
};


template <typename T>
typename Iter_traits<T>::value_type
iter_fn(T iter) {
    return *iter;
}

int main () {
    // 1. 利用模板参数推导获取型别
    int a;
    func(&a);

    // 2. 利用 nested type + 模板参数推导 获取返回值类型
    // 需要借助 struct 或者 class
    Iter<char> iter1 (new char('A'));
    Iter<int> iter2 (new int(1));
    cout << fn(iter1) << " , " << fn(iter2) << endl;

    // 不支持下面的方式
    // int* b = &a;
    // cout << fn(b) << endl;

    // 3. 通过 nested type 和模板偏特化来获取型别
    int c = 0;
    int* d = &c;
    char e = 'A';
    const char* f = &e;
    cout << iter_fn(d) << endl;
    cout << iter_fn(f) << endl;

    return 0;
}
```
