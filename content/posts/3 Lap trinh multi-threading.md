---
title: "3. Lập trình đa luồng trong Java (Java Multi-threading)"
date: 2024-04-15T23:39:49+05:30
tags: []
type: "post"
image: "/images/lorem-ipsum/quick-fox.png"
showTableOfContents: false
---
**Lập trình đa luồng** (Multi-threading) là một kỹ thuật trong lập trình mà một ứng dụng có thể thực thi nhiều phần công việc (luồng – thread) một cách đồng thời. Trong Java, đa luồng là một tính năng mạnh mẽ và quan trọng, giúp các ứng dụng có thể tận dụng tối đa tài nguyên CPU và giảm thời gian xử lý bằng cách thực thi nhiều tác vụ cùng lúc.

Java cung cấp hỗ trợ sẵn có cho lập trình đa luồng thông qua lớp `Thread` và giao diện `Runnable`. Việc sử dụng đa luồng trong Java giúp tăng cường hiệu suất và cải thiện tính đáp ứng (responsiveness) của ứng dụng, đặc biệt trong các ứng dụng yêu cầu xử lý song song hoặc thời gian thực.



## Khái niệm thread trong Java

### Thread là gì?

**Thread** (luồng) về cơ bản là một tiến trình con (sub-process). Một đơn vị xử lý nhỏ nhất của máy tính có thể thực hiện một công việc riêng biệt. Trong Java, các luồng được quản lý bởi máy ảo Java (JVM).

### Multi-thread là gì?

**Multi-thread** (đa luồng) là một tiến trình thực hiện nhiều luồng đồng thời. Một ứng dụng Java ngoài luồng chính có thể có các luồng khác thực thi đồng thời làm ứng dụng chạy nhanh và hiệu quả hơn.

VD: Trình duyệt web hay các chương trình chơi nhạc là 1 ví dụ điển hình về đa luồng.

+ Khi duyệt 1 trang web, có rất nhiều hình ảnh, CSS, javascript… được tải đồng thời bởi các luồng khác nhau.

+ Khi play nhạc, chúng ta vẫn có thể tương tác được với nút điều khiển như: Play, pause, next, back … vì luồng phát nhạc là luồng riêng biệt với luồng tiếp nhận tương tác của người dùng.

### Đa nhiệm (multitasking)

**Multitasking**: Là khả năng chạy đồng thời một hoặc nhiều chương trình cùng một lúc trên một hệ điều hành. Hệ điều hành quản lý việc này và sắp xếp lịch phù hợp cho các chương trình đó. Ví dụ, trên hệ điều hành Windows chúng ta có làm việc đồng thời với các chương trình khác nhau như: Microsoft Word, Excel, Media Player, …

Chúng ta sử dụng đa nhiệm để tận dụng tính năng của CPU.

Đa nhiệm có thể đạt được bằng hai cách:

1. Đa nhiệm dựa trên đơn tiến trình (Process) – Đa tiến trình (Multiprocessing).
   - Mỗi tiến trình có địa chỉ riêng trong bộ nhớ, tức là mỗi tiến trình phân bổ vùng nhớ riêng biệt.
   - Tiến trình là nặng.
   - Sự giao tiếp giữa các tiến trình có chi phí cao.
   - Chuyển đổi từ tiến trình này sang tiến trình khác đòi hỏi thời gian để đăng ký việc lưu và tải các bản đồ bộ nhớ, các danh sách cập nhật, …
2. Đa nhiệm dựa trên luồng (Thread) – Đa luồng (MultiThreading).
   - Các luồng chia sẻ không gian địa chỉ ô nhớ giống nhau.
   - Luồng là nhẹ.
   - Sự giao tiếp giữa các luồng có chi phí thấp.

Đa tiến trình (multiprocessing) và đa luồng (multithreading) cả hai được sử dụng để tạo ra hệ thống đa nhiệm (multitasking). Nhưng chúng ta sử dụng đa luồng nhiều hơn đa tiến trình bởi vì các luồng chia sẻ một vùng bộ nhớ chung. Chúng không phân bổ vùng bộ nhớ riêng biệt để tiết kiệm bộ nhớ, và chuyển đổi ngữ cảnh giữa các luồng mất ít thời gian hơn tiến trình.

## Ưu điểm của Multi-thread Java

Đầu tiên, đa luồng giúp **tăng hiệu suất** của ứng dụng bằng cách chạy song song nhiều tác vụ. Thay vì thực hiện tuần tự, các tác vụ có thể xử lý đồng thời, tận dụng tối đa CPU, đặc biệt trong môi trường đa lõi (multi-core). Mỗi luồng có thể dùng chung và chia sẻ nguồn tài nguyên trong quá trình chạy, nhưng có thể thực hiện một cách độc lập.

