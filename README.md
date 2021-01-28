# SPARK PROPERTIES (THUỘC TÍNH SPARK)

Thuộc tính Spark kiểm soát hầu hết các cài đặt ứng dụng và được cấu hình riêng cho từng ứng dụng. Các thuộc tính này có thể được đặt trực tiếp trên SparkConf được chuyển tới SparkContext của bạn. SparkConf cho phép bạn định cấu hình một số thuộc tính phổ biến (ví dụ: URL chính và tên ứng dụng), cũng như các cặp khóa-giá trị tùy ý thông qua phương thức set (). Ví dụ: chúng ta có thể khởi tạo một ứng dụng với hai luồng như sau:

Lưu ý rằng chúng tôi chạy với local [2], nghĩa là hai luồng - đại diện cho sự song song &quot;tối thiểu&quot;, có thể giúp phát hiện lỗi chỉ tồn tại khi chúng tôi chạy trong ngữ cảnh phân tán.

val conf = new SparkConf()

.setMaster(&quot;local[2]&quot;)

.setAppName(&quot;CountingSheep&quot;)

val sc = new SparkContext(conf)

Lưu ý rằng chúng ta có thể có nhiều hơn 1 luồng ở chế độ cục bộ và trong những trường hợp như Spark Streaming, chúng tôi thực sự có thể yêu cầu nhiều hơn 1 luồng để ngăn chặn bất kỳ loại vấn đề chết đói nào.

Các thuộc tính chỉ định một số khoảng thời gian nên được cấu hình với một đơn vị thời gian. Định dạng sau được chấp nhận:

25ms (milliseconds), 5s (seconds), 10m or 10min (minutes), 3h (hours), 5d (days), 1y (years).

Thuộc tính chỉ định kích thước byte phải được cấu hình với đơn vị kích thước. Định dạng sau được chấp nhận:

1b (bytes), 1k or 1kb (kibibytes = 1024 bytes), 1m or 1mb (mebibytes = 1024 kibibytes), 1g or 1gb (gibibytes = 1024 mebibytes), 1t or 1tb (tebibytes = 1024 gibibytes), 1p or 1pb (pebibytes = 1024 tebibytes)

Trong khi các số không có đơn vị thường được hiểu là byte, một số ít được hiểu là KiB hoặc MiB. Xem tài liệu về các thuộc tính cấu hình riêng lẻ. Việc chỉ định đơn vị là mong muốn nếu có thể.

## Nhiều cách để nạp thuộc tính Spark vào

Trong một số trường hợp, bạn có thể muốn tránh mã hóa cứng các cấu hình nhất định trong SparkConf. Ví dụ: nếu bạn muốn chạy cùng một ứng dụng với các bản chính khác nhau hoặc số lượng bộ nhớ khác nhau. Spark cho phép bạn chỉ cần tạo một conf trống:

valsc=newSparkContext(newSparkConf())

Sau đó, bạn có thể cung cấp các giá trị cấu hình trong thời gian chạy:

./bin/spark-submit --name&quot;My app&quot;--masterlocal[4] --conf spark.eventLog.enabled=false

--conf&quot;spark.executor.extraJavaOptions=-XX:+PrintGCDetails -XX:+PrintGCTimeStamps&quot; myApp.jar

Spark shell và công cụ spark-submit hỗ trợ hai cách để tải cấu hình động.Đầu tiên là các tùy chọn dòng lệnh, chẳng hạn như --master, như được hiển thị ở trên.spark-submit có thể chấp nhận bất kỳ thuộc tính Spark nào sử dụng --conf / -c flag, nhưng sử dụng cờ đặc biệt cho các thuộc tính đóng một vai trò trong việc khởi chạy ứng dụng Spark.Chạy ./bin/spark-submit --help sẽ hiển thị toàn bộ danh sách các tùy chọn này. bin / spark-submit cũng sẽ đọc các tùy chọn cấu hình từ conf / spark-defaults.conf, trong đó mỗi dòng bao gồm một khóa và một giá trị được phân tách bằng khoảng trắng.Ví dụ:

