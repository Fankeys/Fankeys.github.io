# javaSE之IO

![06](../javase-image/06.png)

## File类的使用

~~~java
        //创建一个文件对象，路径为"d:/test/Demo.txt"
        File f1 = new File("d:/test/Demo.txt");
        if(!f1.exists()){//判断文件是否存在
            try{
                //文件不存在，使用createNewFile()创建文件
                f1.createNewFile();
                System.out.println("文件创建成功");
            } catch (IOException e){
                e.printStackTrace();
            }

        }
        //判断是否为文件
        System.out.println(f1.isFile());
        //判断是否为文件夹
        System.out.println(f1.isDirectory());

        File f2 = new File("d:/test");
        //删除文件返回的值为boolean值
        //如果删除文件夹，文件夹里有文件，那就会删除失败
        System.out.println(f2.delete());

        //返回当前目录下的所有文件名
        String[] names = f2.list();
        System.out.println(Arrays.toString(names));
        //列出当前目录下所有文件，以file对象返回
        File[] fs=f2.listFiles();
        for (File f:fs) {
            System.out.println(f.getName());//文件名
            System.out.println(f.length());//文件大小
            System.out.println(f.getPath());//相对路径
            System.out.println(f.getAbsolutePath());//绝对路径
            System.out.println(f.isHidden());//是否为隐藏文件
            Date date = new Date(f.lastModified());
            DateFormat df = new SimpleDateFormat("HH:mm:ss");
            System.out.println(df.format(date));//文件最后一次修改时间
        }

        File f3 = new File("temp.txt");//相对路径,对于这个项目而言的路径
        File f4 =new File("d:/test/test1");
        //一次性创建一连串文件夹
        //f4.mkdirs();
        //一次性创建一个文件夹
        f4.mkdir();
        //重命名与移动文件
        f4.renameTo(new File("d:/test/test2"));

        File f5 = new File("d:/test/test3");
        //FileFilter() 用于自定义找出改目录下你选择的文件，过滤器
        File[] files = f5.listFiles(new FileFilter() {
            @Override
            public boolean accept(File pathname) {
                return pathname.getName().endsWith(".txt");
            }
        });
        for (File file:files) {
            System.out.println(file.getName());
        }

        //也可以用lambda表达式：
        File[] files1 = f5.listFiles((pathname)->pathname.getName().endsWith(".xls"));
        for (File file:files1) {
            System.out.println(file.getName());
        }




/**
     * 该方法用于查找目标目录下所有指定类型的文件
     * @param target ：目标目录
     * @param end   ：查找的类型
     */
    static void findFile(File target,String end){
        if(target == null)return;
        if(target.isDirectory()){
            File[] files = target.listFiles();
            if(files != null){
                for (File file:files) {
                    findFile(file,end);
                }
            }
        }else{
            String name = target.getName().toLowerCase();
            if(name.endsWith(end)){
                System.out.println(target.getAbsolutePath());
            }
        }
    }
~~~



![01](../javase-image/01.jpg)
![02](../javase-image/02.png)



## 字节字符输入输出流

### 字节

~~~java
/**字节流
 * 输入输出超类：OutputStream、IntputStream
 * 对文件输入输出使用：FileOutputStream、FileIntputStream
 * 输入输入流操作原理，每次只会操作一个字节
 * 字节操作流，默认每次执行写入操作会直接把数据写入文件
 * 只有操作文本文件才使用字符流
 */
