# Hutool

## 1.引言

实际工作中，或多或少会写一些方便自己和团队的工作效率的工具类，只要他人简单看方法的注释就可以立即调用，这样的操作称之为轮子。言简意赅，工具类就是为了防止重复造轮子，减少劳动力消耗和提高工作效率，有益身心健康的21世纪伟大发明，当代程序员的巨人之光！！！

## 2.介绍

![image-20210509023622351](https://gitee.com/codeluojay/TyproaImage/raw/master/images/image-20210509023622351.png)

Hutool作者路大神，在百度工作过两年，经常要写工具类，写多了于是想整合多方资源来方便自己使用的工具类，然后开源出来，变成Hutool工具类。有兴趣的可以去B站UP主搬运的路大神早期在腾讯课堂的课程。

[【Hutool】2020年全网独一份作者亲授使用教程！github上Star10.9K的开源项目工具！_哔哩哔哩 (゜-゜)つロ 干杯~-bilibili](https://www.bilibili.com/video/BV1bQ4y1M7d9?zw)

## 3.安装和使用

在maven中引用hutool依赖

```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.6.3</version>
</dependency>
```

## 4.Convert类型转换

### 4.1.痛点

在Java开发中我们要面对各种各样的类型转换问题，尤其是从`Servlet`获取的用户参数、从早期学习`Servlet`的JavaWeb开发中经常需要从`HttpRequest`获取的`Parameter`，然后将请求参数转换为对应的类型使用等等，这些参数类型多种多样，我们怎么去转换他们呢？

常用的办法是先整成`String`，然后调用`XXX.parsexxx`方法，还要承受转换失败的风险，不得不加一层`try catch`来捕获可能出现的错误，这个小小的过程混迹在业务代码中会显得非常难看和臃肿。

### 4.2.Convert类

在`Hutool`中，我们只需要使用封装好的`Convert`类可以说是一个工具方法类，里面封装了针对`Java`常见类型的转换，用于简化类型转换。`Convert`类中大部分方法为`toxxx`，参数为`Object`，可以实现将任意可能的类型转换为指定类型。同时支持第二个参数`defaultvalue`用于在转换失败时返回一个默认值。

### 4.3.如何使用

下面暂时针对工作中我工作常用到的类型转换，进行一些的测试hutool方法的学习，所有的测试方法都写在`TestConvertExample`的测试类中，使用`junit4.1.2`版本的单元测试

1. 数字和字符串相互转化

   ```java
   	/**
        * 测试Integer转String
        */
       @Test
       public void testConvertIntegerToString()
       {
          // 以往的类型转换
           int number = 10;
           String strNumber = String.valueOf(number);
           System.out.println("strNumber:"+strNumber);
   
           // 利用hutool的Convert类来做类型转换
           String toStrNumber = Convert.toStr(number);
           System.out.println("toStrNumber:"+toStrNumber);
       }
   	/**
        * 测试String转Integer
        */
   	@Test
       public void testConvertStringToNumber()
       {
           // 以往的类型转换
   
           String strNumber = "中文123";
           // System.out.println("strNumber:"+Integer.valueOf(strNumber)); 类型转换异常
   
           // 利用hutool的Convert类来做类型转换
           Integer toNumber = Convert.toInt(strNumber);
           //	输出 null，不报异常
           System.out.println("toStrNumber:"+toNumber);
   
       }
   ```

   > 好处：代码简洁不少，简洁的关键点在于作者封装可能出现异常情况

2. 数组和字符串相互转化

   ```java
   	/**
        * 测试数组类型转换
        */
       @Test
       public void testConvertArrayToString()
       {
           // 以往转字符串[1,2,3,4,5]输出
           long[] longArray = {1,2,3,4,5};
           System.out.println(Arrays.toString(longArray));
   
           // 利用hutool的Convert类来做类型转换
           System.out.println(Convert.toStr(longArray));
       }
   ```

   > 好处：这个暂时没有很直观感受到好处，因为数组工具类Arrays也可以做到类似功能

3. 各种数组类型相互转化

   ```java
   
       /**
        * 测试数组类型转换各种类型数组
        */
       @Test
       public void testConvertArray()
       {
           // 以往转字符串[1,2,3,4,5]输出
           String[] stringArray = {"1","2","3","4","5"};
           Integer[] integerArray = new Integer[stringArray.length];
           for (int i = 0; i < stringArray.length; i++) {
               integerArray[i] = Integer.valueOf(stringArray[i]);
           }
           // 利用hutool的Convert类来做Int数组类型转换
           Integer[] toIntArray = Convert.toIntArray(stringArray);
           // 利用hutool的Convert类来做Long数组类型转换
           Long[] toLongArray = Convert.toLongArray(stringArray);
       }
   ```

   > 好处：
   >
   > 1. 内部封装直接返回对应类型的数组（不需要自己定义）
   > 2. 不需要自己写遍历赋值转换

4. 日期转换

   ```java
   	/**
        * 测试日期类型转换
        */
       @Test
       public void testConvertDate() throws ParseException {
   
           // 以往字符串转为日期
           String str="2012-12-12";
           Date parse = new SimpleDateFormat("yyyyy-MM-dd").parse(str);
           System.out.println("SimpleDateFormat parse: "+parse);
   
           //  hutool日期转换
           Date date = Convert.toDate(str);
           System.out.println("hutool convert date: "+date);
   
           String str1="2012/12/12";
           System.out.println("hutool convert date: "+Convert.toDate(str1));
       } 
   ```

   > 好处：
   >
   > 1. 不用自己写SimpleDateFormat和parse转换
   > 2. 支持自定义日期格式转换

5. 集合类型转换

   ```java
   	/**
        * 测试集合类型转换
        */
       @Test
       public void testConvertCollection() {
   
           // 字符串数组转集合
           String[] aaa = {"hello", "luojay", "bobby"};
           List<String> strings = Arrays.asList(aaa);
           List<String> objects = (List<String>) Convert.toList(aaa);
           // lambda表达式输出，其实还可以简化为方法引用 System.out::println
           objects.forEach(s -> System.out.println(s));
   
           // 定义基本类型数组转换
           int[] ccc={21,23};
           // List<Integer> ints = Arrays.asList(ccc);  int[] 无法转换成Integer
   
           //  hutool可以做到int[] 无法转换成Integer
           List<Integer> objects1 = (List<Integer>) Convert. toList(ccc);
           objects1. forEach(cc->System. out. println(cc));
   
       }
   ```

   > 好处：基本数据类型数组可以转换成包装类的集合，如果用Arrays.asList无法做到

## 5.IoUtil输入输出流工具类

### 5.1痛点

提到IO工具类的不免要说到`InputStream`、`OutputStream`、`Reader`、`Writer`流的定义，获取、读取，写出，关闭流操作等繁琐步骤，还要自己手动`try-catch`。

对于IO操作来说，使用频率最高（也是最容易被遗忘）的就是`close`操作，忘记关闭流会造成资源被占用，系统性能下降等问题。而关闭流操作会面临两个异常问题，导致程序出现问题。

1. 被关闭对象为空
2. 对象关闭失败（或对象已关闭）

`IoUtil.close`方法很好的解决了这两个问题。

### 5.2使用

Hutool文件拷贝中`IoUtil`的`copy`方法使用

```java
    /**
     * 测试文件拷贝
     */
    public static void testFileCopy() throws Exception {
        File inFile = new File("F:\\JavaLearingCode\\java-tool-	hutool\\src\\main\\resources\\testFile.txt");
        FileInputStream is = new FileInputStream(inFile);
        File outFile = new File("F:\\JavaLearingCode\\java-tool-hutool\\src\\main\\resources\\testFileCopy.txt");
        FileOutputStream os = new FileOutputStream(outFile);
        int len = 0;
        byte[] buffer = new byte[1024];
        while(true){
            //  不足1024就只读剩下,最后一次一定读不到内容才返回-1
            len = is.read(buffer);
            if(len == -1){
                break;
            }
            os.write(buffer,0,len);
        }
        is.close();
        os.close();
    }

 	/**
     * 使用hutool的文件拷贝
     * @throws Exception
     */
    public static void testFileCopyByIOUtils() throws Exception {
        File inFile = new File("F:\\JavaLearingCode\\java-tool-hutool\\src\\main\\resources\\testFile.txt");
        FileInputStream is = new FileInputStream(inFile);
        File outFile = new File("F:\\JavaLearingCode\\java-tool-hutool\\src\\main\\resources\\testFileCopyHutool.txt");
        FileOutputStream os = new FileOutputStream(outFile);
        IoUtil.copy(is,os,1024);
    }
```

>以往文件拷贝的思路：
>
>1. 需要定义输入流（读取文件）和输出流（写出文件），以字节的形式读取和写出
>2. 需要考虑读取多少字节流，一个文件的字节流大小通常未知，需要定义byte[]缓冲区分批读取
>3. 如何判断字节流读取完毕，当调用read()方法读取完毕后，读不到内容会返回-1
>4. 需要考虑读取多少次才能读完，一般用while循环读取多次，直到读取返回-1退出循环
>5. 写出输出流，以缓冲区的大小写出，每次都从换区的0处开始写，写出读取的缓冲区长度
>6. 流的关闭（输入流关闭，输出流关闭）
>
>使用hutool的IoUtil.copy就简化了2,3,4,5,6步操作

当然啦，以往文件拷贝的方法在工作中不会写的这么冗余，会以下面的形式写会好点

```java
	/**
     * 简化后测试文件拷贝
     */
    public static void testSimpleFileCopy() throws Exception {
        FileInputStream is = new FileInputStream(new File("F:\\JavaLearingCode\\java-tool-hutool\\src\\main\\resources\\testFile.txt"));
        FileOutputStream os = new FileOutputStream(new File("F:\\JavaLearingCode\\java-tool-hutool\\src\\main\\resources\\testSimpleFileCopy.txt"));

        int len;
        byte[] buffer = new byte[1024];
        while((len = is.read(buffer))!=-1){
            os.write(buffer,0,len);
        }
        is.close();
        os.close();
    }
```