Đa luồng còn giúp **cải thiện tính đáp ứng** của ứng dụng. Trong các ứng dụng giao diện người dùng (UI), việc sử dụng đa luồng tránh tình trạng ứng dụng bị “treo” khi thực hiện các tác vụ dài, từ đó cải thiện trải nghiệm người dùng. Điều này rất quan trọng khi cần thực hiện các tác vụ nặng như tải dữ liệu hoặc xử lý I/O.

Ngoài ra, đa luồng hỗ trợ **xử lý đồng thời nhiều yêu cầu**. Đối với các hệ thống máy chủ hoặc ứng dụng web, đa luồng cho phép xử lý nhiều yêu cầu của người dùng cùng lúc, giảm thời gian chờ đợi và tăng khả năng phục vụ. Các luồng cũng chia sẻ tài nguyên bộ nhớ, giúp tiết kiệm tài nguyên so với việc tạo nhiều tiến trình riêng biệt.

## Nhược điểm của đa luồng trong Java

Tuy nhiên, lập trình đa luồng có một số nhược điểm lớn. **Độ phức tạp** của nó là một trong những thách thức lớn nhất. Khi làm việc với nhiều luồng, lập trình viên phải xử lý đồng bộ hóa, phối hợp luồng và tránh các lỗi như deadlock (tình trạng chết) hoặc race condition (điều kiện cạnh tranh). Điều này đòi hỏi kỹ năng cao và kinh nghiệm.

**Đồng bộ hóa dữ liệu** giữa các luồng cũng là một vấn đề quan trọng. Khi nhiều luồng truy cập chung một tài nguyên, nếu không đồng bộ đúng cách, dữ liệu có thể bị thay đổi ngoài ý muốn, dẫn đến lỗi. Đồng bộ hóa giúp ngăn chặn tình trạng này, nhưng nó làm giảm hiệu suất vì các luồng phải chờ nhau. Đồng thời, nếu đồng bộ không chính xác, có thể gây ra tình trạng **deadlock**, khi các luồng bị khóa lẫn nhau và không thể tiếp tục thực thi.

Một vấn đề khác là **race condition**, khi nhiều luồng cùng thao tác với một tài nguyên mà không kiểm soát đúng cách. Điều này có thể dẫn đến dữ liệu không chính xác hoặc bị hỏng.

Cuối cùng, lập trình đa luồng còn dẫn đến **tăng sử dụng tài nguyên**. Mỗi luồng yêu cầu bộ nhớ và CPU riêng, và khi có quá nhiều luồng, việc chuyển đổi giữa các luồng (context switching) làm giảm hiệu suất tổng thể của hệ thống.

## Vòng đời (các trạng thái) của một Thread trong Java

![Alt text](/images/multithread-lifecylce.jpg)

Vòng đời của thread trong java được kiểm soát bởi JVM. Java định nghĩa các trạng thái của luồng trong các thuộc tính static của lớp **Thread.State**:

- **NEW** : Đây là trạng thái khi luồng vừa được khởi tạo bằng phương thức khởi tạo của lớp Thread nhưng chưa được start(). Ở trạng thái này, luồng được tạo ra nhưng chưa được cấp phát tài nguyên và cũng chưa chạy. Nếu luồng đang ở trạng thái này mà ta gọi các phương thức ép buộc stop,resume,suspend … sẽ là nguyên nhân sảy ra ngoại lệ IllegalThreadStateException .
- **RUNNABLE** : Sau khi gọi phương thức start() thì luồng test đã được cấp phát tài nguyên và các lịch điều phối CPU cho luồng test cũng bắt đầu có hiệu lực. Ở đây, chúng ta dùng trạng thái là Runnable chứ không phải Running, vì luồng không thực sự luôn chạy mà tùy vào hệ thống mà có sự điều phối CPU khác nhau.
- **WAITING** : Thread chờ không giới hạn cho đến khi một luồng khác đánh thức nó.
- **TIMED_WAITING** : Thread chờ trong một thời gian nhất định, hoặc là có một luồng khác đánh thức nó.
- **BLOCKED** : Đây là 1 dạng của trạng thái “Not Runnable”, là trạng thái khi Thread vẫn còn sống, nhưng hiện tại không được chọn để chạy. Thread chờ một monitor để unlock một đối tượng mà nó cần.
- **TERMINATED** : Một thread ở trong trạng thái terminated hoặc dead khi phương thức run() của nó bị thoát.

## Cách tạo thread trong Java

