# 四、ArrayList源码重点
## 1、ArrayList 实现的接口
* 继承自AbstractList<E>,实现了List<E>接口，实现添加、删除、修改、遍历等功能。
* 实现RandomAccess接口，实现了随机访问功能。
* 实现了Serializable接口，提供了序列化反序列化功能。
* 实现了Cloneable接口，可以实现克隆功能
## 2、扩容核心方法
* 在add（），addAll（）方法中去判断是否需要扩容，即添加元素之后数组的大小是否大于目前的总容量。
* 如果需要扩容，则数组大小变为原来的1.5倍。这里用到了一个右移位运算符>>,向右移位表示数字除以2，这种位运算效率大于除法的运算效率。如果还不满足要求，则扩展至要求的大小。
## 3、核心API方法
* grow（）扩容、size（）元素数、contains（）包含、indexOf（o）索引位置、lastIndexOf（o）最后匹配的索引、clone（）进行浅拷贝。
* 浅拷贝中对象的引用类型数据只复制引用地址，不开辟新的内存空间。深拷贝中对象的引用类型数据会开辟新的内存空间。
* toArray(),返回一个新数组。get（）、set（）、add（）、addAll（）、remove（）、removeRange（）、removeAll（）、retainAll（）、clear（）。
## 4、关于length和size
* length属性是数组的，求数组长度
* length（）方法是字符串的，求字符串长度
* size（）方法是List的，获取集合元素个数。
