### InputStream-OutputStream

```java
    OutputStream outputStream = null;
    InputStream inputStream = null;
    try {
        //输出到E:/one.txt
        outputStream = new FileOutputStream("e:/one.txt");
        //读入D:/one1.txt
        inputStream = new FileInputStream("d:/one1.txt");
        //读取输入流的数据，返回值为ASCII码，-1时表示没有内容了
        int lenIs = inputStream.read();

        while (lenIs != -1) {
            //将读到的值写入到输出流
            outputStream.write(lenIs);
            //继续下一次读入
            lenIs = inputStream.read();
        }
        System.out.println("传送完毕");
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            //关闭流
            if (outputStream != null) outputStream.close();
            if (inputStream != null) inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

需要追加路径后面写true

```java
Writer writer = new FileWriter("//",true);
```

