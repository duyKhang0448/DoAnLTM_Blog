---
title: "4. InetAddress trong java – Phương thức chuyển tên miền sang IP"
date: 2024-04-18T23:39:49+05:30
tags: []
type: "post"
image: "/images/lorem-ipsum/quick-fox.png"
showTableOfContents: false
---
# 1. InetAddress là gì?

Trong Java, InetAddress là một lớp được sử dụng để hỗ trợ các ứng dụng java mạng. Lớp này được thực hiện các hoạt động mạng như chuyển đổi tên miền đã cho sang địa chỉ IP và ngược lại.

# 2. Các phương thức cơ bản

- `getAddress()`: trả về địa chỉ IP thô, nghĩa là một mảng các byte biểu diễn địa chỉ IP dưới dạng dạng nhị phân.
- `getAllByName()`: nhận vào tên của một máy chủ (host) và trả về một mảng chứa tất cả các địa chỉ IP tương ứng với máy chủ đó.
- `getByAddress(byte[] addr)`: trả về một đối tượng InetAddress dựa trên địa chỉ IP thô được biểu diễn bằng một mảng byte.
- `getByName(String host)`: xác định địa chỉ IP của một máy chủ dựa trên tên của máy chủ đó.
- `getHostAddress()`: trả về địa chỉ IP dưới dạng văn bản.
- `getHostName()`: lấy tên máy chủ.
- `getLocalHost()`: trả về địa chỉ IP máy chủ localhost.
- `toString()`: chuyển IP thành một chuỗi ký tự.

# 3. Cách tạo đối tượng InetAddress trong Java
   
Gói `java.net.InetAddress` có lớp InetAddress cho phép bạn làm việc với các tên miền hoặc địa chỉ IP. Thường để bắt đầu, cần tạo ra một đối tượng InetAddress:

***Từ địa chỉ IP:***

{{< highlight java >}}
String ipAddress = "127.0.0.1";
InetAddress inetAddress = InetAddress.getByName(ipAddress);
{{< /highlight >}}

***Từ tên miền:***

{{< highlight java >}}
String hostName = "www.example.com";
InetAddress inetAddress = InetAddress.getByName(hostName);
{{< /highlight >}}

Nếu bạn muốn lấy tất cả các địa chỉ IP của máy chủ, bạn có thể sử dụng phương thức `getAllByName`:

{{< highlight java >}}
String hostName = "www.example.com";
InetAddress[] addresses = InetAddress.getAllByName(hostName);
{{< /highlight >}}

***Từ địa chỉ IP byte array:***

{{< highlight java >}}
byte[] ipAddressBytes = {127, 0, 0, 1};
InetAddress inetAddress = InetAddress.getByAddress(ipAddressBytes);
{{< /highlight >}}

# 4. Sử dụng phương thức getByName trong InetAddress

Phương thức `getByName(String host)` của lớp InetAddress được sử dụng để tạo một đối tượng InetAddress từ tên máy chủ hoặc địa chỉ IP.

{{< highlight java >}}
private void getByNameExample(){
    try {
        // Tên máy chủ hoặc địa chỉ IP
        String hostName = "ttnguyen.net";
        // Sử dụng phương thức getByName để tạo đối tượng InetAddress
        InetAddress inetAddress = InetAddress.getByName(hostName);
        // Hiển thị thông tin về đối tượng InetAddress
        System.out.println("Host Name: " + inetAddress.getHostName());
        System.out.println("Host Address: " + inetAddress.getHostAddress());
    } catch (Exception e) {
        // Xử lý nếu không thể tìm thấy địa chỉ IP hoặc tên máy chủ
        e.printStackTrace();
    }
}
{{< /highlight >}}

![Alt text](/images/getbyname-inetaddress.jpg)

# 5. Phương thức getHostName trong InetAddress

Phương thức `getHostName()` của lớp InetAddress trong Java được sử dụng để lấy tên máy chủ của đối tượng InetAddress.

{{< highlight java >}}
private void getHostName() {
    try {
        // Tên máy chủ hoặc địa chỉ IP
        String hostName = "ttnguyen.net";
        // Sử dụng phương thức getByName để tạo đối tượng InetAddress
        InetAddress inetAddress = InetAddress.getByName(hostName);
        // Sử dụng phương thức getHostName để lấy tên máy chủ
        String retrievedHostName = inetAddress.getHostName();
        // Hiển thị tên máy chủ
        System.out.println("Retrieved Host Name: " + retrievedHostName);
    } catch (Exception e) {
        // Xử lý nếu không thể tìm thấy địa chỉ IP hoặc tên máy chủ
        e.printStackTrace();
    }
}
{{< /highlight >}}

![Alt text](/images/gethostname-inetaddress.jpg)

# 6. Phương thức getHostAddress

Phương thức `getHostAddress()` trả về địa chỉ IP của đối tượng InetAddress dưới dạng chuỗi. Nó là phương thức thường được sử dụng khi bạn muốn biết địa chỉ IP của máy chủ hoặc thiết bị mà đối tượng InetAddress đang đại diện.

