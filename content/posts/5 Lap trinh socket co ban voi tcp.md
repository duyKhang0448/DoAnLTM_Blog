---
title: "5. Lập Trình Socket Cơ Bản Với TCP/IP Trong Java"
date: 2024-05-02T23:39:49+05:30
tags: []
type: "post"
image: "/images/lorem-ipsum/quick-fox.png"
showTableOfContents: false
---
![Alt text](/images/lap-trinh-socket-voi-tcpip-trong-java-63732476941.2382.jpg)

***Socket được biết đến là cánh cửa giao tiếp giữa hai tiến trình ứng dụng. Socket hỗ trợ nhiều ngôn ngữ (C, java, perl, python,…) cũng như nhiều platform (nix, Windows,…). Socket được viết bằng các ngôn ngữ khác nhau và chạy trên bất kỳ platform nào đều có thể giao tiếp với nhau. Trong bài viết này mình gửi đến các bạn khái niệm cơ bản về Socket cũng như cách lập trình Socket với các loại giao thức.***

# 1. Tổng quan về Socket

Trong hệ thống mạng máy tính tồn tại những mô hình tham chiếu có kiến trúc phần tầng (OSI, TCP/IP…) nhằm hỗ trợ chức năng trao đôi thông tin giữa các ứng dụng ở nhiều máy tính khác nhau.

![Alt text](/images/Annotation2020-08-08132406.jpg)

Dữ liệu bên gửi sẽ được đóng gói (Encapsulation) từ tầng trên đến tầng cuối là tầng vật lí (Physical Layer), sau đó nhờ tầng vật lí này chuyển dữ liệu đến tầng vật lí máy bên nhận, bên nhận tiến hành giải mã (decapsulation) gói dữ kiện từ tầng dưới lên tầng trên cùng, là tầng ứng dụng (application layer).

![Alt text](/images/Annotation2020-08-08160924.jpg)

Ở đây, Socket chính là cửa giao tiếp giữa tầng ứng dụng và tầng giao vận (Transport layer). Nói cách khác, Socket là giao diện do ứng dụng tạo ra trên máy trạm, quản lí bởi hệ điều hành qua đó các ứng dụng có thể gửi/nhận thông điệp đến/từ các ứng  dụng khác. Ở đó, Socket sẽ được ràng buộc với một mã số cổng (Port Number) để giúp tầng giao vận định danh được ứng dụng nhận/gửi thông điệp.

Các bạn có thể thấy ở hình ảnh trên, tầng giao vận có 2 phương thức là TCP (Ttransmission Control Protocol) và UDP (User Datagram Protocol), như vậy socket cơ bản là có 2 loại: Stream Socket sử dụng TCP truyền dòng bytes và Datagram Socket dử dụng UDP truyền gói tin. Với ngôn ngữ lập trình Java, chúng ta được cung cấp 3 loại khác nhau của sockets:

1. Stream Socket (TCP) : Tạo luồng dữ liệu hai chiều, đáng tin cậy, có trình tự và không trùng lặp, dữ liệu chỉ được gửi/nhận khi có đã có liên kết. Dùng với Socket Class của java.
2. Datagram Socket (UDP): Có thể nhận dữ liệu không theo tình tự, trùng lặp. Dùng với DatagramSocket Class.
3. Multicast Socket : cho phép dữ liệu được gửi đến nhiều bên nhận một lúc. Dùng với DatagramSocket Class.

Socket được hỗ trợ trên nhiều ngôn ngữ như C, Java, Pearl,  Python,…. Sau đây là một ví dụ lập tình socket với Java.

# 2. Lập trình TCP Socket với Java

Trong bài viết này mình sẽ nói về lập trình Socket sử dụng TCP.

![Alt text](/images/Annotation2020-08-08160941.jpg)

Đúng như tính chất của TCP chúng ta cần có liên kết 2 chiều trước khi server và client có thể trao đổi thông điệp với nhau.

Ban đầu, phía server tạo Socket được ràng buộc với một cổng (port number) để chờ nhận yêu cầu từ phía client.

Tiếp đến phía client yêu cầu server bằng cách tạo một Socket TCP trên máy kèm với địa chỉ IP và port number của tiến tình tương ứng trên máy server. Khi client tạo Socket, client TCP tạo liên kết với server TCP và chờ chấp nhận kết nối từ server.

TCP cung cấp dịch vụ truyền dòng tin cậy và có thứ tự giữa client và server, giữa máy chủ và máy nhận chỉ có 1 địa chỉ IP duy nhất. Thêm vào đó, mỗi thông điệp truyền đi đều có xác nhận trả về.

Sau đây là một ví dụ ứng dụng đơn giản về lập trình TCP Socket với Java.

Miêu tả ứng dụng:

- Client đọc dòng văn bản nhập từ bàn phím người dùng , gửi tới server qua Socket
- Server đọc các dòng văn bản gửi từ Socket
- Server sẽ chuyển lại dòng văn bản kèm theo “Server accepted” tới phía client qua Socket
- Client đọc dòng văn bản từ socket và in ra dòng văn bản nhận được từ server

![Alt text](/images/Annotation2020-08-08132421.jpg)

Chúng ta có thể thấy rằng mỗi phía server và client đều có 2 luồng dữ liệu, một luồng ra Socket để gửi thông điệp và một luồng vào từ Socket để nhận thông điệp, như vậy với mỗi bên mình có hai biến input và output (inFromServer, outToServer và inFromClient, outToClient).

