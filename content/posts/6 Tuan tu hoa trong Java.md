---
title: "6. Cách tuần tự hóa hoạt động trong Java"
date: 2024-05-25T23:39:49+05:30
tags: []
type: "post"
image: "/images/lorem-ipsum/quick-fox.png"
showTableOfContents: false
---
![Alt text](/images/800.webp)

# Giới thiệu

Tuần tự hóa đối tượng là khả năng một đối tượng lưu trữ một bản sao hoàn chỉnh của chính nó và bất kỳ đối tượng nào khác mà nó tham chiếu bằng cách sử dụng luồng đầu ra (ví dụ: vào tệp bên ngoài). Bằng cách này, đối tượng có thể được tạo lại từ bản sao được tuần tự hóa (đã lưu) sau đó một chút khi cần. Tuần tự hóa đối tượng, một tính năng mới được giới thiệu trong JDK 1.1, cung cấp chức năng chuyển đổi các nhóm hoặc đối tượng riêng lẻ thành dòng bit hoặc mảng byte để lưu trữ hoặc truyền qua mạng. Và như đã nêu, một luồng bit hoặc mảng byte nhất định có thể được chuyển đổi trở lại thành các đối tượng Java. Điều này chủ yếu xảy ra tự động nhờ vào `ObjectInputStream` và các lớp `ObjectOutputStream`. Người lập trình có thể quyết định triển khai chức năng này bằng cách triển khai giao diện `Serializable` khi tạo lớp. Quá trình tuần tự hóa còn được gọi là **sắp xếp** đối tượng , trong khi giải tuần tự hóa được gọi là **không sắp xếp thứ tự** . Tuần tự hóa là một cơ chế cho phép một đối tượng lưu một bản sao của chính nó và tất cả các đối tượng khác được đối tượng đó tham chiếu vào một tệp bên ngoài bằng cách sử dụng phần mở rộng `ObjectOutputStream`. Các đối tượng được lưu có thể là cấu trúc dữ liệu, sơ đồ, đối tượng lớp `JFrame` hoặc bất kỳ đối tượng nào khác, bất kể loại của chúng. Đồng thời, quá trình tuần tự hóa lưu trữ thông tin về loại đối tượng để sau này, khi được giải tuần tự hóa, thông tin đó sẽ được sử dụng để tạo lại loại đối tượng chính xác. Vì vậy, tuần tự hóa cung cấp các khả năng sau: 
- Một hệ thống lưu trữ các đối tượng, tức là: lưu các thuộc tính của chúng vào một tệp bên ngoài, vào đĩa hoặc vào cơ sở dữ liệu.
- Hệ thống gọi thủ tục từ xa.
- Ví dụ: một hệ thống phân phối đối tượng trong các thành phần phần mềm như COM, COBRA.
- Hệ thống xác định những thay đổi trong dữ liệu biến đổi theo thời gian.

Để hiểu đầy đủ về khái niệm tuần tự hóa, bạn cần hiểu rõ về hai khái niệm còn lại—sự bền vững của đối tượng và sự bền vững của luồng. Ở đây chúng ta sẽ nói một chút về từng người trong số họ để ghi nhớ. Một lời giải thích đầy đủ về chúng sẽ cần một chương riêng cho từng khái niệm này. 

# Dòng suối:

Mọi chương trình phải ghi dữ liệu của nó vào một vị trí lưu trữ hoặc đường ống và mọi chương trình phải đọc dữ liệu từ một đường ống hoặc vị trí lưu trữ. Trong Java, các kênh nơi chương trình ghi vào và từ đó chương trình đọc dữ liệu được gọi là **Luồng ( `Stream` )** 

![Alt text](/images/512.webp)

Luồng chủ yếu được chia thành hai loại: 
- Các lớp luồng byte được gọi là *Streams
- Các lớp dòng ký tự được gọi là *Reader và *Writer
Mỗi luồng ghi dữ liệu chứa một tập hợp các phương thức ghi. Và theo đó, mỗi luồng đọc dữ liệu có một bộ phương pháp đọc tương tự. Khi luồng được tạo, tất cả các phương thức này phải được gọi. 

# Kiên trì

