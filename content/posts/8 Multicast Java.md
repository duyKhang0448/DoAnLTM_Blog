---
title: "8. Xây dựng chương trình Client-Server nối kết theo dạng multicast (truyền theo nhóm) Java"
date: 2024-12-15T23:39:49+05:30
tags: []
type: "post"
image: "/images/lorem-ipsum/quick-fox.png"
showTableOfContents: false
---
![Alt text](/images/1596093882.png)

# 1. Multicast là gì?

Các cơ chế Socket TCP và UDP đã giới thiệu ở trên đề gọi là unicast, nghĩa là giao tiếp chỉ diễn ra giữa một máy tính gửi và một máy tính nhận.

Multicast là việc gửi quảng bá (broadcast) nhưng đến một nhóm máy tính ở cùng một địa chỉ cho trước. Địa chỉ multicast là địa chỉ lớp D được xác định trong khoảng 244.0.0.0 đến 239.255.255.255. Địa chỉ 244.0.0.0 là địa chỉ riêng nên không sử dụng được.

Multicast được sử dụng trong game nhiều người chơi, trong những ứng dụng mà đối tượng là nhiều thiết bị hay nhiều máy tính cùng nhận một loại thông tin. Multicast cũng được sử dụng trong giải thuật vạch đường (Routing Protocol), khi các router muốn cập nhật thông tin với nhau.

Java hỗ trợ Multicast thông qua lớp java.net.MulticastSocket. Một Multicast Socket là 1 DatagramSocket (UDP) có khả năng gia nhập (joining) vào một nhóm các máy tính multicast trên mạng. Khi một máy tính nào gửi thông điệp đến nhóm thì tất cả các máy tính trong đó đều nhận được.

![Alt text](/images/java-multicast.png)

# 2. Một số phương thức cần thiết để xây dựng các chương trình Multicast

- public **MulticastSocket(int port)** : Tạo Socket kiểu Multicast với số hiệu cổng được xác định trong tham số (port).
- public void **joinGroup(InetAddress group)** : Tham gia nhóm Multicast tại địa chỉ xác định.
- public void **leaveGroup(InetAddress group)** : Rời khỏi nhóm Multicast tại địa chỉ xác định.
- public void **send(DatagramPacket dp)** : Dùng để gởi một DatagramPacket đi.
- public synchronized void **receive(Datagrampacket dp)** : Chờ nhận một DatagramPacket.

# 3. Ví dụ:

Cài đặt một dịch vụ gởi tin nhắn trên cổng 8888, tại địa chỉ 224.0.0.1. Khi Client muốn nhận tin nhắn thì Join Group tại địa chỉ và cổng trên.

**Lớp Sender**

{{< highlight java >}}
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
 
public class MulticastSender {
 
    public static final String GROUP_ADDRESS = "224.0.0.1";
    public static final int PORT = 8888;
 
    public static void main(String[] args) throws InterruptedException {
        DatagramSocket socket = null;
        try {
            // Get the address that we are going to connect to.
            InetAddress address = InetAddress.getByName(GROUP_ADDRESS);
 
            // Create a new Multicast socket
            socket = new DatagramSocket();
 
            DatagramPacket outPacket = null;
            long counter = 0;
            while (true) {
                String msg = "Sent message No. " + counter;
                counter++;
                outPacket = new DatagramPacket(msg.getBytes(), msg.getBytes().length, address, PORT);
                socket.send(outPacket);
                System.out.println("Server sent packet with msg: " + msg);
                Thread.sleep(1000); // Sleep 1 second before sending the next message
            }
        } catch (IOException ex) {
            ex.printStackTrace();
        } finally {
            if (socket != null) {
                socket.close();
            }
        }
    }
}
{{< /highlight >}}

**Lớp Receiver**

{{< highlight java >}}
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.InetAddress;
import java.net.MulticastSocket;
 
public class MulticastReceiver {
 
    public static final byte[] BUFFER = new byte[4096];
 
    public static void main(String[] args) {
        MulticastSocket socket = null;
        DatagramPacket inPacket = null;
        try {
            // Get the address that we are going to connect to.
            InetAddress address = InetAddress.getByName(MulticastSender.GROUP_ADDRESS);
 
            // Create a new Multicast socket
            socket = new MulticastSocket(MulticastSender.PORT);
 
            // Joint the Multicast group
            socket.joinGroup(address);
 
            while (true) {
                // Receive the information and print it.
                inPacket = new DatagramPacket(BUFFER, BUFFER.length);
                socket.receive(inPacket);
                String msg = new String(BUFFER, 0, inPacket.getLength());
                System.out.println("From " + inPacket.getAddress() + " Msg : " + msg);
            }
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }
}
{{< /highlight >}}

Chạy chương trình MulticastSender, sau đó start tiếp một vài chương trình MulticastReceiver. Chúng ta nhận được kết quả như sau:

![Alt text](/images/multicast-server-sender.png)

![Alt text](/images/multicast-client-receiver-1.png)

![Alt text](/images/multicast-client-receiver-2.png)

Như bạn thấy, bất kỳ client nào join group đều nhận được nội dung message giống nhau, và nhận cùng thời điểm. Chương trình Multicast Server vẫn phục vụ theo dạng UDP, chỉ khác là sẽ gửi gói tin Datagram đến địa chỉ IP thuộc lớp D.