spark.master spark://5.6.7.8:7077

spark.executor.memory 4g

spark.eventLog.enabled true

spark.serializer org.apache.spark.serializer.KryoSerializer

Mọi giá trị được chỉ định dưới dạng cờ hoặc trong tệp thuộc tính sẽ được chuyển đến ứng dụng và được hợp nhất với những giá trị được chỉ định thông qua SparkConf. Các thuộc tính được đặt trực tiếp trên SparkConf được ưu tiên cao nhất, sau đó các cờ được chuyển đến spark-submit hoặc spark-shell, sau đó là các tùy chọn trong tệp spark-defaults.conf. Một vài khóa cấu hình đã được đổi tên kể từ các phiên bản Spark trước đó; trong những trường hợp như vậy, các tên khóa cũ hơn vẫn được chấp nhận, nhưng được ưu tiên thấp hơn bất kỳ trường hợp nào của khóa mới hơn.

Các thuộc tính của Spark chủ yếu có thể được chia thành hai loại: một là liên quan đến triển khai, như &quot;spark.driver.memory&quot;, &quot;spark.executor.instances&quot;, loại thuộc tính này có thể không bị ảnh hưởng khi thiết lập lập trình thông qua SparkConf trong thời gian chạy, hoặc hành vi tùy thuộc vào trình quản lý cụm và chế độ triển khai bạn chọn, vì vậy bạn nên đặt thông qua tệp cấu hình hoặc tùy chọn dòng lệnh spark-submit; một thứ khác chủ yếu liên quan đến kiểm soát thời gian chạy Spark, như &quot;spark.task.maxFailures&quot;, loại thuộc tính này có thể được đặt theo một trong hai cách.

## Xem thuộc tính Spark

Giao diện người dùng web ứng dụng tại http: // \&lt;driver\&gt;: 4040 liệt kê các thuộc tính Spark trong tab &quot;Môi trường&quot;. Đây là một nơi hữu ích để kiểm tra để đảm bảo rằng các thuộc tính của bạn đã được đặt chính xác. Lưu ý rằng chỉ các giá trị được chỉ định rõ ràng thông qua spark-defaults.conf, SparkConf hoặc dòng lệnh mới xuất hiện. Đối với tất cả các thuộc tính cấu hình khác, bạn có thể giả sử giá trị mặc định được sử dụng.

## Thuộc tính có sẵn