Trong java ta có thể tạo ra một luồng bằng một trong hai cách sau: tạo 1 đối tượng của lớp được **extend** từ **class Thread** hoặc **implements** từ **interface Runnable**.

### Tạo luồng bằng cách extend từ lớp Thread

Để tạo luồng bằng cách tạo lớp kế thừa từ lớp Thread, ta phải làm các công việc sau :

1. Khai báo 1 lớp mới kế thừa từ lớp Thread
2. Override lại phương thức run ở lớp này, những gì trong phương thức run sẽ được thực thi khi luồng bắt đầu chạy. Sau khi luồng chạy xong tất cả các câu lệnh trong phương thức run thì luồng cũng tự hủy.
3. Tạo 1 thể hiện (hay 1 đối tượng) của lớp ta vừa khai báo.
4. Sau đó gọi phương thức start() của đối tượng này để bắt đầu thực thi luồng.

{{< highlight java >}}
package com.gpcoder.simple;

public class TheadSimple extends Thread {
    public void run() {
        System.out.println("thread is running...");
    }

    public static void main(String args[]) {
        TheadSimple t1 = new TheadSimple();
        t1.start();
    }
}
{{< /highlight >}}

**Lưu ý :**

- Tuy ta khai báo những công việc cần làm của luồng trong phương thức run() nhưng khi thực thi luồng ta phải gọi phương thức start(). Vì đây là phương thức đặc biệt mà java xây dựng sẵn trong lớp Thread, phương thức này sẽ cấp phát tài nguyên cho luồng mới rồi chạy phương thức run() ở luồng này. Vì vậy, nếu ta gọi phương thức run() mà không gọi start() thì cũng tương đương với việc gọi 1 phương thức của 1 đối tượng bình thường và phương thức vẫn chạy trên luồng mà gọi phương thức chứ không chạy ở luồng mới tạo ra, nên vẫn chỉ có 1 luồng chính làm việc chứ ứng dụng vẫn không phải là đa luồng.
- Sau khi start một thread, nó không bao giờ có thể được start lại. Nếu bạn làm như vậy, một ngoại lệ IllegalThreadStateException sẽ xảy ra.

### Tạo luồng bằng cách implement từ Interface Runnable

Để tạo luồng bằng cách hiện thực từ Interface Runnable, ta phải làm các công việc sau :

1. Khai báo 1 lớp mới implements từ Interface Runnable
2. Hiện thực phương thức run() ở lớp này, những gì trong phương thức run() sẽ được thực thi khi luồng bắt đầu chạy. Sau khi luồng chạy xong tất cả các câu lệnh trong phương thức run thì luồng cũng tự hủy.
3. Tạo 1 thể hiện (hay 1 đối tượng) của lớp ta vừa khai báo. (VD : Tên đối tượng là r1)
4. Tạo 1 thể hiện của lớp Thread bằng phương thức khởi tạo : Thread(Runnable target)
   - Runnable target: Là 1 đối tượng thuốc lớp được implements từ giao diện Runnable.
   - Ví dụ: Thread t1 = new Thread(r1);
5. Gọi phương thức start() của đối tượng t1.

{{< highlight java >}}
package com.gpcoder.simple;

public class RunnableSimple implements Runnable {
    public void run() {
        System.out.println("thread is running...");
    }

    public static void main(String args[]) {
        RunnableSimple runable = new RunnableSimple();
        Thread t1 = new Thread(runable);
        t1.start();
    }
}
{{< /highlight >}}

### Khi nào implements từ interface Runnable?

+ Cách hay được sử dụng và được yêu thích là dùng **interface Runnable**, bởi vì nó không yêu cầu phải tạo một lớp kế thừa từ lớp Thread. Trong trường hợp ứng dụng thiết kế yêu cầu sử dụng đa kế thừa, chỉ có interface mới có thể giúp giải quyết vấn đề. Ngoài ra, **Thread Pool** rất hiểu quả và có thể được cài đặt, sử dụng rất hơn giản.
+ Trong trường hợp còn lại ta có thể kế thừa từ lớp Thread.

Lập trình mạng (Network Programming) là hành động sử dụng mã máy tính để viết các chương trình hoặc quy trình có thể giao tiếp với các chương trình hoặc quy trình khác trên mạng. Các lập trình viên sử dụng các ngôn ngữ lập trình, thư viện mã và giao thức khác nhau để thực hiện công việc.

### Ví dụ minh họa sử dụng đa luồng

#### Ví dụ Tạo luồng bằng cách extend từ class Thread

Tạo luồng extend từ class Thead

{{< highlight java >}}
package com.gpcoder.flow;

public class ThreadDemo extends Thread {
    private Thread t;
    private String threadName;

    ThreadDemo(String name) {
        threadName = name;
        System.out.println("Creating " + threadName);
    }

