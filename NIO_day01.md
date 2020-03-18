##### java NIO的主要内容

1. **java nio 简介**
   1. nio（new io/non blocking io---非阻塞式），nio支持面向缓冲区的、基于通道的io操作。nio将更加高效的方式进行文件的读写操作
2. **java nio 与 io的主要区别**
3. **缓冲区（buffer）和通道（channel）**
4. **文件通道（fileChannel）**
5. **nio的非阻塞式网络通信**
   1. **选择器（selector）**
   2. **socketChannel、serverSocketChannel、DatagramChannel**
6. **管道（pipe）**
7. **java nio2（path，paths与files）**

##### java nio和io的主要区别

| IO                        | NIO                           |
| ------------------------- | ----------------------------- |
| 面向流（stream oriented） | 面向缓冲区（buffer oriented） |
| 阻塞IO（blocking IO）     | 非阻塞式IO（Non Blocking IO） |
| -------                   | 选择器（selectors）           |

##### 简而言之 Channel负责传输，Buffer负责存储

##### 根据数据类型的不同（boolean除外），提供了相应类型的缓冲区

ByteBuffer CharBuffer ShortBuffer IntBuffer LongBuffer FloatBuffer DoubleBuffer：上述缓冲区的管理方式几乎一样，通过allocate（）获取缓冲区

**缓冲区存取数据的两个核心方法**

1. put（）：存入数据到缓冲区中

2. get（）：获取缓冲区中的数据

   **缓冲区中的四个核心属性**

1. capacity：容量，表示缓冲区中最大存储数据的容量，一旦声明不可以改变
2. limit：界限，表示缓冲区中可以操作数据的大小（limit后数据不能进行读写）
3. position：位置，表示缓冲区中正在操作数据的位置
4. mark：标记，表示记录当前position的位置。可以通过reset（）恢复到mark的位置
5. 0<=mark<=position <= limit <= capacity

##### 其他方法

rewind（）：可重复读

clear：清空缓冲区,缓冲区中的数据依然存在，处于被遗忘状态

reset():恢复到mark的位置

##### 直接缓冲区与非直接缓冲区

非直接缓冲区：通过allocate（）方法分配缓冲区，将缓冲区建立在jvm的内存中

直接缓冲区：通过allocateDirect（）方法分配直接缓冲区，将缓冲区建立在物理内存中。可以提高效率。

##### Channel（通道）：利用通道把数据映射到缓冲区

通道的主要实现类：

1. FileChannel：用于本地
2. SocketChannel：用于tcp
3. ServerSocketChannel：用于tcp
4. DatagramChannel：用于udp

获取通道：

#####第一种

1. 本地IO：
   1. FileInputStream/FileOutputStream
   2. RandomAccessFile
2. 网络IO：
3. Socket
4. ServerSocket
5. DatagramSocket

##### 第二种

在JDK1.7中的NIO.2针对各个通道提供了静态方法open（）

##### 第三种

在JDK1.7中的NIO.2的Files工具类的newByteChannel（）

//利用通道完成文件的复制
	```@Test
	public void test1() throws FileNotFoundException{
		FileInputStream fis = new FileInputStream("1.jpg");
		FileOutputStream fos = new FileOutputStream("2.jpg");
		//获取通道
		FileChannel inChannel = fis.getChannel();
		FileChannel outChannel = fos.getChannel();
		//分配指定大小的缓冲区
		ByteBuffer buffer = ByteBuffer.allocate(1024);
		//将通道中的数据存入缓冲区
		try {
			while(inChannel.read(buffer)!=-1){
				//切换到读取数据的模式
				buffer.flip();
				//将缓冲区中的数据写入通道
				try {
					outChannel.write(buffer);
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				//清空缓冲区
				buffer.clear();
			}
		} catch (IOException e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		}
		try {
			outChannel.close();
			inChannel.close();
			fos.close();
			fis.close();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}```

​	`}`

##### 使用直接缓冲区完成文件的复制（内存映射文件）

```java
@Test
public void test2(){
    FileChannel inChannel = FileChannel.open(Paths.get("1.jpg"),StandardOpenOption.READ);
    FileChannel outChannel = FileChannel.open(Paths.get("2.jpg"),StandardOpenOption.WRITE,StandardOpenOption.CREATE);
    //只有bytebuffer支持-----字节
   MappedByteBuffer inMappedBuf = intChannel .map(MapMode.READ_ONLY,0，inChannel.size（）)；
       MappedByteBuffer outMappedBuf = intChannel .map(MapMode.READ_WRITE,0，inChannel.size（）)；
       //直接对缓冲区进行数据的读写操作
       byte[] dst = new byte[inMappedBuf.limit()];
    	inMappedBuf.get(dst);
    	outMappedBuf.put(dst);
}
```

##### 通道之间的数据传输

1. transferFrom（）
2. transferTo（）

##### 分散（scatter）与聚集（gather）

分散读取（Scattering Reads）：将通道中的数据分散到多个缓冲区中

聚集写入（Gathering Writes）：将多个缓冲区中的数据聚集到通道中

##### 字符集（Charset）

1. 编码：字符串------字节数组
2. 解码：字节数组---字符串

##### 使用NIO完成网络通信的三个核心

1. 通道（Channel）：负责连接
   1. java.nio.channels.Channel接口：
      1. SelectableChannel：
         1. SocketChannel
         2. ServerSocketChannel：tcp
         3. DatagramChannel：udp
         4. Pipe.SinkChannel
         5. Pipe.SourceChannel 
2. 缓冲区（Buffer）：负责数据的存取
3. 选择器（Selector）：是SelectableChannel 的多路复用器。用于监控SelectableChannel的IO状况

```java
//客户端
@Test
public void client() throws IOException{
    SocketChannel sChannel SocketChannel.open(new InetSocketAddress("127.0.0.1",8899));
    //切换到非阻塞模式
    sChannel.configureBlocking（false）；
    //分配指定大小的缓冲区
        ByteBuffer buf = ByteBuffer.allocate（1024）；
    //发送数据给服务器
    buf.put（new Date（）.toString（）.getBytes（））；
    buf.flip（）；
    sChannel.write（buf）；
    buf.clear（）；
    //关闭通道
    sChannel.close（）；
}


@Test
public void server(){
    //获取通道
    ServerSocketChannel ssChannel = ServerSocketChannel.open（）；
    //切换到非阻塞模式
    ssChannel.configureBlocking（false）；
    //绑定连接
    ssChannel.bind（new InetSocketAddress（8899））；
    //获取选择器
    Selector selector = Selector.open（）；
    //将通道注册到选择器上，并且指定“监听接收事件”
    ssChannel.register（selector，SelectionKey.OP_ACCEPT）;
    //轮询式的获取选择器上已经就绪的事件
    while（selector.select（）>0）{
        //获取当前选择器中所有注册的选择键（已经就绪的监听事件）
        Iterator <SelectionKey> it = selector.selectedKeys().iterator();
        while(it.hasnext()){
            //获取准备就绪的事件
            SelectonKey sk = it.next();
            //判断具体是什么事件准备就绪
            if(sk.isAcceptable()){
                //若接收就绪，获取客户端连接
                SocketChannel sChannel = ssChannel.accept();
                //切换非阻塞模式
                sChannel.configureBlocking（false）；
                //将该通道注册到选择器上
                sChannel.register（selector，SelectonKey.OP_READ）；
            }
        }
}
```