Tính bền vững của đối tượng là khả năng của một đối tượng tồn tại hay nói cách khác là “sống sót” khi thực hiện một chương trình. Điều này có nghĩa là bất kỳ đối tượng nào được tạo trong thời gian chạy đều bị trình dọn rác JVM hủy bất cứ khi nào đối tượng đó không còn được sử dụng. Nhưng nếu API kiên trì được triển khai, các đối tượng này sẽ không bị trình quét JVM phá hủy, thay vào đó chúng sẽ được phép “hoạt động”, điều này cũng giúp bạn có thể truy cập chúng vào lần khởi chạy ứng dụng tiếp theo. Nói cách khác, tính bền vững có nghĩa là có một thời gian tồn tại cho một đối tượng, không phụ thuộc vào thời gian tồn tại của ứng dụng đang chạy. Một cách để triển khai tính bền vững là lưu trữ các đối tượng ở đâu đó trong tệp hoặc cơ sở dữ liệu bên ngoài, sau đó khôi phục chúng sau đó bằng cách sử dụng các tệp hoặc cơ sở dữ liệu đó làm nguồn. Đây là lúc việc tuần tự hóa phát huy tác dụng. Bất kỳ đối tượng không liên tục nào đều tồn tại miễn là JVM đang chạy. Các đối tượng được tuần tự hóa đơn giản là các đối tượng được chuyển đổi thành các luồng, sau đó được lưu vào một tệp bên ngoài hoặc được truyền qua mạng để lưu trữ và phục hồi. 

# Triển khai giao diện có thể tuần tự hóa

Bất kỳ lớp nào cũng phải triển khai một giao diện `java.io.Serializable` để tuần tự hóa các đối tượng của lớp đó. Giao diện `Serializable` không có phương thức và chỉ đánh dấu lớp để có thể xác định nó là có thể tuần tự hóa. Chỉ có thể lưu các trường của đối tượng lớp được tuần tự hóa. Các phương thức hoặc hàm tạo không được lưu trữ như một phần của luồng được tuần tự hóa. Nếu bất kỳ đối tượng nào hoạt động như một tham chiếu đến một đối tượng khác thì các trường của đối tượng đó cũng được tuần tự hóa nếu lớp của đối tượng đó triển khai giao diện `Serializable`. Nói cách khác, đồ thị của đối tượng này thu được hoàn toàn có thể tuần tự hóa. Biểu đồ đối tượng bao gồm một cây hoặc cấu trúc các trường của một đối tượng và các đối tượng con của nó. Hai lớp chính giúp triển khai giao diện `Seriliazable`: 
- `ObjectInputStream`
- `ObjectOutputStream`

*Liệt kê 1. Ví dụ về một lớp đơn giản để hiển thị tuần tự hóa*

{{< highlight java >}}
import java.io.*;
public class RandomClass implements Serializable {
 
 private static int r() {
        return (int)(Math.random() * 10);
 }
    private int data[];
    
public RandomClass() {
        datafile = new int[r()];
        for (int i=0; i<datafile.length; i++)
        datafile[i]=r();
 }
    public void printout() {
 System.out.println("This RandomClass has "+datafile.length+" random integers");
 for (int i=0; i<datafile.length; i++) {
        System.out.print(datafile[i]+":");
        System.out.println();
    }
}
{{< /highlight >}}

Trong đoạn mã trên, một lớp được tạo có thể tuần tự hóa được vì "được đánh dấu" bởi giao diện tuần tự hóa. Lớp tạo ra một mảng các số nguyên ngẫu nhiên khi một thể hiện của nó được tạo. Mã bên dưới cho thấy khả năng ghi đối tượng vào luồng bằng cách sử dụng `ObjectOutputStream`. Chương trình có một mảng các số nguyên, nhưng để tuần tự hóa, chúng ta không cần phải lặp lại các đối tượng bên trong của nó. Giao diện `Seriliazable` sẽ tự động xử lý việc này. 

*Liệt kê 2. Một ví dụ đơn giản về việc tuần tự hóa các đối tượng để xuất ra một tệp*

{{< highlight java >}}
import java.io.*;
import java.util.*;
public class OutSerialize {
    public static void main (String args[]) throws IOException {
        RandomClass rc1 = new RandomClass();
        RandomClass rc2 = new RandomClass();

ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("objects.dat"));
        Date now = new Date(System.currentTimeMillis());