    @Override
    public void run() {
        System.out.println("Running " + threadName);
        try {
            for (int i = 4; i > 0; i--) {
                System.out.println("Thread: " + threadName + ", " + i);
                // Let the thread sleep for a while.
                Thread.sleep(50);
            }
        } catch (InterruptedException e) {
            System.out.println("Thread " + threadName + " interrupted.");
        }
        System.out.println("Thread " + threadName + " exiting.");
    }

    public void start() {
        System.out.println("Starting " + threadName);
        if (t == null) {
            t = new Thread(this, threadName);
            t.start();
        }
    }

}
{{< /highlight >}}

Chương trình sử dụng đa luồng:

{{< highlight java >}}
package com.gpcoder.flow;

public class ThreadDemoTest {
    public static void main(String args[]) {
        System.out.println("Main thread running... ");

        ThreadDemo T1 = new ThreadDemo("Thread-1-HR-Database");
        T1.start();

        ThreadDemo T2 = new ThreadDemo("Thread-2-Send-Email");
        T2.start();

        System.out.println("==> Main thread stopped!!! ");
    }
}
{{< /highlight >}}

Kết quả thực thi chương trình trên:

{{< highlight java >}}
Main thread running...
Creating Thread-1-HR-Database
Starting Thread-1-HR-Database
Creating Thread-2-Send-Email
Starting Thread-2-Send-Email
==> Main thread stopped!!!
Running Thread-1-HR-Database
Running Thread-2-Send-Email
Thread: Thread-2-Send-Email, 4
Thread: Thread-1-HR-Database, 4
Thread: Thread-1-HR-Database, 3
Thread: Thread-2-Send-Email, 3
Thread: Thread-2-Send-Email, 2
Thread: Thread-1-HR-Database, 2
Thread: Thread-2-Send-Email, 1
Thread: Thread-1-HR-Database, 1
Thread Thread-2-Send-Email exiting.
Thread Thread-1-HR-Database exiting.
{{< /highlight >}}

Kết quả chương trình trên được giải thích thông qua hình bên dưới:

![Alt text](/images/Multi-Thread-Flow.png)

#### Ví dụ Tạo luồng bằng cách implement từ Interface Runnable

Tạo luồng implement từ Interface Runnable

{{< highlight java >}}
package com.gpcoder.flow;

class RunnableDemo implements Runnable {
    private Thread t;
    private String threadName;

    RunnableDemo(String name) {
        threadName = name;
        System.out.println("Creating " + threadName);
    }

    @Override
    public void run() {
        System.out.println("Running " + threadName);
        try {
            for (int i = 4; i > 0; i--) {
                System.out.println("Thread: " + threadName + ", " + i);
                // Let the thread sleep for a while.
                Thread.sleep(50);
            }
        } catch (InterruptedException e) {
            System.out.println("Thread " + threadName + " interrupted.");
        }
        System.out.println("Thread " + threadName + " exiting.");
    }

    public void start() {
        System.out.println("Starting " + threadName);
        if (t == null) {
            t = new Thread(this, threadName);
            t.start();
        }
    }

}
{{< /highlight >}}

Chương trình sử dụng đa luồng:

{{< highlight java >}}
package com.gpcoder.flow;

public class RunnableDemoTest {
    public static void main(String args[]) {
        System.out.println("Main thread running... ");

        RunnableDemo R1 = new RunnableDemo("Thread-1-HR-Database");
        R1.start();

        RunnableDemo R2 = new RunnableDemo("Thread-2-Send-Email");
        R2.start();

        System.out.println("==> Main thread stopped!!! ");
    }
}
{{< /highlight >}}

Kết quả thực thi chương trình trên:

{{< highlight java >}}
Main thread running...
Creating Thread-1-HR-Database
Starting Thread-1-HR-Database
Creating Thread-2-Send-Email
Starting Thread-2-Send-Email
==> Main thread stopped!!!
Running Thread-1-HR-Database
Running Thread-2-Send-Email
Thread: Thread-1-HR-Database, 4
Thread: Thread-2-Send-Email, 4
Thread: Thread-1-HR-Database, 3
Thread: Thread-2-Send-Email, 3
Thread: Thread-1-HR-Database, 2
Thread: Thread-2-Send-Email, 2
Thread: Thread-1-HR-Database, 1
Thread: Thread-2-Send-Email, 1
Thread Thread-1-HR-Database exiting.
Thread Thread-2-Send-Email exiting.
{{< /highlight >}}

Kết quả chương trình trên được giải thích thông qua hình bên dưới:

![Alt text](/images/Multi-Thread-Flow-Runnable.png)
