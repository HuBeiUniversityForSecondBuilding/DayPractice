# **1.饿汉式**

"因为饿，所以要立即吃饭，刻不容缓"

```java
public class Singleton{ 
    private staic final Singleton singleton = new Singleton();  
    
    public singletion(){  
        
    }       
    public static Singleton getInstance(){  
        return singleton;   
    }    
}
```

好处：线程安全，获取实例速度快。

缺点：类加载即初始化实例，浪费内存



# **2.懒汉式**

“因为懒，等非用不可的时候才会去创建，延迟加载”

```java
public class Singleton{  
    private static Singleton singleton = null;   
    public singleton(){    
    }      
    public static Singleton getInstance(){   
        if(singleton == null){  
            singleton = new Singleton();  
        }      
        return singleton;
    } 
}
```

优点：节约资源，延迟加载

缺点：不是线程安全，若多个线程同时访问getInstance()，可能会产生多个实例；每次获取实例时都会进行非空检查，开销大；获取实例时初始化工作较多，加载速度变慢，影响整个系统性能。



## **线程安全改造：**

```java
public class Singleton{  
    private static Singleton singleton = null;   
    public singleton(){       
    }     
    public synchronized static Singleton getInstance(){ 
        if(singleton == null){   
            singleton = new Singleton();    
        }      
        return singleton;  
    } 
}
```

优点：线程安全

缺点：每次获取实例都需要加锁，耗费资源。

## **double checklock改造：**

```java
public class Singleton{   
    //注意volatile关键字的作用，为什么要加？不加会有什么后果？
    private volatile static Singleton singleton = null;     
    public singleton(){       
    }      
    public static Singleton getInstance(){   
        if(singleton == null){    
            synchronized(Singleton.class){      
                if(singleton == null){       
                    singleton = new singleton();    
                }          
            }      
        }      
        return singleton;   
    } 
}
```

### <span style="color:red">**Q&A:**</span>

####  **<span style="color:blue"> a.为什么懒汉模式中要用两个if（）判断对象是否为空？</span>**

​ 第一个if判断，大家应该可以理解。关键是第二个if判断，大家会想第一个if不是已经判断出对象是否为空了吗？ 
现在我拿上面写的例子来解释一下，当x事件和y事件同时调用SingletonClass的对象时，
如果此时instance为null，那么x 和 y 都会经过第一个if判断，到达同步锁那里。其中一个（假设 x）进入到里面以后，y在锁（synchronized（））外等候，
x从锁里出来后，instance已经不为null，这时y进入锁后，如果没有if判断instance是否为空，那么instance就会又执行了一次new操作，
这样x事件和y事件调用的就不是同一个对象了，这样就失去了单例模式的意义，所以要进行两次if判断。
       

####   **<span style="color:blue">b.synchronized作用？</span>**

​				**作用：当它用来修饰一个方法或者一个代码块的时候，能够保证在同一时刻最多只有一个线程执行该段代码。synchronized同步块括号中的锁定对象是采用的一个无关的Object类实例，而不是采用this，因为getInstance是一个静态方法，在它内部不能使用未静态的或者未实例的类对象**

​			**注意：在用synchronized关键字的时候，能缩小代码段的范围就尽量缩小，能在代码段上加同步就不要再整个方法上加同步。这样可以使代码更大程度的并发，否则，锁的代码段太长了，别的线程要等很久，会影响应用的性能。**

# **3.静态内部类实现模式**

```java
public class Singleton{  
    public Singleton(){ 
    }      
    public static class InnerSingleton{  
        private static final Singleton singleton = new Singleton();   
    }       
    public static Singleton getInstance(){ 
        return InnerSingleton.singleton; 
    } 
}
```

优点：避免了同步带来的性能损耗，又能延迟加载。

缺点：不能防止反射，序列化

# **4.枚举类**

```java
public enum  Singleton{   
    INSTANCE;       
    public void singletonOperation(){  
        System.out.println("自己定义的操作");  
    } 
}
```

​	优点：天然防反射和序列化