        out.writeObject(now);
        out.writeObject(rc1);
        out.writeObject(rc2);
out.close();
        System.out.println("I have written:");
System.out.println("A Date object: "+now);
        System.out.println("Two Group of randoms");
rc1.printout();
rc2.printout();
 }
}
{{< /highlight >}}

 Mã bên dưới thể hiện khả năng của lớp `ObjectInputStream`, đọc dữ liệu được tuần tự hóa từ tệp bên ngoài vào chương trình. Lưu ý rằng các đối tượng được đọc theo cùng thứ tự mà chúng được ghi vào tệp. 
 
 *Liệt kê 3. Đọc các đối tượng được tuần tự hóa hoặc Giải tuần tự hóa*

{{< highlight java >}}
import java.io.*;
import java.util.*;
public class InSerialize {
 public static void main (String args[]) throws  IOException, ClassNotFoundException {
    ObjectInputStream in =  new ObjectInputStream (new FileInputStream("objects.dat"));
 Date d1 = (Date)in.readObject();
 RandomClass rc1 = (RandomClass)in.readObject();
    RandomClass rc2 = (RandomClass)in.readObject();
    System.out.println("I have read:");
    System.out.println("A Date object: "+d1);
    System.out.println("Two Group of randoms");
    rc1.printout();
rc2.printout();
 }
}
{{< /highlight >}}

 Hầu hết tất cả các lớp Java đều có thể được tuần tự hóa, bao gồm cả các lớp AWT. Một khung, là một cửa sổ, chứa một tập hợp các thành phần đồ họa. Nếu khung được tuần tự hóa, công cụ tuần tự hóa sẽ đảm nhiệm việc này và tuần tự hóa tất cả các thành phần và dữ liệu của nó (vị trí, nội dung, v.v.). Một số đối tượng lớp Java không thể được tuần tự hóa vì chúng chứa dữ liệu tham chiếu đến tài nguyên hệ điều hành tạm thời. Ví dụ: các lớp `java.io.FileInputStream` và `java.lang.Thread`. Nếu một đối tượng chứa các tham chiếu đến các phần tử không thể tuần tự hóa thì toàn bộ hoạt động tuần tự hóa sẽ thất bại và một ngoại lệ sẽ được đưa ra `NotSerializableException`. Nếu bất kỳ đối tượng nào tham chiếu đến một tham chiếu của một đối tượng chưa được tuần tự hóa thì nó có thể được tuần tự hóa bằng từ khóa tạm thời . 
 
 *Liệt kê 4. Tạo các đối tượng có thể tuần tự hóa bằng cách sử dụng từ khóa tạm thời*

 {{< highlight java >}}
 public class Sclass implements Serializable{
public transient Thread newThread;

    private String studentID;
    private int sum;
}
 {{< /highlight >}}

 # Bảo mật trong tuần tự hóa

 Tuần tự hóa một lớp trong Java liên quan đến việc chuyển tất cả dữ liệu của nó sang tệp hoặc cơ sở dữ liệu bên ngoài thông qua một luồng. Chúng tôi có thể giới hạn dữ liệu sẽ được tuần tự hóa bất cứ khi nào chúng tôi muốn. Có hai cách để làm điều này: 
- Mỗi tham số lớp được khai báo là tạm thời không được tuần tự hóa (theo mặc định, tất cả các tham số lớp được tuần tự hóa)
- Hoặc, mỗi tham số lớp mà chúng ta muốn tuần tự hóa sẽ được đánh dấu bằng một thẻ `Externalizable`(theo mặc định, không có tham số nào được tuần tự hóa).

Trường dữ liệu sẽ không được tuần tự hóa với `ObjectOutputStream`, khi được gọi trên một đối tượng, nếu trường dữ liệu của đối tượng đó được đánh dấu là **tạm thời** . Ví dụ: `private transient String password`. Mặt khác, để khai báo rõ ràng dữ liệu của một đối tượng là có thể tuần tự hóa, chúng ta phải đánh dấu lớp đó là ghi và đọc dữ liệu của đối tượng đó `ExternalizablewriteExternal` một cách rõ ràng `.readExteranl`