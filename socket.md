# socket编程

## 使用tcp/ip创建socket编程的过程

客户端：
1.创建socket套接字（指定ip和端口号）
2.打开连接到socket的输出流
3.关闭socket操作

## 代码实列：
public class client01 {

    public static void main(String[] args) throws IOException {

        InputStream is = client01.class.getClassLoader().getResourceAsStream("renwen.jpg");
        System.out.println(is);
        byte[] bytes = FileCopyUtils.FileCopy(is);
        Socket socket = new Socket("127.0.0.1",9999);
        OutputStream outputStream = socket.getOutputStream();
        outputStream.write(bytes);
        socket.shutdownOutput();
        
        InputStream inputStream = socket.getInputStream();
        System.out.println(inputStream.read());
        inputStream.close();
        outputStream.close();
        is.close();
        socket.close();
    }
}

接收端：
1.创建 ServerSocket 对象，用于监听客户端的请求
2.调用accept()获取到socket
3.调用socket获取输入流读取数据
4.关闭socket操作
## 代码实列：
public class server {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(9999);
        System.out.println("服务启动，等待客户端连接......");
        Socket socket = serverSocket.accept();
        InputStream inputStream = socket.getInputStream();
        FileOutputStream fos = new FileOutputStream(new File("D:\\desktop\\code\\images\\renwen2.jpg"));
        byte[] buff = new byte[1024];
        int len;
        while ((len = inputStream.read(buff))!=-1){
            fos.write(buff,0,len);
        }

        //向客户端返回数据
        OutputStream outputStream = socket.getOutputStream();
        outputStream.write(1);
        socket.shutdownOutput();

        outputStream.close();
        fos.close();
        inputStream.close();
        socket.close();
        serverSocket.close();
    }
}


# 注意的一些问题

## 如何判断数据是否传输完成，避免阻塞
1.使用缓冲区：在接收端创建一个缓冲区，将从Socket的输入流中读取的数据暂存到缓冲区中。可以使用BufferedReader等类来实现。
2.设置约定的消息结束标记：发送端在每条消息的末尾添加一个特定的标记，用于表示消息的结束。例如，可以在每条消息的末尾添加一个换行符\n作为结束标记。或者socket.shutdownOutput();
3.如果传输数据时一个对象，可以判断接收到的对象是否存在来判断数据是否传输完成
## socket中对象传输
1.对象要序列化
