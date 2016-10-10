学习笔记

原文地址：http://blog.csdn.net/without0815/article/details/7697916#comments

##java程序员必知的8大排序

8种排序之间的关系:

![关系](http://oe53dpmqz.bkt.clouddn.com/20161010001.png)

###一、直接插入排序
####1、基本思想
　　在要排序的一组数中，假设前面(n-1)[n>=2] 个数已经是排好顺序的，现在要把第n个数插到前面的有序数中，使得这n个数也是排好顺序的。如此反复循环，直到全部排好顺序。
####2、实例
![实例](http://oe53dpmqz.bkt.clouddn.com/20161010002.png)
####3、用Java实现
```java
public class InsertSort {
    public InsertSort(){
        int a[]={49,38,65,97,76,13,27,49,78,34,12,64,5,4,62,99,98,54,56,17,18,23,34,15,35,25,53,51};
        int temp=0;
        Arrays.sort(a);
        for(int i=1;i<a.length;i++){
            int j=i-1;
            temp=a[i];
            for(;j>=0&&temp<a[j];j--){
                a[j+1]=a[j];                       //将大于temp的值整体后移一个单位
            }//执行完这个for循环之后j=-1
            a[j+1]=temp;
        }
        for(int i=0;i<a.length;i++)
            System.out.print(a[i]+" ");
    }
}
```

输出结果
```
4 5 12 13 15 17 18 23 25 27 34 34 35 38 49 49 51 53 54 56 62 64 65 76 78 97 98 99 
```
　　