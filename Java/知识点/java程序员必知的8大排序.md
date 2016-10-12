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
　　
### 二、希尔排序（最小增量排序）（未完成）
####1、基本思想
　　算法先将要排序的一组数按某个增量d（n/2,n为要排序数的个数）分成若干组，每组中记录的下标相差d.对每组中全部元素进行直接插入排序，然后再用一个较小的增量（d/2）对它进行分组，在每组中再进行直接插入排序。当增量减到1时，进行直接插入排序后，排序完成。
####2、实例
![实例](http://oe53dpmqz.bkt.clouddn.com/20161011001.png)
###3、用java实现
```java
public class shellSort {  
    public  shellSort(){  
        int a[]={1,54,6,3,78,34,12,45,56,100};  
        double d1=a.length;  
        int temp=0;  
        while(true){  
            d1= Math.ceil(d1/2);  //大于等于i的最小整数 double类型
            int d=(int) d1;  
            for(int x=0;x<d;x++){  
                for(int i=x+d;i<a.length;i+=d){  
                    int j=i-d;  
                    temp=a[i];  
                    for(;j>=0&&temp<a[j];j-=d){  
                    a[j+d]=a[j];  
                    }  
                    a[j+d]=temp;  
                }  
            }  
            if(d==1)  
                break;  
        }  
        for(int i=0;i<a.length;i++)  
            System.out.println(a[i]);  
    }  
}  
```

java.lang.Math.ceil(double a) 返回最小的（最接近负无穷大）double值，大于或相等于参数，并相等于一个整数。

输出结果：
```
1 3 6 12 34 45 54 56 78 100
```

### 三、简单选择排序
####1、基本思路
　　在要排序的一组数中，选出最小的一个数与第一个位置的数交换；然后在剩下的数当中再找最小的与第二个位置的数交换，如此循环到倒数第二个数和最后一个数比较为止。

####2、实例：
![实例](http://oe53dpmqz.bkt.clouddn.com/20161011002.png)

####3、用java实现
```java
public class SelectSort {
    public SelectSort(){
        int a[]={1,54,6,3,78,34,12,45};
        int position=0;
            for(int i=0;i<a.length;i++){
            int j=i+1;
            position=i;
            int temp=a[i];
            for(;j<a.length;j++){//每次循环都获取数组中最小值，并将最小值保存在temp
                if(a[j]<temp){
                    temp=a[j];
                    position=j;
                }
            }
            a[position]=a[i];
            a[i]=temp;
        }
        for(int i=0;i<a.length;i++)
            System.out.print(a[i]+" ");
    }
}
```

输出结果：
```
1 3 6 12 34 45 54 78 
```

每次执行完for循环i的值，temp的值，a数组的值
```
初始{1,54,6,3,78,34,12,45}
i=0，1 {1,54,6,3,78,34,12,45}
i=1，3 {1,3,6,54,78,34,12,45}
i=2，6 {1,3,6,54,78,34,12,45}
i=3，12 {1,3,6,12,78,34,54,45}
i=4，34 {1,3,6,12,34,78,54,45}
i=5，45 {1,3,6,12,34,45,54,78}
i=6，54 {1,3,6,12,34,45,54,78}
i=7，78 {1,3,6,12,34,45,54,78}
```



