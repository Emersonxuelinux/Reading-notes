##==与equals()的区别
==和!=比较的是对象的引用

    public class Main {
        public static void main(String[] args) {
            Integer n1 = new Integer(47);
            Integer n2 = new Integer(47);
            System.out.println(n1 == n2);
            System.out.println(n1 != n2);
        }
    }
    输入结果：
    false
    true

equals()比较的是对象的值

    public class Main {
        public static void main(String[] args) {
            Integer n1 = new Integer(47);
            Integer n2 = new Integer(47);
            System.out.println(n1.equals(n2));
        }
    }
    输入结果：
    true