public class WWW_Demo1 {
    public static void out()  {
        //确定目标文件
        File file= new File("D:\\test\\test4\\test.txt");

        try{
            //构建一个文件输出对象
            OutputStream out = new FileOutputStream(file,false);//append为true表示追加内容，默认为覆盖
            //输出内容
            String str = "小明在学校";
            //将内容以字节数组的方式写入文件
            out.write(str.getBytes());
            //关闭流
            out.close();
            System.out.println("写入成功");
        }catch (FileNotFoundException e){
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void in(){
        //确定目标文件
        File file= new File("D:\\test\\test4\\test.txt");

        try{
            //构建一个文件输入对象
            InputStream in = new FileInputStream(file);
            //数组用于每次处理的字节长度（1024为最大长度）
            byte[] bytes = new byte[20];
            StringBuilder buf = new StringBuilder();
            int len = -1;//表示每次被读取的字节长度
            //把数据读取到数组中，并返回读取的字节数，表示读取到数据，等于-1表示文件已经去读取完毕
            while((len = in.read(bytes))!=-1){
                //根据读取到的字节数组，转换为字符串,len用来去除每次录入的多余的或者空的字节
                buf.append(new String(bytes,0,len));
            }
            System.out.println(buf);
            //关闭流
            in.close();

        }catch (FileNotFoundException e){
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        out();
        in();
    }
}
~~~

### 字符

~~~java 
/**
 * 字符流
 * 字符的输入输出超类：Writer,Reader
 * 字符的对文件输入输出使用：FileWriter、FileReader
 * 每次操作的单位是一个字符
 * 文件字符操作流自带缓存，默认大小为1024字节，在缓存满后、或者刷新、关闭流是会把数据写入文件
 */
public class WWW_Demo2 {
    public static void out() {
        //确定目标文件
        File file = new File("D:\\test\\test4\\test.txt");

        try {
            Writer out = new FileWriter(file);
            out.write("你是我的神");
            out.close();
            System.out.println("写入成功");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static void in() {
        //确定目标文件
        File file = new File("D:\\test\\test4\\test.txt");

        try {
            Reader in = new FileReader(file);
            char[] cs = new char[20];
            int len = in.read(cs);
            StringBuilder buf = new StringBuilder();
            while(len!=-1){
                buf.append(new String(cs,0,len));
                len = in.read(cs);
            }
            in.close();
            System.out.println(buf);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) {
        out();
        in();
    }
}

~~~

### 文件copy例子

```
public static boolean copy(String src, String target){
    File srcFile = new File(src);
    File targetFile = new File(target);
    try{
        InputStream in = new FileInputStream(srcFile);
        OutputStream out = new FileOutputStream(targetFile);
        byte[] bytes = new byte[1024];
        int len = in.read(bytes);
        while(len!=-1){
            out.write(bytes,0,len);
            len = in.read(bytes);
        }
        in.close();
        out.close();
        return true;
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }
    return false;
}
```

## 转换流

~~~java
/**
 * 转换流
 * OutputStreamWriter,可以将输出的字符转换为字节流的输出形式
 * InputStreamReader,将输入的字节流转换为字符流输入形式
 */
public class WWW_Demo4 {

    public static void read(InputStream in) {
        Reader reader = new InputStreamReader(in, Charset.forName("UTF-8"));
        char[] cs = new char[20];
        try {
            int len = reader.read(cs);
            while (len != -1) {
                System.out.println(new String(cs, 0, len));
                len = reader.read(cs);
            }
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    public static void write(OutputStream out) {
        Writer writer = new OutputStreamWriter(out);
        try {
            writer.write("村花到我家");
            System.out.println("写入成功");
            writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    public static void main(String[] args) throws FileNotFoundException {
        InputStream in = new FileInputStream("D:\\test\\Demo.txt");
        read(in);
        OutputStream out = new FileOutputStream("D:\\test\\Demo.txt");
        write(out);
    }
}

~~~



![03](../javase-image/03.jpg)

## 缓冲流

~~~java
/**
 * 字节缓冲流
 * 解决在写入文件操作时，频繁的操作文件所带来的性能降低
 * BufferedOutputStream内部的缓存默认的缓存大小是8KB，当缓存满了就会刷新，写入文件
 */
public class WWW_Demo5 {
    public static void byteReader(){
        File file = new File("D:\\test\\Demo.txt");
        try{
            InputStream in = new FileInputStream(file);
            //构造一个字节缓冲流
            BufferedInputStream bis = new BufferedInputStream(in);
            StringBuilder str = new StringBuilder();
            byte[] bytes = new byte[2];
            int len = bis.read(bytes);
            while(len!=-1){
                str.append(new String(bytes,0,len));
                len = bis.read(bytes);
            }

            bis.close();
            System.out.println(str);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static void byteWriter(){
        File file = new File("D:\\test\\Demo.txt");
        try{
            OutputStream out = new FileOutputStream(file);
            //构造一个字节缓冲流
            BufferedOutputStream bos = new BufferedOutputStream(out);

            String str = "11123fa3111";
            bos.write(str.getBytes(StandardCharsets.UTF_8));
            //out可以不用关闭，因为bos的close方法能帮忙关闭
            //文件在缓冲流关闭时刷新到文件中，或者用flush方法
            bos.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    
    public static void main(String[] args) {
        byteWriter();
    }
}


/**
 * 字符流
 * 加入字符缓冲流会增强读取的功能（readline），更高效地读取数据
 * 比字节流减少了byte转换为char的次数
 */
public class WWW_Demo6 {

    public static void charReader(){
        try{
            Reader reader= new FileReader(new File("D:\\test\\Demo.txt"));
            //构造一个字节缓冲流
            BufferedReader br = new BufferedReader(reader);
            StringBuilder str = new StringBuilder();
            char[] chars = new  char[2];
            int len = br.read(chars);
            while(len!=-1){
                str.append(new String(chars,0,len));
                len = br.read(chars);
            }
            br.close();
            System.out.println(str);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static void charWriter(){
        try{
            Writer writer = new FileWriter(new File("D:\\test\\Demo.txt"));
            //构造一个字节缓冲流
            BufferedWriter bw = new BufferedWriter(writer);

            String str = "21314151";
            bw.write(str);
            bw.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        charWriter();
    }
}

~~~

## 打印流

~~~java
/**
 * 打印流：方便进行输出
 * 字节打印流
 * 在字节输出时可以增强输出功能
 * 字符打印流
 */
public class WWW_Demo7 {

    public static void bytePrint(){
        try{
        OutputStream out = new FileOutputStream(new File("D:\\test\\Demo.txt"));
        //加入缓存
        BufferedOutputStream bos = new BufferedOutputStream(out);
        //增强缓存
        PrintStream ps = new PrintStream(bos);
        ps.println("dhslakhdlashdl");
        ps.close();
        }catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }


    public static void charPrint(){
        try{
            Writer writer = new FileWriter(new File("D:\\test\\Demo.txt"));
            //加入缓存
            BufferedWriter bw = new BufferedWriter(writer);
            //增强缓存
            PrintWriter pw = new PrintWriter(bw);
            pw.println("这是一条");
            pw.close();
        }catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        charPrint();
    }
}

~~~

## 对象流与序列化

~~~java
/**
 * 对象流与序列化
 * 如果一个类创建的对象需要被序列化，需要实现Serializable接口
 * Serializable是一个标记接口，没有任何定义，为了告诉JVM该类对象可以被序列化
 *
 * 什么时候需要对象序列化
 * 1、把对象保存到文件中（存储到物理介质）
 * 2、对象需要在网络上传输时
 *
 * 对象序列化就是把对象写入文件，实际写入的是类名，属性名，属性类型，属性值等
 *
 * 反序列化就是从文件中把对象的内容读取出来，还原对象
 *
 * transient关键字用来修饰需要被序列化对象的属性，可以在序列化是屏蔽被此关键字修饰的属性
 */
public class ObjectStreamDemo {

    public static void readObject(){
        File file = new File("D:\\test\\dog.obj");
        try{
           InputStream in = new FileInputStream(file);
            ObjectInputStream ois = new ObjectInputStream(in);
            Dog dog = (Dog) ois.readObject();
            ois.close();
            System.out.println(dog);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    public static void writeObject(){
        Dog dog = new Dog("xiaodai",2,"gong");
        File file = new File("D:\\test\\dog.obj");
        try{
            OutputStream out = new FileOutputStream(file);
            ObjectOutputStream oos = new ObjectOutputStream(out);
            oos.writeObject(dog);
            oos.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        readObject();
    }
}

~~~

## 字节数组流

~~~java
public class ByteArrayStreamDemo {
    private static void byteArray(){
        String s = "dsajhdk414+_%&";
        ByteArrayInputStream bais = new ByteArrayInputStream(s.getBytes());
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        int curr = -1;//每次读取的字节
        while ((curr=bais.read())!=-1){
            if((curr>=65&&curr<=90)||(curr>=97&&curr<=122)){
                baos.write(curr);
            }
        }
        //无需关闭，字节数组流是基于内存的操作了流
        System.out.println(baos.toString());
    }
    public static void main(String[] args) {
        byteArray();
    }
}
~~~

## 数据流

~~~java

/**
 * 数据流
 * 与机器无关的操作Java基本数据类型
 */
public class DataStreamDemo {

    private static void read(){
        try {
            BufferedInputStream bis = new BufferedInputStream(new FileInputStream(new File("D:\\test\\Demo.txt")));
            DataInputStream dis = new DataInputStream(bis);
            int num = dis.readInt();
            byte b = dis.readByte();
            String s = dis.readUTF();
            System.out.println(num+" "+b+" "+s+" ");
            dis.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private static void write(){
        try {
            BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(new File("D:\\test\\Demo.txt")));
            DataOutputStream dos = new DataOutputStream(bos);
            dos.writeInt(10);//写入4个字节
            dos.writeByte(1);//写入1个字节
            dos.writeUTF("中");//这个汉字几个字节就写入几个字节
            dos.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    public static void main(String[] args) {
        write();
        read();
    }
}
~~~

## 小练习：文件分割与合并

~~~java
public class FileDivisionMergeDemo {
    /**
     * 文件合并
     */
    private static void merge(Enumeration<InputStream> es){
        //构造一个合并流
        SequenceInputStream sis = new SequenceInputStream(es);
        try{
        BufferedOutputStream bos = new BufferedOutputStream(
                new FileOutputStream(
                        new File("D:\\test\\test5\\合并诡秘之主.txt")));//文件写入位置
        byte[] bytes = new byte[1024];
        int len = sis.read(bytes);
        while(len!=-1){
            bos.write(bytes,0,len);
            len = sis.read(bytes);
            bos.flush();
        }
        bos.close();
        sis.close();
        System.out.println("合并完成");

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    /**
     * 文件的分割
     * @param targetFile 需要分割的目标文件
     * @param cutSize  分割后每个文件的大小
     */
    private static void division(File targetFile, long cutSize){
        if (targetFile==null)return;
        //计算分割的文件数
        int num = targetFile.length()%cutSize==0 ?
                (int)(targetFile.length()/cutSize) :
                (int)(targetFile.length()/cutSize+1);
        try {
            //构造一个文件输入流
            BufferedInputStream in = new BufferedInputStream(new FileInputStream(targetFile));
            BufferedOutputStream out = null;

            byte[] bytes = null;//每次读取的字节数
            int len = -1;
            int count = 0;//每一个文件要读取的次数

            for (int i = 0; i < num; i++) {
                out = new BufferedOutputStream(
                        new FileOutputStream(
                                new File(
                                        "D:\\test\\test1\\cuttest"+(i+1)+targetFile.getName())));
                if(cutSize<=1024){
                    bytes = new byte[(int)cutSize];
                    count = 1;
                }else {
                    bytes = new byte[1024];
                    count = (int)cutSize/1024;
                }
                while (count>0&&(len=in.read(bytes))!=-1){
                    out.write(bytes,0,len);
                    out.flush();
                    count --;
                }
                if(cutSize%1024!=0){
                    bytes = new byte[(int)cutSize];
                    len = in.read(bytes);
                    out.write(bytes,0,len);
                    out.flush();
                }
                out.close();
            }
            in.close();


        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
//        File file = new File("D:\\test\\诡秘之主.txt");
//        division(file,2048000);
        try{
            //集合工具类，内部实现了数组
            Vector<InputStream> v = new Vector<InputStream>();
            for (int i = 0; i < 5; i++) {
                InputStream in = new FileInputStream(new File("D:\\test\\test1\\cuttest"+(i+1)+"诡秘之主.txt"));
                v.add(in);
                v.elements();
            }
            Enumeration<InputStream> es = v.elements();
            merge(es);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }
}
~~~

## 字符串流与管道流

~~~java
/**
 * 字符串流：以一个字符串为数据源，来构造一个字符流
 * 作用：在WEB开发中，经常要从服务器上获取数据,数据的返回格式通过一个字符串（XML、Json），我们
 * 需要把这个字符串构造成一个字符流，然后再用第三方的数据解析器来解析数据。
 */
public class StringStreamDemo {
    private static void stringReader() {
        String str = "good good study";
        //字符串输入流
        StringReader sr = new StringReader(str);
        //流标记器,用于分析流的内容
        StreamTokenizer st = new StreamTokenizer(sr);
        int count = 0;//计算总数
        //TT_WORD表示令牌是一个单词。
        //TT_NUMBER表示令牌是一个数字。
        //TT_EOL表示已经读取了行尾。 如果使用参数true调用了eolIsSignificant方法，则该字段只能具有此值。
        //TT_EOF指示已经达到输入流的结尾。
        //ttype就是得到以上标识的类型
        try {
            while (st.ttype != StreamTokenizer.TT_EOF) {
                if (st.nextToken() == StreamTokenizer.TT_WORD) {
                    count++;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        sr.close();
        System.out.println("一共有"+count+"个单词");
    }

    public static void main(String[] args) {
        stringReader();
    }
}



/**
 * 管道流测试：一个线程写入，一个线程读取
 * 作用：用于线程之间的数据通讯
 */
public class PipedStreamDemo {
    public static void main(String[] args) {
        PipedInputStream pin = new PipedInputStream();
        PipedOutputStream pout = new PipedOutputStream();
        try{
            //输入流与输出流连接，两个管道进行连接
            pin.connect(pout);
        } catch (IOException e) {
            e.printStackTrace();
        }

        ReadThread readTh = new ReadThread(pin);
        WriteThread writeTh = new WriteThread(pout);
        //读取数据的线程要先启动，读不到数据会堵塞，直到写数据线程启动后，线程联通后可继续运行
        new Thread(readTh).start();//启动A线程
        new Thread(writeTh).start();//启动B线程
    }
}

//读取数据的线程
class ReadThread implements Runnable{
    private PipedInputStream pin;//输入管道

    public ReadThread(PipedInputStream pin) {
        this.pin = pin;
    }

    @Override
    public void run() {
        try{
            byte[] bytes = new byte[1024];
            int len = pin.read(bytes);//read阻塞
            System.out.println("读取到:"+new String(bytes,0,len));
            pin.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

//写入数据的线程
class WriteThread implements Runnable{
    private PipedOutputStream pout;//输出管道

    WriteThread(PipedOutputStream pout){
        this.pout = pout;
    }
    @Override
    public void run() {
        try{
            pout.write("一个美女".getBytes());//管道输出流
            pout.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
~~~

## RandomAccessFile工具类

~~~java
/**
 *  RandomAccessFile是IO包的类，直接继承Object，只可以对文件进行操作
 *   RandomAccessFile有强大的文件读写功能，其内部是大型byte[]，可以通过seek(),
 *   getFilePointer()等操作方法的指针，方便对数据进行写入和读取，还可以对基本数据类型进行直接的读和写操作。
 *   不过 RandomAccessFile的觉大多数功能，已经在JDK1.4后的NIO的“内存映射文件（memory-mapped files）”
 *   给取代了
 */
public class RandomAccessFileDemo {
    //复制文件
    public static void main(String[] args) {
        try{
            //读取文件
            RandomAccessFile r = new RandomAccessFile("D:\\test\\Demo.txt","r");
            //写入文件
            RandomAccessFile w = new RandomAccessFile("D:\\test\\test5\\Demo.txt","rw");
            byte[] bytes = new byte[1024];
            int len = r.read(bytes);
            while(len!=-1){
                w.write(bytes,0,len);
            }
            w.close();
            r.close();
            System.out.println("复制成功");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
~~~

## Properties工具类

~~~java
/**
 * Properties:
 * Properties可以用来做配置文件
 * javaweb javaee 开发中通常会用到
 * <p>
 * ResouceBundle 只读
 * Properties 可读可写
 */
public class PropertiesDemo {
    public static String version = "";
    public static String userName = "";
    public static String passWord = "";

    static {
        //一般放入静态代码块
        readConfig();
    }

    /**
     * 对配置文件的写入操作
     * @param version
     * @param userName
     * @param passWord
     */
    private static void writeConfig(String version, String userName, String passWord) {
        Properties p = new Properties();
        p.put("app.version", version);
        p.put("db.userName", userName);
        p.put("db.passWord", passWord);
        try {
            OutputStream outputStream = new FileOutputStream("千峰\\config.properties");
            p.store(outputStream, "更新配置信息");
            outputStream.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 读取properties配置文件
     */
    private static void readConfig() {
        Properties p = new Properties();

        try {
            InputStream inputStream = new FileInputStream("千峰\\config.properties");
            p.load(inputStream);//加载文件
            //从properties中获取数据
            version = p.getProperty("app.version");
            userName = p.getProperty("db.userName");
            passWord = p.getProperty("db.passWord");
            inputStream.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        System.out.println(version + " " + userName + " " + passWord);
        writeConfig("2","ding","654321");
        readConfig();
        System.out.println(version + " " + userName + " " + passWord);
    }
}

~~~



![04](../javase-image/04.png)

## 文件的压缩和解压

~~~java
import java.io.*;
import java.util.zip.ZipEntry;
import java.util.zip.ZipInputStream;
import java.util.zip.ZipOutputStream;

/**
 * 压缩和解压
 */
public class CompressionAndDeCompressionDemo {

    /**
     * 压缩操作
     *
     * @param zipFileName 压缩包的位置和名字
     * @param target      需要压缩的文件
     */
    private static void compression(String zipFileName, File target) {
        System.out.println("正在压缩...");
        try {
            //要生成的压缩文件
            ZipOutputStream out = new ZipOutputStream(new FileOutputStream(zipFileName));
            BufferedOutputStream bos = new BufferedOutputStream(out);
            zip(out, target, target.getName(), bos);
            bos.close();
            out.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("压缩成功");

    }

    /**
     * 压缩方法
     *
     * @param out    压缩流
     * @param target 需要压缩的文件
     * @param bos    缓冲流
     */
    private static void zip(ZipOutputStream out, File target, String name, BufferedOutputStream bos) {

        try {
            //如果是个目录（文件夹），就获取子目录
            if (target.isDirectory()) {
                File[] files = target.listFiles();
                if (files.length == 0) {//空文件夹
                    out.putNextEntry(new ZipEntry(name + "/"));//处理空目录
                }
                for (File file : files) {
                    //递归处理
                    zip(out, file, name + "/" + file.getName(), bos);
                }
            } else {
                out.putNextEntry(new ZipEntry(name));
                InputStream in = new FileInputStream(target);
                BufferedInputStream bis = new BufferedInputStream(in);
                byte[] bytes = new byte[1024];
                int len = bis.read(bytes);
                while (len != -1) {
                    bos.write(bytes, 0, len);
                    len = bis.read(bytes);
                }
                bis.close();
                return;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     *  文件解压方法
     * @param target 需要解压的文件的位置
     * @param path   解压后文件放置的位置
     */
    private static void decompression(String target, String path) {
        System.out.println("正在解压...");
        try {
            //构件解压输入流
            ZipInputStream zin = new ZipInputStream(new FileInputStream(target));
            ZipEntry entry = null;
            File file = null;
            while ((entry = zin.getNextEntry()) != null && !entry.isDirectory()) {
                file = new File(path, entry.getName());
                if (!file.exists()) {
                    //创建此文件的上级目录
                    new File(file.getParent()).mkdirs();//常见此文件的上级目录
                }
                OutputStream out = new FileOutputStream(file);
                BufferedOutputStream bos = new BufferedOutputStream(out);
                byte[] bytes = new byte[1024];
                int len = zin.read(bytes);
                while (len != -1) {
                    bos.write(bytes, 0, len);
                    len = zin.read(bytes);
                }
                bos.close();
            }
            System.out.println("解压成功...");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        //compression("D:/test.zip", new File("D:/test"));
        decompression("D:/test.zip","E:/test");

    }
}
~~~

![05](../javase-image/05.png)

## 常用的字符编码

![07](../javase-image/07.png)

![08](../javase-image/08.png)

## NIO

### NIO的基本概念和缓冲区

~~~java

public class NIODemo {


    public static void main(String[] args) {
        //创建一个字节缓冲区，申请的内存空间为8个字节
        ByteBuffer buf = ByteBuffer.allocate(8);
        System.out.println(buf.position());//0   当前位置
        System.out.println(buf.limit());//8      限制位置
        System.out.println(buf.capacity());//8   总空间量
        //向缓冲区中写入数据
        buf.put((byte)10);
        buf.put((byte)20);
        buf.put((byte)30);
        buf.put((byte)40);

        System.out.println(buf.position());//4
        System.out.println(buf.limit());//8
        System.out.println(buf.capacity());//8

        //缓冲区反转
        buf.flip();
        System.out.println(buf.position());//0
        System.out.println(buf.limit());//4
        System.out.println(buf.capacity());//8

        //当前位置和限制位置之间是否有元素
        if(buf.hasRemaining()) {
            //返回当前位置与限制之间的元素数
            for (int i = 0; i < buf.remaining(); i++) {
                System.out.println(buf.get(i));
            }
        }
    }
}
~~~

![09](../javase-image/09.png)![10](../javase-image/10.png)

### 通道文件操作与内存映射

~~~java
/**
 * IO操作的性能比较：
 * 1、内存映射最快
 * 2、NIO读写文件
 * 3、使用了缓存的IO流
 * 4、无缓存的IO流
 */
public class CopyFileDemo {

    private static void randomAccessFileCopy() {
        try {
            RandomAccessFile in = new RandomAccessFile("D:/test/test5/合并诡秘之主.txt", "r");
            RandomAccessFile out = new RandomAccessFile("D:/合并诡秘之主.txt", "rw");

            FileChannel fcIn = in.getChannel();
            FileChannel fcOut = out.getChannel();

            long size = fcIn.size();//输入流的字节大小
            MappedByteBuffer inBuf = fcIn.map(FileChannel.MapMode.READ_ONLY,0,size);//输入流缓存区:内存映射的方式
            MappedByteBuffer outBuf = fcOut.map(FileChannel.MapMode.READ_WRITE,0,size);//输出流缓存区
            for (int i = 0; i < size; i++) {
                outBuf.put(inBuf.get());
            }

            //关闭（关闭通道时会写入数据块）
            fcIn.close();
            fcOut.close();
            in.close();
            out.close();
            System.out.println("copy success");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    /**
     * 通过文件通道实现文件复制
     */
    private static void copyFile() {
        try {
            //创建一个输入的文件的通道
            FileChannel in = new FileInputStream("D:/test/test5/合并诡秘之主.txt").getChannel();
            //创建一个输出文件的通道
            FileChannel out = new FileOutputStream("D:/合并诡秘之主.txt").getChannel();

            ByteBuffer buf = ByteBuffer.allocate(1024);
            while (in.read(buf) != -1) {
                buf.flip();
                out.write(buf);
                buf.clear();
            }
            in.close();
            out.close();
            System.out.println("copy success");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        //copyFile();
        randomAccessFileCopy();
    }
}
~~~

![11](../javase-image/11.png)

### Path接口与Files工具类

~~~java
public class PathFileDemo {


    public static void main(String[] args) {
        File file = new File("D:/test/test.txt");
        //path
        Path p1 = Paths.get("D:/test", "test.txt");
        System.out.println(p1);//输出路径：D:\test\test.txt

        Path p2 = file.toPath();
        System.out.println(p2);//输出路径：D:\test\test.txt
        Path p3 = FileSystems.getDefault().getPath("D:/test", "test.txt");
        System.out.println(p3);//输出路径：D:\test\test.txt

        //写入文件
        Path p4 = Paths.get("D:/test/Demo.txt");
//        String info = "san刀两断";
//        try {
//            Files.write(p4, info.getBytes("UTf-8"), StandardOpenOption.APPEND);
//        } catch (UnsupportedEncodingException e) {
//            e.printStackTrace();
//        } catch (IOException e) {
//            e.printStackTrace();
//        }

        //读文件
        try {
            byte[] bytes = Files.readAllBytes(p4);
            System.out.println(new String(bytes));
        } catch (IOException e) {
            e.printStackTrace();
        }

//        try {
//            //复制文件
//            Files.copy(p4, Paths.get("e:/Demo.txt"), StandardCopyOption.REPLACE_EXISTING);
//        } catch (IOException e) {
//            e.printStackTrace();
//        }

//        try {
//            //移动文件
//            Files.move(p4, Paths.get("e:/Demo.txt"), StandardCopyOption.REPLACE_EXISTING);
//        } catch (IOException e) {
//            e.printStackTrace();
//        }

//        try {
//            //删除文件
//            Files.delete(p4);
//        } catch (IOException e) {
//            e.printStackTrace();
//        }

        //创建新目录，除了最后一个部件，其他必须是已存在的
        try{
            Files.createDirectories(Paths.get("c:/BB"));
        } catch (IOException e) {
            e.printStackTrace();
        }

        //创建文件
        try{
            Files.createFile(Paths.get("d:/BB/c.txt"));
        } catch (IOException e) {
            e.printStackTrace();
        }

        //添加前、后缀创建临时文件或临时目录
//        Path newPath = Files.createTempFile(dir,prefix,suffix);
//        Path newPath = Files.createTempFile(dir,prefix);

    }
}

~~~



![12](../javase-image/12.png)

![13](../javase-image/13.png)

![16](../javase-image/16.png)

![14](../javase-image/14.png)

![15](../javase-image/15.png)

# 集合

![17](../javase-image/17.png)

## Collection接口

![18](../javase-image/18.png)

### List接口

![19](../javase-image/19.png)

~~~java

/**
 * Collection接口:用于存储单个对象的集合
 * List接口：
 * 1、有序的
 * 2、允许多个空元素
 * 3、允许重复元素
 * 4、具体的常用的实现有：ArrayList,Vector,LinkedList
 * 在实际开发中，如何选择：
 * 1、安全性问题
 * 2、是否频繁插入，删除操作：LinkedList
 * 3、是否存储后遍历
 *
 */
public class ListDemo {

    /**
     * linkedList
     * 1、实现原理：采用双向链表结构实现
     * 2、适合插入，删除操作，性能高
     * 3、线程不安全
     */
    private static void linkList(){
        LinkedList<String> list = new LinkedList<>();
        list.add("啊哈哈");
        list.add("的哈回复");
        list.add("发送咯");
        for (String s : list) {
            System.out.println(s);
        }
    }

    /**
     * Vector
     * 1、实现原理：采用动态数组实现，默认构造方法创建了一个大小为10的对象数组
     * 2、扩充的算法：增量为0时，扩充为原来大小的两倍，当增量大于0时，扩充为原来大小——增量
     * 3、不适合删除或者插入操作
     * 4、为了防止数组动态扩充次数过多建议，创建Vector时，给定初始容量
     * 5、线程安全，适合在多线程访问时使用
     *
     */
    private static void vector(){
        Vector<String> vc = new Vector<>();
        vc.add("啊哈哈");
        vc.add("的哈回复");
        vc.add("发送咯");

        for (String v:vc
             ) {
            System.out.println(v);
        }
    }

    /**
     * ArrayList
     * 1、实现原理：采用动态对象数组实现，默认构造方法创建了一个空数组
     * 2、第一次添加元素，扩充容量为10，之后的扩充算法：原数组大小+原数组大小/2
     * 3、不适合进行删除或者插入操作
     * 4、为了防止数组动态扩充过多，建议创建ArrayList时，给定初始容量
     * 5、线程不安全，适合在单线程访问时使用
     */
    private static void arrayList(){
        //使用集合来存储多个不同类型的元素，实际开发不建议，直接加<>中间写限制类型
        List<String> list = new ArrayList<>();
        list.add("啊哈哈");
        list.add("的哈回复");
        list.add("发送咯");
        for (String s : list) {
            System.out.println(s);
        }
        System.out.println(list.contains("发送咯"));
        list.remove("啊哈哈");
        System.out.println(list.size());
        String[] array = list.toArray(new String[]{});
        for (String arr:array
             ) {
            System.out.println(arr);
        }
    }

    public static void main(String[] args) {
        vector();
    }
}

~~~

### Set接口

![20](../javase-image/20.png)

~~~java
/**
 * Set接口:
 * 1、无序的（不保证顺序）
 * 2、数据不可重复的
 * 3、最多有一个null数据
 * HashSet、TreeSet、LinkedHashSet
 *
 * 如果要排序，选择treeSet
 * 如果不要排序，也不用保证顺序,选择hashSet
 * 不要排序，要保证顺序，选择LinkedHashSet
 *
 */
public class SetDemo {

    /**
     * 哈希表和连接列表实现，
     * 维护着一个运行于所有条目的双重连接列表，此连接列表定义了迭代顺序，即按照将元素的插入顺序进行迭代。
     *
     */
    private static void linkedHashSet(){
        LinkedHashSet<Student> set = new LinkedHashSet<>();
        Student s1 = new Student("李帆",24);
        Student s2 = new Student("李饭",23);
        Student s3 = new Student("李凡",22);
        Student s4 = new Student("李",24);
        set.add(s1);
        set.add(s2);
        set.add(s3);
        set.add(s4);
        for (Student stu:set
        ) {
            System.out.println(stu);
        }
    }

    /**
     * treeSet
     * 1、有序的，基于TreeMap(二叉树数据结构)，对象需要通过对象比较器Comparator比较大小
     *    对象比较器还可以取出重复元素，如果自定义的对象的数据类没有实现比较器接口，将无法添加
     *    到treeSet集合中
     * 2、
     */
    private static void treeSet(){
        TreeSet<Student> tree = new TreeSet<>(new StudentComparator());
        Student s1 = new Student("李帆",24);
        Student s2 = new Student("李饭",23);
        Student s3 = new Student("李凡",22);
        Student s4 = new Student("李",24);
        tree.add(s1);
        tree.add(s2);
        tree.add(s3);
        tree.add(s4);
        System.out.println(tree.size());

        for (Student stu:tree
             ) {
            System.out.println(stu);
        }

    }

    /**
     * HashSet
     * 1、实现原理，基于哈希表（hashMap）实现
     * 2、不允许重复，可以有一个NULL元素
     * 3、不保证顺序恒久不变
     * 4、添加元素时把元素作为hashMap的Key来存储，Value固定使用一个Object对象填充
     * 5、排除重复元素时通过equals来检查对象是否相同，加入重复的对象，新对象会顶替原对象
     * 6、判断两个对象是否相同吗，先判断两个对象的hashCode是否相同（hashCode相同，不一定是一个对象，如果不同一定不是一个对象），
     *    如果不同则不是一个对象，如果相同，还要进行equals判断，equals相同则相同
     * 7、自定义对象要重写hashCode和equals，才能达到插入时不重复相同对象
     *  (1)哈希表的存储结构：数组+链表：数组里的每个元素都以链表的形式存储
     *  (2)如何把对象存储到哈希表中，先计算对象的hashCode值，再对数组的长度求余数，来决定对象要存储在数组中的那个位置
     *  (3)解决hashSet中的重复值使用的方式参考第六点
     *
     */
    private static void hashSet(){
        Set<String> set = new HashSet<>();
        set.add("飞飞");
        set.add("关关");
        set.add("备备");
        set.add("亮亮");
        set.add("曹操");

        String[] names = set.toArray(new String[]{});
        for (String name:names
             ) {
            System.out.println(name);
        }

    }

    public static void main(String[] args) {
        linkedHashSet();
    }
}
class Student{
    String name;
    int age;

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return age == student.age && Objects.equals(name, student.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}


import java.util.Comparator;

class StudentComparator implements Comparator<Student> {
    @Override
    public int compare(Student o1, Student o2) {
        return o1.age-o2.age;
    }
}

~~~