Hầu hết các thuộc tính kiểm soát cài đặt nội bộ đều có giá trị mặc định hợp lý. Một số tùy chọn phổ biến bạn có thể tham khảo chi tiết tại: [https://spark.apache.org/docs/latest/configuration.html#spark-properties](https://spark.apache.org/docs/latest/configuration.html#spark-properties)

# RDD LÀ GÌ?

Khi nói đến điện toán phân tán lặp, tức là xử lý dữ liệu qua nhiều công việc trong các tính toán như Hồi quy logistic, phân cụm K-nghĩa, thuật toán xếp hạng Trang, khá phổ biến để sử dụng lại hoặc chia sẻ dữ liệu giữa nhiều công việc hoặc bạn có thể muốn thực hiện nhiều công việc truy vấn đặc biệt trên một tập dữ liệu được chia sẻ.

Có một vấn đề tiềm ẩn với việc tái sử dụng dữ liệu hoặc chia sẻ dữ liệu trong các hệ thống máy tính phân tán hiện có (như MapReduce) và đó là, bạn cần lưu trữ dữ liệu trong một số cửa hàng phân tán ổn định trung gian như HDFS hoặc Amazon S3. Điều này làm cho việc tính toán tổng thể các công việc chậm hơn vì nó liên quan đến nhiều hoạt động IO, sao chép và tuần tự hóa trong quy trình.

![2](https://user-images.githubusercontent.com/47163776/106078820-a3e17980-6146-11eb-84b2-019dacb98815.jpg)

## Thực thi trên MapRedure

MapReduce được áp dụng rộng rãi để xử lý và tạo các bộ dữ liệu lớn với thuật toán xử lý phân tán song song trên một cụm. Nó cho phép người dùng viết các tính toán song song, sử dụng một tập hợp các toán tử cấp cao, mà không phải lo lắng về xử lý/phân phối công việc và khả năng chịu lỗi.

Tuy nhiên, trong hầu hết các framework hiện tại, cách duy nhất để sử dụng lại dữ liệu giữa các tính toán (Ví dụ: giữa hai công việc MapReduce) là ghi nó vào storage (Ví dụ: HDFS). Mặc dù framework này cung cấp nhiều hàm thư viện để truy cập vào tài nguyên tính toán của cụm Cluster, điều đó vẫn là chưa đủ.

Cả hai ứng dụng Lặp (Iterative) và Tương tác (Interactive) đều yêu cầu chia sẻ truy cập và xử lý dữ liệu nhanh hơn trên các công việc song song. Chia sẻ dữ liệu chậm trong MapReduce do sao chép tuần tự và tốc độ I/O của ổ đĩa. Về hệ thống lưu trữ, hầu hết các ứng dụng Hadoop, cần dành hơn 90% thời gian để thực hiện các thao tác đọc-ghi HDFS.

**- Iterative Operation trên MapReduce:**

![3](https://user-images.githubusercontent.com/47163776/106079081-210cee80-6147-11eb-95e2-28f9e1f75235.jpg)

**- Interactive Operations trên MapReduce:**

![4](https://user-images.githubusercontent.com/47163776/106079090-24a07580-6147-11eb-8f4a-c9bf6985b65d.jpg)

## Thực thi trên Spark RDD

Để khắc phục được vấn đề về MapRedure, các nhà nghiên cứu đã phát triển một framework chuyên biệt gọi là Apache Spark. Ý tưởng chính của Spark là Resilient Distributed Datasets (RDD); nó hỗ trợ tính toán xử lý trong bộ nhớ. Điều này có nghĩa, nó lưu trữ trạng thái của bộ nhớ dưới dạng một đối tượng trên các công việc và đối tượng có thể chia sẻ giữa các công việc đó. Việc xử lý dữ liệu trong bộ nhớ nhanh hơn 10 đến 100 lần so với network và disk.

**- Iterative Operation trên Spark RDD:**

![5](https://user-images.githubusercontent.com/47163776/106079201-5a455e80-6147-11eb-804e-abfae938799b.jpg)

**- Interactive Operations trên Spark RDD:**

![6](https://user-images.githubusercontent.com/47163776/106079210-5ca7b880-6147-11eb-966a-b6e31d897377.jpg)

## Các loại RDD

- Các RDD biểu diễn một tập hợp cố định, đã được phân vùng các record để có thể xử lý song song.
- Các record trong RDD có thể là đối tượng Java, Scale hay Python tùy lập trình viên chọn. Không giống như DataFrame, mỗi record của DataFrame phải là một dòng có cấu trúc chứa các field đã được định nghĩa sẵn.
- RDD đã từng là API chính được sử dụng trong series Spark 1.x và vẫn có thể sử dụng trong version 2.X nhưng không còn được dùng thường xuyên nữa.
- RDD API có thể được sử dụng trong Python, Scala hay Java:
  - Scala và Java: Perfomance tương đương trên hầu hết mọi phần. (Chi phí lớn nhất là khi xử lý các raw object)
  - Python: Mất một lượng performance, chủ yếu là cho việc serialization giữa tiến trình Python và JVM

## Các transformation và action với RDD

RDD cung cấp các transformation và action hoạt động giống như DataFrame lẫn DataSets. Transformation xử lý các thao tác lazily và Action xử lý thao tác cần xử lý tức thời.

![7](https://user-images.githubusercontent.com/47163776/106079279-806afe80-6147-11eb-887b-698302500c1d.jpg)

**- Một số transformation:**

Nhiều phiên bản transformation của RDD có thể hoạt động trên các Structured API, transformation xử lý lazily, tức là chỉ giúp dựng execution plans, dữ liệu chỉ được truy xuất thực sự khi thực hiện action

- **distinct** : loại bỏ trùng lắp trong RDD
- **filter** : tương đương với việc sử dụng where trong SQL – tìm các record trong RDD xem những phần tử nào thỏa điều kiện. Có thể cung cấp một hàm phức tạp sử dụng để filter các record cần thiết – Như trong Python, ta có thể sử dụng hàm lambda để truyền vào filter
- **map** : thực hiện một công việc nào đó trên toàn bộ RDD. Trong Python sử dụng lambda với từng phần tử để truyền vào map
- **flatMap** : cung cấp một hàm đơn giản hơn hàm map. Yêu cầu output của map phải là một structure có thể lặp và mở rộng được.
- **sortBy** : mô tả một hàm để trích xuất dữ liệu từ các object của RDD và thực hiện sort được từ đó.
- **randomSplit** : nhận một mảng trọng số và tạo một random seed, tách các RDD thành một mảng các RDD có số lượng chia theo trọng số.

**- Một số action:**

Action thực thi ngay các transformation đã được thiết lập để thu thập dữ liệu về driver để xử lý hoặc ghi dữ liệu xuống các công cụ lưu trữ.

- **reduce** : thực hiện hàm reduce trên RDD để thu về 1 giá trị duy nhất
- **count** : đếm số dòng trong RDD
- **countApprox:** phiên bản đếm xấp xỉ của count, nhưng phải cung cấp timeout vì có thể không nhận được kết quả.
- **countByValue** : đếm số giá trị của RDD
 chỉ sử dụng nếu map kết quả nhỏ vì tất cả dữ liệu sẽ được load lên memory của driver để tính toán
 chỉ nên sử dụng trong tình huống số dòng nhỏ và số lượng item khác nhau cũng nhỏ.
- **countApproxDistinct** : đếm xấp xỉ các giá trị khác nhau
- **countByValueApprox** : đếm xấp xỉ các giá trị
- **first** : lấy giá trị đầu tiên của dataset
- **max và min:** lần lượt lấy giá trị lớn nhất và nhỏ nhất của dataset
- **take và các method tương tự** : lấy một lượng giá trị từ trong RDD. take sẽ trước hết scan qua một partition và sử dụng kết quả để dự đoán số lượng partition cần phải lấy thêm để thỏa mãn số lượng lấy.
- **top và takeOrdered** : top sẽ hiệu quả hơn takeOrdered vì top lấy các giá trị đầu tiên được sắp xếp ngầm trong RDD.
- **takeSamples** : lấy một lượng giá trị ngẫu nhiên trong RDD

# **DATA FRAME**

Khung dữ liệu là một bảng hoặc cấu trúc giống như mảng hai chiều, trong mà mỗi cột chứa các phép đo trên một biến và mỗi hàng chứa một trường hợp.

Vì vậy, một DataFrame có siêu dữ liệu bổ sung do định dạng bảng của nó, cho phép Spark chạy một số tối ưu hóa nhất định trên truy vấn đã hoàn thành.

Mặt khác, RDD chỉ là một **R** esilient **D** istribution **D** ataset có nhiều hộp đen dữ liệu không thể được tối ưu hóa như các hoạt động có thể được thực hiện chống lại nó, không bị ràng buộc.

Tuy nhiên, bạn có thể chuyển từ DataFrame sang RDD thông qua phương thức rdd của nó và bạn có thể chuyển từ RDD sang DataFrame (nếu RDD ở định dạng bảng) thông qua phương thức toDF

**Nói chung** nên sử dụng DataFrame trong trường hợp có thể do tối ưu hóa truy vấn tích hợp.

Điều đầu tiên là DataFrame được phát triển từ SchemaRDD.

![8](https://user-images.githubusercontent.com/47163776/106079281-819c2b80-6147-11eb-8ad8-5f15117cc91c.jpg)

Có .. chuyển đổi giữa Dataframe và RDD là hoàn toàn có thể.

Dưới đây là một số đoạn mã mẫu.

- df.rdd là RDD[Row]

Dưới đây là một số tùy chọn để tạo dataframe.

- 1) yourrddOffrow.toDF chuyển đổi thành DataFrame.
- 2) Sử dụng createDataFrame của bối cảnh sql

val df = spark.createDataFrame(rddOfRow, schema)

import org.Apache.spark.sql.catalyst.ScalaReflection

val schema = ScalaReflection.schemaFor[YourScalacaseClass].dataType.asInstanceOf[StructType]

HOẶC sử dụng Encoders

import org.Apache.spark.sql.Encoders

val mySchema = Encoders.product[MyCaseClass].schema

như được mô tả bởi Schema cũng có thể được tạo bằng cách sử dụng StructType và StructField

val schema = new StructType()

.add(StructField(&quot;id&quot;, StringType, true))

.add(StructField(&quot;col1&quot;, DoubleType, true))

.add(StructField(&quot;col2&quot;, DoubleType, true)) etc...

![9](https://user-images.githubusercontent.com/47163776/106079406-ba3c0500-6147-11eb-8963-3375561371d0.jpg)

![10](https://user-images.githubusercontent.com/47163776/106079407-bb6d3200-6147-11eb-99e6-273440254089.jpg)

## Api Dataframe

Spark 1.3 đã giới thiệu API DataFrame mới như một phần của sáng kiến ​​Dự án Vonfram nhằm tìm cách cải thiện hiệu suất và khả năng mở rộng của Spark. API DataFrame giới thiệu khái niệm lược đồ để mô tả dữ liệu, cho phép Spark quản lý lược đồ và chỉ truyền dữ liệu giữa các nút, theo cách hiệu quả hơn nhiều so với sử dụng tuần tự hóa Java.

API DataFrame hoàn toàn khác với API RDD vì đây là API để xây dựng kế hoạch truy vấn quan hệ mà trình tối ưu hóa Spark Spark Catalyst có thể thực hiện. API là tự nhiên đối với các nhà phát triển đã quen thuộc với việc xây dựng kế hoạch truy vấn

_ **Ví dụ kiểu SQL:** _

df.filter(&quot;age \&gt; 21&quot;);

_ **Hạn chế:** _ Vì mã đang đề cập đến các thuộc tính dữ liệu theo tên, nên trình biên dịch không thể thực hiện được để bắt bất kỳ lỗi nào. Nếu tên thuộc tính không chính xác thì lỗi sẽ chỉ được phát hiện khi chạy, khi kế hoạch truy vấn được tạo.

Một nhược điểm khác với API DataFrame là nó rất trung tâm và trong khi nó hỗ trợ Java, sự hỗ trợ bị hạn chế.

Ví dụ: khi tạo DataFrame từ một đối tượng RDD hiện có của các đối tượng Java, trình tối ưu hóa Spark Spark Catalyst không thể suy ra lược đồ và giả định rằng bất kỳ đối tượng nào trong DataFrame đều thực hiện giao diện scala.Product. Scala case class hoạt động tốt vì họ thực hiện giao diện này.

## Các tính năng của khung dữ liệu: -

- **Bộ sưu tập đối tượng hàng phân tán:** DataFrame là tập hợp phân phối dữ liệu được sắp xếp thành các cột được đặt tên. Nó là khái niệm tương đương với một bảng trong cơ sở dữ liệu quan hệ, nhưng với tối ưu hóa phong phú hơn dưới mui xe.
- **Xử lý dữ liệu:** Xử lý các định dạng dữ liệu có cấu trúc và không cấu trúc (Avro, CSV, tìm kiếm đàn hồi và Cassandra) và hệ thống lưu trữ (HDFS, bảng Hive, MySQL, v.v. ). Nó có thể đọc và viết từ tất cả các nguồn dữ liệu khác nhau.
- **Tối ưu hóa bằng trình tối ưu hóa chất xúc tác:** Nó cung cấp năng lượng cho cả truy vấn SQL và API DataFrame. Dataframe sử dụng khung chuyển đổi cây xúc tác theo bốn giai đoạn,
- 1.Analyzing a logical plan to resolve references
- 2.Logical plan optimization
- 3.Physical planning
- 4.Code generation to compile parts of the query to Java bytecode.
- **Tương thích Hive:** Sử dụng Spark SQL, bạn có thể chạy các truy vấn Hive chưa sửa đổi trên kho Hive hiện tại của mình. Nó sử dụng lại Hive frontend và MetaStore và cung cấp cho bạn khả năng tương thích hoàn toàn với dữ liệu, truy vấn và UDF hiện có.
- **Vonfram:** Vonfram cung cấp một phụ trợ thực thi vật lý, nó sẽ quản lý bộ nhớ một cách tự động và tự động tạo mã byte để đánh giá biểu thức.
- **Ngôn ngữ lập trình được hỗ trợ:**
 [.\_\_.] API Dataframe có sẵn bằng Java, Scala, Python và R.

## Giới hạn khung dữ liệu: -

- **An toàn loại thời gian biên dịch:** Như đã thảo luận, API Dataframe không hỗ trợ biên dịch an toàn thời gian, điều này hạn chế bạn thao tác dữ liệu khi không biết cấu trúc. Ví dụ sau hoạt động trong thời gian biên dịch. Tuy nhiên, bạn sẽ nhận được một ngoại lệ Runtime khi thực thi mã này.

Thí dụ:

case class Person(name : String , age : Int)

val dataframe = sqlContext.read.json(&quot;people.json&quot;)

dataframe.filter(&quot;salary \&gt; 10000&quot;).show

=\&gt; throws Exception : cannot resolve &#39;salary&#39; given input age , name

Đây là một thách thức đặc biệt khi bạn đang làm việc với một số bước chuyển đổi và tổng hợp.

- **Không thể hoạt động trên đối tượng miền (đối tượng miền bị mất):** Khi bạn đã chuyển đổi một đối tượng miền thành dataframe, bạn không thể tạo lại nó từ nó. Trong ví dụ sau, một khi chúng ta đã tạo personDF từ personRDD, chúng ta đã thắng được lấy lại RDD gốc của lớp Person (RDD [Person]).

Thí dụ:

case class Person(name : String , age : Int)

val personRDD = sc.makeRDD(Seq(Person(&quot;A&quot;,10),Person(&quot;B&quot;,20)))

val personDF = sqlContext.createDataframe(personRDD)

personDF.rdd // returns RDD[Row] , does not returns RDD[Person]

## Các nguồn tham khảo:

[https://spark.apache.org/docs/latest/configuration.html#spark-properties](https://spark.apache.org/docs/latest/configuration.html#spark-properties)

[https://www.it-swarm-vi.com/vi/apache-spark/su-khac-biet-giua-dataframe-dataset-va-rdd-trong-spark/1054639227/](https://www.it-swarm-vi.com/vi/apache-spark/su-khac-biet-giua-dataframe-dataset-va-rdd-trong-spark/1054639227/)

[https://laptrinh.vn/books/apache-spark/page/apache-spark-rdd](https://laptrinh.vn/books/apache-spark/page/apache-spark-rdd)

[https://helpex.vn/article/rdd-trong-spark-la-gi-va-tai-sao-chung-ta-can-no-5c6afe5bae03f628d053a84c](https://helpex.vn/article/rdd-trong-spark-la-gi-va-tai-sao-chung-ta-can-no-5c6afe5bae03f628d053a84c)