{{< highlight java >}}
public class GetHostAddressExample {
    public static void main(String[] args) {
        try {
            String hostName = "www.example.com";
            InetAddress inetAddress = InetAddress.getByName(hostName);

            // Lấy địa chỉ IP và hiển thị
            String ipAddress = inetAddress.getHostAddress();
            System.out.println("IP Address: " + ipAddress);
        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
    }
}
{{< /highlight >}}

# 7. Phương thức getByAddress trong lớp InetAddress

Phương thức `getByAddress()` trong lớp InetAddress được sử dụng để tạo một đối tượng InetAddress từ một địa chỉ IP được biểu diễn dưới dạng mảng byte.

{{< highlight java >}}
public class GetByAddressExample {
    public static void main(String[] args) {
        try {
            // Địa chỉ IP được biểu diễn dưới dạng mảng byte
            byte[] ipAddressBytes = {127, 0, 0, 1};

            // Sử dụng phương thức getByAddress để tạo đối tượng InetAddress
            InetAddress inetAddress = InetAddress.getByAddress(ipAddressBytes);

            // Hiển thị thông tin về đối tượng InetAddress
            System.out.println("Host Name: " + inetAddress.getHostName());
            System.out.println("Host Address: " + inetAddress.getHostAddress());
        } catch (UnknownHostException e) {
            // Xử lý nếu không thể tìm thấy địa chỉ IP hoặc tên máy chủ
            e.printStackTrace();
        }
    }
}
{{< /highlight >}}

![Alt text](/images/getbyaddress-inetaddress.jpg)

# 8. Ví dụ về việc sử dụng InetAddress trong Java

Viết chương trình nhập vào một tên miền. Yêu cầu:

- Hiển thị tên miền và địa chỉ IP của tên miền đó.
- Hiển thị IP đó thuộc lớp nào

***Code mẫu:***

{{< highlight java >}}
public static void main(String[] args) {
    INet domainSearch = new INet();
    domainSearch.DomainSearch();

    //
}
private void DomainSearch() {
    /*
    Viet chuong trinh nhap ten mien va cho biet ten mien do thuoc lop nao
     */
    String domain;

    Scanner sc = new Scanner(System.in);

    System.out.println("Nhap ten mien: ");
    domain = sc.nextLine();

    try {
        InetAddress inetAddress = InetAddress.getByName(domain);
        String ip = inetAddress.getHostAddress();
        System.out.println("Ten mien: " + domain);
        System.out.println("Dia chi IP: " + ip);

        byte[] b = inetAddress.getAddress();

        int i;

        if (b[0] >= 0) {
            i = b[0];
        } else {
            i = 256 + b[0];
        }

        System.out.println("Dia chi IP: " + i);

        if ((i >= 1) & (i <= 126)) {
            System.out.println(ip + " thuoc lop A");
        }

        if ((i <= 191) & (i >= 128)) {
            System.out.println(ip + " thuoc lop B");
        }

        if ((i <= 223) & (i >= 192)) {
            System.out.println(ip + " thuoc lop C");
        }
    } catch (Exception e) {
        e.printStackTrace();
        System.out.println("Khong tim thay dia chi IP" + e.getMessage());
    }
}
{{< /highlight >}}

***Kết quả chạy chương trình:***

![Alt text](/images/inetaddress-example.jpg)

# 9. Ứng dụng InetAddress trong phát triển mạng Java

InetAddress chủ yếu được sử dụng trong các ứng dụng mạng java để giải quyết tên miền và truy xuất địa chỉ IP. Nó cũng được dùng để thực hiện các kết nối mạng và truy cập dữ liệu từ máy chủ.

# 10. Đánh giá hiệu năng của InetAddress trong Java

InetAddress là lớp quan trọng trong việc xây dựng các ứng dụng mạng java. Hầu hết các ứng dụng mạng cần đến địa chỉ IP hoặc tên miền, do vậy việc sử dụng InetAddress là bắt buộc. Chất lượng và hiệu suất của InetAddress rất ổn định và tốt. Phần lớn, nó kiểm soát thời gian phản hồi của hệ thống và giảm thiểu thời gian chờ nhờ vào việc chuyển đổi hiệu quả giữa tên miền và địa chỉ IP. Nhưng cũng cần chú ý xử lý các ngoại lệ có thể xảy ra để đảm bảo ứng dụng mạng hoạt động một cách ổn định và hiệu quả.

Tổng kết lại, InetAddress trong Java đóng vai trò quan trọng trong việc xử lý mạng và liên lạc giữa các máy tính thông qua việc cung cấp các phương thức để tương tác với địa chỉ IP. Vì vậy, mỗi lập trình viên Java nên nắm vững kiến thức về InetAddress để tận dụng tốt nhất các tính năng mà nó mang lại.