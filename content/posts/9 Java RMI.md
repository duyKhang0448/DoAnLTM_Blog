---
title: "9. Java RMI và ứng dụng phân tán đơn giản"
date: 2024-12-28T23:39:49+05:30
tags: []
type: "post"
image: "/images/lorem-ipsum/quick-fox.png"
showTableOfContents: false
---
# Khái niệm RMI

**RMI - Remote Method Invocation là một kĩ thuật cài đặt các đối tượng phân tán trong Java. RMI là một phần của bộ J2SDK và là hàm thư viện hỗ trợ các lời gọi phương thức từ xa và trả về giá trị cho các ứng dụng tính toán phân tán. Chúng ta giả sử rằng ngôn ngữ Java được sử dụng ở cả hai phía gọi và phía bên phương thức được gọi.**

*Để giải quyết một số vấn đề trong việc truyền thông giữa Client/Server. RMI không gọi trực tiếp mà thông qua lớp trung gian. Lớp này tồn tại ở cả hai phía Client và Server. Lớp ở máy Client gọi là Stub, lớp ở máy Server gọi là Skel (Skeletion).*

# Đặc tính của RMI

- RMI là mô hình đối tượng phân tán của Java, RMI giup cho việc giao tiếp giuẵ các đối tượng phân tán trong môi trường internet trể nên dễ dàng hơn.
- RMI là API bậc cao được xây dựng dựa trên lập trình Socket.
- RMI không những cho phép chúng ta truyền dữ liệu giữa các đối tượng trên các hệ thống máy tính khác nhau, mà còn triệu gọi các phương thức trong các đối tượng ở xa (Remote Object).
- Việc truyền dữ liệu giữa các máy khác nhau được xử lý một cách trong suốt bởi máy ảo Java (Java virtual machine).
- Tương tự như mô hình Client/Server, RMI vẫn lấy/duy trì khái niệm của Client và Server, tuy nhiên cách tiếp cận (approach) của RMI linh hoạt hơn, mềm dẻo hơn so với môt hình Client/Server.
- Một điều thuận lợi quan trong nhất của RMI là nó cung cấp cớ chế callbacks, nó cho phép Server triệu gọi các phương thức ở Client.

# RMI Architecture

- Remote interface: Nên extend từ java.rmi.remote. Nó khai báo tất cả các phương thức mà Client có thể triệu gọi. Tất cả các method trong interface này nên throw RemoteException
- Remote implementation: Được thực thi từ Remote interface và mở rộng từ UnicastRemoteObject. Triển khai các method được khai báo trong Interface tại đây. Nó là một Remote - - - Object thực sự. Phát sinh hai lớp trung gian Stub và Skel.
- Server class bao gồm:
- RMI registry: Bộ đăng kí này sẽ đăng kí một Remote object với Naming Registry. Giúp các Remote object được chấp nhận khi gọi các method từ xa.
- Các class thực thi trên server.
- Client class: Truy vấn trên tên Remote object trên RMI registry, thông qua stub để gọi các phương thức trên server.

![Alt text](/images/rmi-2.gif)

# Tạo một chương trình đơn giản sử dụng RMI

Ở đâyy tôi sẽ tạo một project đơn giản để các bạn hiểu thêm về cách thức khai báo cũng như remote của RMI.

- OS: Linux Ubuntu 15.04
- Java: version 1.7
- IDE: IntelliJ IDEA 15.0.3 (hoặc bất kỳ IDE nào các bạn cảm thấy tiện cho mình)

## Tạo mới một server để có thể remote từ xa

Đầu tiên chúng ta sẽ tạo một Interface để khai báo những method chúng ta sẽ sử dụng khi remote. Interface là `RMICalcul` và nó extends `Remote`

![Alt text](/images/9361ec5e-e3ad-40ce-ac37-0d8a1ff01727.webp)

{{< highlight java >}}
/**
 * Created by tranducliem on 3/26/16.
 */

import java.rmi.*;

public interface RMICalcul extends Remote {
    public int add (int a, int b) throws RemoteException;
    public int sub (int a, int b) throws RemoteException;
    public double convert (int f) throws RemoteException;
}
{{< /highlight >}}

Sau đó chúng ta tạo một class `RMICalculimpl` và implements 2 interface là `RMICalcul` và `Serializable`

