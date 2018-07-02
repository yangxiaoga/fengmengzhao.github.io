---
layout: post
title: transient
subtitle: transient关键字，当你序列化的时候是否会想起她
background: '/img/posts/transient-20180702.jpg'
comment: true
---

## 目录

- [1. **简介**](#1)
- [2. **如何使用**](#2)

<h3 id="1">1. 简介</h3>

Java语言的关键字`变量修饰符`  
如果用`transient`声明一个实例变量，当对象存储时，它的值不需要维持，也就是用transient标记的成员变量不参与序列化过程。   
Java中对象的序列化指的是将对象转换成以字节序列的形式来表示，这些字节序列包含了对象的数据和信息，一个序列化的对象可以被写到数据库或文件中，也可用于网络传输。  
一般我们使用缓存（内存空间不够有可能会本地存储到硬盘）或远程RPC(网络传输)的时候，经常需要让我们实体类实现`Serializable`接口,目的是为了让其可序列化。

---

<h3 id="2">2. 如何使用</h3>

Java的Serialization提供了一种持久化对象实例的机制。  
当持久化对象时，可能有一个特殊的对象数据成员如：`密码`,`银行卡号`等，我们不想用`serialization`机制来保护它，
为了在一个特定对象的一个域上关闭序列化，可以用`serialization`来标记 。 

注意： transient关键字只能修饰变量，不能修饰方法和类，被transient关键字修饰的变量不再被序列化，一个静态变量不管是否被trnsient修饰，均不能被序列化 。 

	具体使用：
	package com.wanghang.one;
	import java.io.Serializable;
 
	/**
	* 序列化与非序列化
	* @author Jak
	* @version 1.0, 2018-07-01 01:30:12
	*/
	class Person implements Serializable {
	
	    // UID
	    private static final long serialVersionUID = 42L;
	    // 用transient修饰后name将不会进行序列化
	    private transient String name;
	    public int age;
	    Person(String name, int age) {
		    this.name = name;
		    this.age = age;
	    }
	    @Override
	    public String toString() {
		    return "Person [name=" + name + ", age=" + age + "]";
	    }
    }    

利用ObjectInputStream 和ObjectOutputStream对对象进行读写
保存对象到文件中（参数obj一定要实现Serializable接口）：

	public static void writeObjectToFile(Object obj)
    {
        File file =new File("test.dat");
        FileOutputStream out;
        try {
            out = new FileOutputStream(file);
            ObjectOutputStream objOut=new ObjectOutputStream(out);
            objOut.writeObject(obj);
            objOut.flush();
            objOut.close();
            System.out.println("write object success!");
        } catch (IOException e) {
            System.out.println("write object failed");
            e.printStackTrace();
        }
    }    

从文件中读取对象

	public static Object readObjectFromFile()
    {
        Object temp=null;
        File file =new File("test.dat");
        FileInputStream in;
        try {
            in = new FileInputStream(file);
            ObjectInputStream objIn=new ObjectInputStream(in);
            temp=objIn.readObject();
            objIn.close();
            System.out.println("read object success!");
        } catch (IOException e) {
            System.out.println("read object failed");
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return temp;
    }

---

**Reference**

- [transient解析](https://stackoverflow.com/questions/5245600/what-does-the-keyword-transient-mean-in-java)