Trong ví dụ này mình dùng công cụ NetBeans IDE  và Socket Class, serverSocket của Java. Sau đây là file java cho phía server:

{{< highlight java >}}
package socket;
import java.io.*;
import java.net.Socket;
import java.net.ServerSocket;
public class serverTCP {
    public static void main(String argv[]) throws Exception
    {
        String sentence_from_client;
        String sentence_to_client;
        
        //Tạo socket server, chờ tại cổng '6543'
        ServerSocket welcomeSocket = new ServerSocket(6543);
        
        while(true) {
            //chờ yêu cầu từ client
            Socket connectionSocket = welcomeSocket.accept();
            
            //Tạo input stream, nối tới Socket
            BufferedReader inFromClient =
                new BufferedReader(new
                    InputStreamReader(connectionSocket.getInputStream())); 
            
            //Tạo outputStream, nối tới socket
            DataOutputStream outToClient =
                new DataOutputStream(connectionSocket.getOutputStream());
            
            //Đọc thông tin từ socket
            sentence_from_client = inFromClient.readLine();
            
            sentence_to_client = sentence_from_client +" (Server accepted!)" + '\n';
            //ghi dữ liệu ra socket
            outToClient.writeBytes(sentence_to_client); 
            return;
        }
        
    }
}
{{< /highlight >}}

Ở đây mình tạo 2 biến `String` để nhận và gửi dữ liệu cho/từ phía client, và một biến của ServerSocket để chờ liên kết từ phía client, với số cổng là một số ngẫu nhiên lớn hơn 1024 ( trong một số hệ thống yêu cầu đặc quyền quản trị để ràng buộc số cổng < 1024), ở đây mình chọn 6543.

Tiếp theo trong vòng lặp `While` mình cho giá tị true để serversocket luôn chấp nhận liên kết từ client, các bạn có thể chèn thêm điều kiện dừng cho vòng lặp. Như đã nói ở trên mình sẽ tạo 1 socket connectionSocket để nhận một đối tượng socket từ server đồng thời chấp nhận liên kết từ client qua phương thức accept().

Sau đó là 2 biến inFromClient và outToClient để nhận và gửi dữ liệu lên socket.

Biến sentence_from_client sẽ lấy dòng văn bản được truyền từ phía client thông qua biến inFromClient, biến sentence_to_client được gán bằng biến sentence_from_client + “ (Server accepted!)”.

Biến sentence_to_client sẽ được chuyển lên Socket qua phương thức writeBytes().

Còn về phía client:

{{< highlight java >}}
package socket;
import java.io.*;
import java.net.Socket;
public class clientTCP {
    public static void main(String argv[]) throws Exception
    {
        String sentence_to_server;
        String sentence_from_server;
    
        //Tạo Inputstream(từ bàn phím)
        System.out.print("Input from client: ");
        BufferedReader inFromUser =
            new BufferedReader(new InputStreamReader(System.in));
        //Lấy chuỗi ký tự nhập từ bàn phím
        sentence_to_server = inFromUser.readLine();
//    
        //Tạo socket cho client kết nối đến server qua ID address và port number
        Socket clientSocket = new Socket("127.0.0.1", 6543);
    
        //Tạo OutputStream nối với Socket
        DataOutputStream outToServer =
            new DataOutputStream(clientSocket.getOutputStream());
    
        //Tạo inputStream nối với Socket
        BufferedReader inFromServer =
            new BufferedReader(new
            InputStreamReader(clientSocket.getInputStream()));
//     
        //Gửi chuỗi ký tự tới Server thông qua outputStream đã nối với Socket (ở trên)
        outToServer.writeBytes(sentence_to_server + '\n');
    
        //Đọc tin từ Server thông qua InputSteam đã nối với socket
        sentence_from_server = inFromServer.readLine();
    
        //print kết qua ra màn hình
        System.out.println("FROM SERVER: " + sentence_from_server);
    
        //Đóng liên kết socket
        clientSocket.close();    
    } 
}
{{< /highlight >}}

Mình cũng có 2 biến String để nhận và gửi dòng văn bản. Nhận văn bản được nhập từ bàn phím cho biến sentence_to_server qua biến inFromUser.

Tiếp theo là tạo một socket bên client hướng liên kết tới địa chỉ IP “localhost” và cổng 6543 giống với số cổng Server, ở đây vì mình đang sử dụng trên một máy tính lên địa chỉ IP để là “localhost” hoặc “127.0.0.1”, máy tính sẽ tham chiếu tới chính nó.

Tạo 2 biến outToServer và inFomServer để nhận và gửi dữ liệu.

Biến sentence_to_server sẽ được chuyển lên socket để truyền tới server qua phương thức writeBytes().

Sau khi server nhận dòng văn bản, xử lí và chuyển lên socket, biến sentence_from_server sẽ nhận dòng văn bản đó thông qua phương thức readLine(). Cuối cùng chúng ta in ra dòng văn bản vừa nhận từ Server và đong socket bằng phương thức close().

Để chạy được ví dụ này chúng ta cần run file java phía server trước để thiết lập server socket, sau đó run file java phía client. Đây là kết quả sau khi chạy lần lượt 2 file trên.

![Alt text](/images/Annotation2020-08-08163442.jpg)