*Lưu ý: RMICalcul là interface do chúng ta tạo ra còn Serializable là interface có sẵn trong java, bất cứ object nào muốn send qua môi trường mạng (internet) đều phải implements Serializable. Hiểu đơn giản thì interface Serializable sẽ có nhiệm vụ đóng băng trạng thái của object trước khi được gửi qua môi trường mạng.*

{{< highlight java >}}
import java.io.Serializable;
import java.rmi.RemoteException;

/**
 * Created by tranducliem on 3/26/16.
 */
public class RMICalculimpl implements RMICalcul, Serializable {
    @Override
    public int add(int a, int b) throws RemoteException {
        return a + b;
    }

    @Override
    public int sub(int a, int b) throws RemoteException {
        return a - b;
    }

    @Override
    public double convert(int f) throws RemoteException {
        return  ((f - 32)*5)/9;
    }
}
{{< /highlight >}}

## Tiếp theo chúng ta sẽ tạo một server có nhiệm vụ lắng nghe khi client call method

{{< highlight java >}}
/**
 * Created by tranducliem on 3/26/16.
 */

import java.rmi.*;

public class CalculServer {
    public static void main(String[] args){
        try{
            // Load the service
            RMICalculimpl calculService = new RMICalculimpl();
            String res = "rmi://localhost/RMICalcul";

            // Register with service
            Naming.rebind(res, calculService);
        } catch(Exception e){
            System.err.println("Error: " + e.getMessage());
        }
    }
}
{{< /highlight >}}

Đến đây chúng ta đã tạm thời hoàn thành phần tạo server để lắng nghe khi client gọi đến. Tuy nhiên để client có thể gọi được thì đăng ký trong registry như sau:

{{< highlight java >}}
rmiregistry &
{{< /highlight >}}

Sau khi đã đăng ký thì chúng ta sẽ genarate những file cần thiết cho việc remote đến server, sử dụng lệnh sau:

{{< highlight java >}}
rmic -vcompat RMICalculimpl
{{< /highlight >}}

hệ thống sẽ tự động sinh ra 2 file `RMICalculimpl_Skel.class` và `RMICalculimpl_Stub.class` ở project nào cần remote thì chúng ta sẽ import 2 file này vào. Ở đây tôi sẽ tạo class test trên cùng 1 project nên không cần copy

Sau khi đã tạo ra những file cần thiết cho việc chạy thì chúng ta sẽ start server bằng lệnh:

{{< highlight java >}}
java CalculServer
{{< /highlight >}}

Như vậy là server đã running và chờ đợi remote từ ứng dụng khác.

## Tạo mới một class Client để test server vừa tạo

Để test server remote vừa tạo tôi sẽ tạo một class `CalculClient` trong file `CalculClient.java` cùng thư mục hiện hành:

{{< highlight java >}}
/**
 * Created by tranducliem on 3/26/16.
 */

import java.rmi.*;

public class CalculClient {
    public static void main(String[] args){
        try{
            String res = "rmi://localhost/RMICalcul";
            // Lookup the service in the registry, and obtain a remote service
            Remote remoteService = Naming.lookup (res);

            // Cast to a RMICalcul Interface
            RMICalcul calculService = (RMICalcul)remoteService;

            // Call remote method
            System.out.println("Sum of 3 and 5 is: " + calculService.add(3,5));
            System.out.println("Sub of 5 and 3 is: " + calculService.sub(5,3));
            System.out.println("Convert 100 fahrenheit to Celsius: " + calculService.convert(100));
        } catch(Exception e){
            System.err.println("Error – " + e.getMessage());
        }
    }
}
{{< /highlight >}}

Sau khi tạo xong tôi compile file `CalculClient.java` bằng lệnh:

{{< highlight java >}}
javac CalculClient.java
{{< /highlight >}}

Sau khi compile xong thì hệ thống sẽ biên dịch code ra file `CalculClient.class` file này sẽ dùng để chạy

tất cả những file sau khi compile sẽ như sau:

![Alt text](/images/32140ae7-5bbc-4261-818a-fbe7bac17cf9.png)

để test chương trình chúng ta chạy lệnh sau trên terminal:

{{< highlight java >}}
java CalculClient
{{< /highlight >}}

Và chúng ta có kết qủa như sau:

![Alt text](/images/fbe03dd4-49e4-468a-8ce7-6b780c536719.png)

Như vậy chúng ta vừa tạo xong một ứng dụng đơn gỉản để remote mothod sử dụng RMI của Java. 