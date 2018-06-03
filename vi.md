# 12-factor

## Giới thiệu
Trong thời buổi hiện đại ngày nay, phần mềm thường được cung cấp dưới dạng dịch vụ và được gọi chung là các ứng dụng web hoặc "Phần mềm như một dịch vụ". Phần mềm tuân theo "12 quy chuẩn" là một phương pháp luận để xây dựng các ứng dụng dạng "Phần mềm như một dịch vụ" mà thỏa mãn các yếu tố sau:

- Sử dụng các định dạng được khai báo để phục vụ cho việc tự động hóa cài đặt. Giảm thiểu tối đa thời gian và chi phí để các nhà phát triển khác tham gia dự án;
- Có các quy ước rõ ràng với hệ điều hành bên dưới, tối ưu hóa tính di dộng giữa các môi trường thực thi.
- Phù hợp để triển khai trên các nền tảng đám mây hiện đại, giảm bớt yêu cầu từ server và các hệ thống quản trị.
- Giảm thiểu tối đa sự khác biệt giữa giai đoạn phát triển và thực thi, tạo điều kiện cho việc phát triển liên tục một cách nhanh nhất.
- Và có thể mở rộng mà không cần những thay đổi đáng kể liên quan tới công cụ, kiến trúc hoặc phong cách/thói quen phát triển.

Phương pháp luận "12 quy chuẩn" có thể áp dụng cho các ứng dụng viết bằng bất kỳ ngôn ngữ lập trình nào và sử dụng bất kỳ các dịch vụ nền nào (database, queue, memory cache, v.v).

## Cơ sở của bài viết
Các tác giả đóng góp cho tài liệu này đã trực tiếp tham gia vào các quá trình phát triển và triển khai của hàng trăm ứng dụng. Họ cũng gián tiếp chứng kiến quá trình phát triển, vận hành và mở rộng của hàng trăm nghìn ứng dụng thông qua nền tảng Heroku.

Tài liệu này tổng hợp tất cả các kinh nghiệm và quan sát của chúng tôi trên rất nhiều ứng dụng dạng "phần mềm như một dịch vụ". Nó bao gồm 3 yếu tố quan trọng mà bất kỳ quy trình phát triển ứng dụng lý tưởng nào cũng cần có. Nó bao gồm việc tập trung vào sự phát triển linh động của một ứng dụng theo thời gian, tính linh động trong việc cộng tác giữa các nhà phát triển trên nền code của ứng dụng, và tránh các chi phí phát sinh tạo ra bởi sự hao mòn của phần mềm.

Mục đích của chúng tôi là nâng cao nhận thức về những vấn đề có tính hệ thống mà chúng tôi đã chứng kiến trong việc phát triển các ứng dụng hiện đại. Ngoài ra, chúng tôi cũng mong muốn cung cấp các từ vựng chung để sử dụng trong việc bàn luận về những vấn đề này, và để mang đến một tập các giải pháp rộng lớn có tính khái niệm cho các vấn đề với các thuật ngữ tương ứng. Định dạng của tài liệu này được lấy cảm hứng từ cuốn sách của Martin Fowler, "Patterns of Enterprise Application Architecture and Refactoring".

## Ai nên đọc tài liệu này?
Bất kỳ nhà phát triển nào đang xây dựng các ứng dụng mà chạy như một dịch vụ. Các kĩ sư Ops triển khai hoặc quản lý các ứng dụng tương tự.

#### I. Nền code
###### Một nền code cần được theo dõi trong các hệ thống quản lý sửa đổi và sẽ có nhiều bản triển khai .

Một ứng dụng tuân theo "12 quy chuẩn" luôn cần được theo dõi trong một hệ thống quản lý sửa đổi như Git, Mercurial, hoặc Subversion. Một bản sao xét duyệt của cơ sở dữ liệu theo dõi được gọi là một code repository, thường được viết tắt là code repo hoặc repo.

Một nền code có thể là bất cứ repo đơn lẻ nào (trong một hệ thống quản lý sửa đổi phân quyền tập trung như Subversion), hoặc có thể là một tập bất kỳ các repo chia sẻ cùng một commit gốc (trong một hệ thống quản lý sửa đổi phân quyền phân cấp như Git). 

Luôn luôn có sự tương quan một-một giữa nền code và ứng dụng:
- Nếu có nhiều nền code thì đó không phải là một ứng dụng - nó là một hệ thống phân cấp. Mỗi thành phần là một hệ thống được phân cấp trong một ứng dụng và mỗi thành phần có thể tuân theo "12 quy chuẩn" một cách riêng biệt.
- Nhiều ứng dụng chia sẻ cùng đoạn code là một sự vi phạm trong "12 quy chuẩn". Giải pháp ở đây là quản lý các đoạn code được dùng chung thành các thư viện mà ta có thể sử dụng thông qua dependency manager.

Chỉ có duy nhất một nền code cho mỗi ứng dụng, nhưng sẽ có nhiều bản triển khải của ứng dụng. Một bản triển khai là một thực thể đang chạy của ứng dụng. Đây thường trang trong môi trường thực thi (production) và một hoặc nhiều phía trang staging. Ngoài ra, mỗi nhà phát triển có một bản sao của ứng dụng đang chạy trên môi trường phát triển nội bộ của họ, mỗi bản sao đó cũng đủ tiêu chuẩn để có thể triển khai thực tế.

Nền code là chung giữa tất cả các triển khai, mặc dù các phiên bản khác nhau có thể hoạt động hoặc không hoạt động trong mỗi triển khai. Ví dụ, một nhà phát triển có vài commit chưa triển khai đến staging, staging có vài commit chưa được triển khai tới production. Nhưng chúng đều dùng chung nền codebase, điều này làm chúng được phân biệt là các triển khai khác nhau của cùng một ứng dụng

#### II. Các phụ thuộc
###### Khai báo rõ ràng và tách biệt các phụ thuộc

Hầu hết các ngôn ngữ lập trình đều cung cấp một hệ thống đóng gói cho các thư viện có hỗ trợ việc phân phối như CPAN cho Perl hoặc Rubygems cho Ruby. Các thư viện được cài đặt thông qua hệ thống packaging có thể được cài đặt ở mức hệ thống ( được gọi là các “site package”) hoặc chỉ thu hẹp phạm vi trong thư mục chứa ứng dụng ( được gọi là “vendoring” hoặc “bundling”).

Một ứng dụng tuân theo "12 quy chuẩn" không bao giờ phụ thuộc vào sự tồn tại ngầm của các package ở mức hệ thống. Nó sẽ khai báo tất cả các phụ thuộc một hoàn thiện và chính xác thông qua một bản khai báo phụ thuộc. Ngoài ra, nó sử dụng một công cụ cô lập các phụ thuộc trong quá trình thực thi để đảm bảo rằng không có phụ thuộc ngầm nào “lọt vào”  từ các hệ thống xung quanh. Đặc tả phụ thuộc đầy đủ và chi tiết được áp dụng một cách thống nhất trong cả môi trường thực thi và phát triển.

Ví dụ, Bundler cho Ruby cung cấp định dạng biểu thị `Gemfile` cho việc khai báo các phụ thuộc và `bundle exec` cho việc cô lập phụ thuộc. Trong Python có 2 công cụ riêng biệt cho các bước này - Pip được sử dụng để khai báo và Virtualenv để cô lập. Thâm chí C có Autoconf dùng cho việc khai báo phụ thuộc và liên kết tĩnh có thể mang lại sự cô lập hóa các phụ thuộc. Bất kể tập công cụ nào được sử dụng, khai báo và cô lập phụ thuộc luôn phải được sử dụng cùng nhau - chỉ một là không đủ để thỏa mãn bộ 12 quy chuẩn".

Một lợi ích của việc khai báo phụ thuộc rõ ràng là nó đơn giản hóa công việc cài đặt cho những người phát triển mới làm quen với ứng dụng. Những nhà phát triển mới có thể lấy nền code của ứng dụng về máy của họ để phát triển, với điều kiện tiên quyết là ngôn ngữ runtime và trình quản lý phụ thuộc đã được cài đặt trước đó. Họ sẽ có thể cài đặt bất cứ thứ gì cần thiết để chạy code của ứng dụng với một lệnh build mà đã được xác định từ trước. Ví dụ, lệnh build cho Ruby/Bundler là `bundle install`, còn đối với Clojureojure/Leiningen là `lein dép`.

Các ứng dụng tuân theo bộ "12 quy chuẩn" cũng không phụ thuộc vào sự tồn tại ngầm của bất kỳ công cụ hệ thống nào. Ví dụ như công cụ hệ thống ImageMagick hoặc curl. Trong khi các công cụ này có thể tồn tại trên nhiều hay thậm chí là hầu hết các hệ thống, không có gì bảo đảm rằng chúng sẽ tồn tại trên tất cả các hệ thống mà ứng dụng có thể chạy trên trong tương lai, hoặc liệu một phiên bản được tìm thấy trong hệ thống tương lai có thể tương thích với ứng dụng hay không. Nếu ứng dụng cần một công cụ hệ thống, công cụ đó cần được gán vào ứng dụng.

#### III. Cấu hình
###### Lưu cấu hình trong môi trường

Cấu hình một ứng dụng là bất cứ thứ gì tạo ra sự khác biệt nằm trong các giai đoạn triển khai (môi trường staging, môi trường thực thi, môi trường nhà phát triển, v.v). Nó bao gồm:

- Tài nguyên xử lý cơ sở dữ liệu, Memcaches, và các dịch vụ hỗ trợ khác
- Các xác thực cho các dịch vụ bên ngoài như Amazon S3 hoặc Twitter
- Các giá trị của mỗi triển khai như là tên máy chủ của triển khai

Các ứng dụng đôi khi lưu trữ các cấu hình như các hằng trong mã nguồn. Điều này là một vi phạm trong bộ "12 quy chuẩn". Bộ quy chuẩn yêu cầu sự phân chia rõ ràng giữa cấu hình và mã nguồn. Về cơ bản, cấu hình thay đổi giữa các triển khai còn code thì không.

Một test để kiểm tra xem liệu ứng dụng đã có tất cả các cấu hình mà được tách biệt với code được cài đặt chính xác hay chưa là xác định xem liệu nền code có thể trở thành mã nguồn mở bất cứ lúc nào mà không ảnh hưởng đến bất cứ thông tin xác thực nào hay không.

Lưu ý rằng định nghĩa `cấu hình` không bao gồm các cấu hình nội bộ của ứng dụng, ví dụ như là `config/routes.rb` trong Rails, hoặc cách các module được kết nối trong Spring. Loại cấu hình này không thay đổi giữa các triển khai, và ví thế chúng là tốt nhất trong code.

Một cách tiếp cận khác với cấu hình là sử dụng các file cấu hình mà không được kiểm soát trong hệ thống quản lý sửa đổi như `config/database.yml` trong Rails. Đây là một cải thiện lớn so với việc sử dụng các hằng số được sử dụng trong code repo, tuy nhiên chúng vẫn còn những nhược điểm: việc thêm một file config vào repo là việc rất dễ xảy ra, đôi khi dễ xảy ra việc các file cấu hình nằm rải rác ở những chỗ khác nhau và có những định dạng khác nhau. Điều này tạo ra khó khăn để thấy và kiểm soát tất cả các cấu hình trong cùng một chỗ. Hơn nữa những định dạng này có thường sẽ khác biệt đối với từng ngôn ngữ hay framework.

Ứng dụng tuân theo bộ "12 quy chuẩn" lưu trữ cấu trình trong các biến môi trường (thường đọc tắt là env vars hoặc env). Env vars có thể dễ dàng thay đổi giữa các triển khai mà không phải thay đổi code; không giống như các file cấu hình, khả năng chúng được thêm vào code repo một cách vô tình là rất thấp; và không giống như các file cấu hình thông thường, các cơ chế cấu hình khác như Java System Properties, chúng là các chuẩn ngôn ngữ và OS-agnostic.

Một khía cạnh khác của quản lý cấu hình là gom nhóm. Đôi khi các ứng dụng gom cấu hình thành các tên nhóm (thường gọi là “các môi trường”) và được đặt tên dựa trên các triển khai cụ thể như môi trường `development`, `test`, và `production` trong Rails. Phương pháp này sẽ không được gọn khi mở rộng: vì khi có nhiều triển khai của ứng dụng được tạo ra, các tên môi trường mới cũng sẽ cần được tạo như `staging` hoặc `qa`. Khi dự án sẽ càng phát triển hơn nữa, các nhà phát triển có thể sẽ thêm các môi trường đặc biệt riêng của họ như `joes-staging`, điều này tạo ra một kết hợp các cấu hình khổng lồ làm cho việc quản lý các triển khai của ứng dụng trở nên vô cùng dễ khó khăn và dễ xảy ra lỗi.

Trong các ứng dụng tuân theo bộ "12 quy chuẩn", env vars đóng vai trò là các điều khiển, mỗi env vars liên kết đầy đủ với các env vars khác. Chúng không vào giờ được nhóm lại với nhau như là các "môi trường", nhưng thay vào đó chúng quản lý riêng biệt cho từng triển khai. Đây là một mô hình tạo nên sự mượt mà khi nâng cấp hay mở rộng vì ứng dụng tự mở rộng thành các triển khai khác trong vòng đời của nó.

#### IV. Dịch vụ nền
###### Coi và sử dụng các dịch vụ nền như các tài nguyên đính kèm

Một dịch vụ nền là bất cứ dịch vụ nào mà ứng dụng sử dụng nó trong mạng kết nối như một phần của quá trình vận hành thông thường. Ví dụ bao gồm các kho dữ liệu (như MySQL hoặc CouchDB), hệ thống truyền tin/hàng đợi (như RabbitMQ hoặc Beanstalkd), các dịch vụ SMTP cho phép gửi email(như Postfix) và các hệ thống caching (như Memcaches).

Thông thường, các hệ thống lưu trữ như cơ sở dữ liệu và các triển khai runtime của ứng dụng sẽ được quản lý bởi cùng hệ thống quản trị. Ngoài các dịch vụ được quản lý có tính cục bộ này, ứng dụng còn có thể có các dịch vụ được cung cấp và quản lý bởi các bên thứ ba. Ví dụ bao gồm các dịch vụ SMTP (như Postmark), các dịch vụ thu thập ma trận ( như là New Relic hay Loggly), các dịch vụ lưu trữ nhị phân (như là Amazon S3), và thậm chí các dịch vụ sử dụng các API có thể truy cập (như là Twitter, Google Maps, hay Last.fm).

Quy tắc cho ứng dụng tuân theo bộ 12 quy chuẩn không phân biệt các dịch vụ cục bộ hay các dịch vụ cung cấp bởi bên thứ ba. Đối với ứng dụng, cả hai đều là các tài nguyên đi kèm, được truy cập thông qua một URL hay các định vị/ dữ liệu xác thực khác được lưu trữ trong cấu hình. Một triển khai của ứng dụng tuân theo bộ 12 quy chuẩn nên có khả năng cho phép thay thế cơ sở dữ liệu MySQL cục bộ bằng một hệ thống được quản lý bởi một bên thứ ba (như là Amazon RDS) mà không yêu cầu bất kỳ thay đổi nào trong code của ứng dụng. Tương tự, 1 máy chủ SMTP cục bộ có thể được thay thế với một dịch vụ SMTP được cung cấp bởi bên thứ ba (như Postmark) mà không phải thay đổi code. Trong cả 2 trường hợp, chỉ có các tài nguyên xử lý trong cấu hình cần được thay đổi.

Mỗi dịch vụ nền là một tài nguyên. Ví dụ, một cơ sở dữ liệu MySQL là một tài nguyên, hai cơ sở dữ liệu MySQL (sử dụng phân chia tại tầng ứng dụng) được coi là hai tài nguyên riêng biệt. Ứng dụng tuân theo bộ 12 quy chuẩn coi các cơ sở dữ liệu này như là các tài nguyên đi kèm, điều này biểu thị sự phụ thuộc không chặt chẽ (loose coupling) của chúng đối với triển khai mà chúng đi kèm.

Các tài nguyên có thể được gắn kèm cũng như được tách rời khỏi triển khai một cách tùy ý. Ví dụ, nếu cơ sở dữ liệu của ứng dụng hoạt động lỗi do các vấn đề bởi phần cứng, người quản trị ứng dụng có thể tạo một máy chủ cơ sở dữ liệu mới được khôi phục từ lần backup gần nhất. Cơ sở dữ liệu production hiện tại có thể được gỡ bỏ, và thay thế bởi cơ sở dữ liệu mới được thêm vào - tất cả đều không yêu cầu bất kỳ sự thay đổi nào về code.

#### V. Build, release, run (Xây dựng, phát hành, chạy)
###### Phân tách rõ ràng giai đoạn xây dựng và chạy

Một codebase được chuyển thành một triển khai(không phải bản developement) thông qua ba giai đoạn:

- Giai đoạn xây dựng là một quá trình chuyển đổi mà nó chuyển hóa một code repo thành một tập các thực thi, được gọi là build. Sử dụng một phiên bản code tại một commit cụ thể bởi quá trình triển khai, bước xây dựng sẽ lấy về các phụ thuộc của vendors và biên dịch các file nhị phân và asets.
- Giai đoạn phát hành sẽ sử dụng các tập thực thi mà được tạo ra từ giai đoạn xây dựng và kết hợp chúng với cấu hình triển khai hiện tại. Kết quả của bản phát hành sẽ bảo gồm cả các tập thực thi và cấu hình và nó đã sẵn sàng cho việc thực thi ngay lập tức trong môi trường thực thi..
- Giai đoạn chạy (cũng được gọi là "thời gian chạy") chạy ứng dụng trong môi trường thực thi, bằng cách chạy một tập các tiến trình của ứng dụng đối với một bản phát hành được chỉ định.

Ứng dụng tuân theo bộ 12 quy chuẩn sử dụng tách biệt rõ ràng giữa các giai đoạn xây dựng, phát hành và chạy. Ví dụ, ta không sửa đổi code trong thời gian chạy, vì ta không có cách nào để truyền những thay đổi đó trở về giai đoạn xây dựng cả.

Các công cụ triển khai thường sẽ cung cấp các công cụ quản lý phát hành, đáng chú ý nhất là khả năng có thể roll back về bản phát hành trước đó. Ví dụ công cụ triển khai Capistrano lưu các bản phát hành trong một thư mục con gọi là `releases`, tại đó, bản phát hành hiện tại là một liên kết tượng trưng tới thư mục phát hành hiện tại. Lệnh `rollback` cho phép roll back về bản phát hành trước một cách dễ dàng.

Mỗi bản phát hành nên có một ID phát hành duy nhất, như là mốc thời gian của bản phát hành (ví dụ `2011-04-06-20:32:17`) hay một số tự tăng (như `v100`). Các bản phát hành có thể được hình dung như là các bản ghi được thêm cuối vào một sổ cái và một bản phát hành không thể chỉnh sửa khi nó đã được tạo ra. Bất kỳ thay đổi nào đều phải tạo ra một bản phát hành mới.

Các bản xây dựng sẽ được khởi tạo bởi nhà phát triển ứng dụng mỗi khi code mới được triển khai. Ngược lại, thời gian thực thi chạy có thể tự động xảy ra trong một số trường hợp như khi server khởi động lại, hoặc một tiến trình crash được khởi động lại bởi trình quản lý tiến trình. Vì thế, giai đoạn chạy cần hạn chế càng nhiều các thành phần di chuyển càng tốt, vì các vấn đề làm ngăn chặn việc hoạt động ứng dụng có thể xảy ra bất kỳ lúc và làm hỏng hệ thống khi không có developer sẵn sàng. Quá trình xây dựng có thể phức tạp hơn, vì các lỗi luôn có thể xảy ra đối với một nhà phát triển đang thực hiện việc triển khai.

#### VI. Các tiến trình
###### Thực thi ứng dụng thành dạng một hay nhiều các tiến trình không trạng thái

Ứng dụng được thực thi trong môi trường thực thi bằng một hoặc nhiều các tiến trình.

Trong trường hợp đơn giản nhất, code là một script độc lập, môi trường thực thi là laptop cục bộ của developer với ngôn ngữ thời gian chạy đã được cài đặt, và tiến trình được thực hiện thông qua dòng lệnh (ví dụ, `python my_script.py`). Mặt khác, một triển khai production của một ứng dụng phức tạp có thể sử nhiều loại tiến trình, khởi tạo từ 0 hoặc có thể có nhiều hơn các tiến trình chạy.

Các tiến trình tuân theo bộ 12 quy chuẩn có dạng không trạng thái và không chia sẻ. Bất kỳ dữ liệu nào cần lưu trữ đều phải được lưu trong một dịch vụ lưu trữ có trạng thái, thường là một cơ sở dữ liệu.

Bộ nhớ hoặc filesystem của tiến trình có thể được sử dụng như là một cache nhỏ gọn, đơn chiều. Ví dụ, việc tải một file lớn, thao tác trên đó, và lưu các kết quả sau khi thao tác vào trong cơ sở dữ liệu. Ứng dụng tuân theo bộ 12 quy chuẩn không bao giờ giả định bất cứ thứ gì được cache trong bộ nhớ hay trên đĩa sẽ khả dụng trong công việc trong tương lai - với nhiều tiến trình của mỗi loại đang chạy, khả năng cao là một yêu cầu trong tương lai sẽ được phục vụ bởi một tiến trình khác. Ngay cả khi đang chạy duy nhất một tiến trình, một restart (kích hoạt bởi triển khai của code hoặc thay đổi của cấu hình hay môi trường thực thi chuyển tiến trình sang một vị trí vật lí khác) thường sẽ xóa sạch tất cả trạng thái cục bộ (ví dụ như bộ nhớ và filesystem).

Các đóng gói asset như django-asetpackager sử dụng filesystem như là bộ nhớ đệm cho các asset được biên dịch. Một ứng dụng tuân theo bộ 12 quy chuẩn có xu hướng thực hiện việc biên dịch này trong suốt giai đoạn xây dựng. Các trình đóng gói asset như Jammit và asset pipeline Rails có thể được cấu hình thành các asset package trong giai đoạn xây dựng.

Một vài hệ thống web phụ thuộc vào các “sticky sessions” - đó là việc caching các dữ liệu của phiên của người trong bộ nhớ của tiến trình của ứng dụng và đợi các yêu cầu trong tương lai đền từ cùng một người sẽ được điều hướng tới cùng một tiến trình. Sticky sesions là một vi phạm của bộ 12 quy chuẩn và không bao giờ nên sử dụng hoặc phụ thuộc vào nó. Các dữ liệu trang thái phiên là một gỉai pháp thay thế tốt cho các kho dữ liệu mà trong hệ thống có quy định giới hạn về thời gian, như là Memcaches hay Redis.


#### VII. Kết nối cổng
###### Cung cấp các dịch vụ thông qua cổng kết nối

Các ứng dụng web đôi khi được thực thi bên trong webserver container. Ví dụ, các ứng dụng php có thể chạy như là một module bên trong Apache HTTPD, hay các ứng dụng Java có thể chạy bên trong Tomcat.

Ứng theo tuân theo bộ 12 quy chuẩn hoàn toàn độc lập và không phụ thuộc vào các runtime injection của 1 webserver trong môi trường thực thi để tạo ra một dịch vụ web-facing. Ứng dụng web cung cấp HTTP như một dịch vụ bằng cách kết nối với một cổng, và lắng nghe các yêu cầu tới từ cổng đó.

Trong môi trường phát triển cục bộ, các developer truy cập vào một URL dạng `http://localhost:5000/` để sử dụng các dịch vụ cung cấp bởi ứng dụng của họ. Trong triển khai, tầng điều hướng xử lý các yêu cầu điều hướng từ một tên miền có tính public đến các tiến trình web được gắn liền với cổng.

Điều này thường được cài đặt bằng các sử dụng các khai báo phụ thuộc để thêm một thư viện webserver vào ứng dụng, như là Tornado cho Python, Thin cho Ruby hoặc Jetty cho Java và các ngôn ngữ chuẩn JVM khác. Điều này xảy ra hoàn toàn trong không gian người dùng, đó là, bên trong code của ứng dụng. Các quy ước với môi trương thực thi được kết nối tới một cổng để xử lý các yêu cầu.

HTTP không phải dịch vụ duy nhất có thể được cung cấp bằng cách kết nối qua cổng. Gần đây thì bất kỳ loại phần mềm máy chủ nào cũng có thể chạy thông qua một tiến trình kết nối với một cổng và đợi các yêu cầu. Các ví dụ bao gồm ejabberd (XMPP), và Redis (giao thức Redis).

Cần lưu ý rằng phương pháp kết nối cổng cũng có nghĩa là một ứng dụng có thể trở thành một dịch vụ nền cho một ứng dụng khác, bằng cách cung cấp URL cho ứng dụng nền như một tài nguyên để xử lý trong cấu hình dành cho ứng dụng sử dụng.

#### VIII. Xử lý đồng thời
###### Mở rộng thông qua mô hình tiến trình

Bất kỳ chương trình máy tính nào, khi đã chạy, được đại diện bởi một hoặc nhiều hơn các tiến trình. Các ứng dụng web có rất nhiều hình thức thực thi tiến tình. Ví dụ, các tiến trình PHP chạy như các tiến trình con của Apache, được khởi tạo theo yêu cầu tầm quan trọng theo số lượng của lượng request. Các tiến trình Java lại có một cách tiếp cận đối ngược, với  JVM cung cấp một uberprocess lớn lưu trữ khối tài nguyên hệ thống (CPU và bộ nhớ) lúc khởi động, với xử lý đồng thời được quản lý nội bộ thông qua các luồng. Trong cả 2 trường hợp, các tiến trình chạy chỉ hiển thị một cách tối giản tới các người lập trình của ứng dụng.

Trong bộ 12 quy chuẩn, các tiến trình là các "công dân ở lớp đầu tiên". Các tiến trình trong ứng dụng tuân theo bộ 12 quy chuẩn tuân theo mô hình tiến trình unix để chạy các dịch vụ nền (daemons). Sử dụng mô hình này, developer có thể thiết kế ứng dụng của họ để xử lý các khối lượng công việc khác nhau bằng cách giao mỗi loại công việc cho một loại tiến trình. Ví dụ các yêu cầu HTTP có thể được xử lý bằng một tiến trình web, và các công việc nền tốn nhiều thời gian được xử lý bởi các tiến trình worker.

Điều này không loại trừ các tiến trình độc lập khỏi việc xử lý các kênh ghép nội bộ của chúng, thông qua các luồng bên trong thời gian chạy của VM, hoặc mô hình bất đồng bộ/sự kiện được tìm thấy trong các công cụ như là EventMachine, Twisted, hay Node.js. Nhưng một VM độc lập có thể mở rộng rất lớn (mở rộng theo chiều dọc), do vậy ứng dụng cũng phải có khả năng cung cấp nhiều tiến trình chạy trên nhiều máy vật lý.

Mô hình tiến trình thực sự hiệu quả khi ta quan tâm đến vấn đề mở rộng. Nguyên tắc không chia sẻ, có thể phân chia theo chiều ngang của các tiến tình tuân theo bộ 12 quy chuẩn có nghĩa là thêm nhiều xử lý đồng thời là một hoạt động đơn giản và đáng tin cậy, ổn định. Mảng các loại tiến trình và số lượng tiến trình của mỗi loại được gọi là hệ thống tiến trình.

Các tiến trình của ứng dụng tuân theo bộ 12 quy chuẩn nền không bao giờ được chạy nền hóa hoặc viết ra các file PID. Thay vào đó, sử dụng trình quản lý tiến trình của hệ điều hành (như là systemd, một trình quản lý tiến trình phân tán trên nền tảng đám mây, hay một công cụ như Foreman trong development) để quản lý các luồng ra, các phản hồi đối với các tiến trình lỗi, và xử lý các restart và shutdown được tạo bởi người dùng.


#### IX. Tính khả dụng
###### Tối đa hóa sức mạnh của ứng dụng bằng cách áp dụng fast startup và graceful shutdown (khởi động nhanh và tắt hợp lý)

Các process của ứng dụng tuân theo bộ 12 quy chuẩn phải có tính khả dụng, nghĩa là chúng có thể khởi động hoặc kết thúc tại bất kỳ thời điểm nào. Điều này tạo điều kiện cho elastic scaling (mở rộng một cách mềm dẻo), gíup việc deploy code và các config diễn ra nhanh chóng, và tạo ra sự chắc chắn, ổn định của các production deploy.

Các process cần giảm thiểu thời gian khởi động. Lý tưởng nhất là một process chỉ nên mất vài giây kể từ lúc câu lệnh được thực thi cho tới khi process đã ở trạng thái chạy và sẵn sàng tiếp nhận các yêu cầu. Thời gian khởi động ngắn mang lại sự nhanh chóng và ổn định trong quá trình release và mở rộng (scaling up) vì trình quản lý process có thể chuyển các process đến các máy vật lý mới dễ dàng hơn khi nó đã được đảm bảo.

Các process được gracefully shutdown khi chúng nhận được một tín hiệu `SIGTERM` từ trình quản lý process. Với một tiến trình web, graceful shutdown đạt được bằng cách dừng việc lắng nghe các service port (do đó nó sẽ từ chối bất cứ yêu cầu mới nào), cho phép bất cứ yêu cầu hiện tại nào được hoàn thành rồi thoát. Ý tưởng trong mô hình này là thời gian của các HTTP request sẽ ngắn(không nhiều hơn vài giây), hoặc trong trường thời gian là dài, client nên liên tục cố gắng kết nối lại khi kết nối bị mất.

Với một worker process, graceful shutdown đạt được bằng cách trả công việc hiện tại về work queue. Ví dụ, trên RabbitMQ, worker có thể gửi một `NACK`; trên Beanstalkd, công việc tự động trả về quêu mỗi khi một worker ngắt kết nối. Các hệ thống dựa trên khóa (lock-based) như Delayed Job cần đảm bảo việc release khóa trên công việc hiện tại. Ý tưởng trong mô hình này là tất cả các công việc có thể gọi lại, điều này có thể đạt được bằng cách gom các kết quả trong một giao dịch lại, hoặc khiến tiến trình trở nên bất biến.

Các process cũng cần có tính ổn định trước những sự cố bất ngờ như trong trường hợp lỗi hỏng gây ra do phần cứng. Dù đây thường là điều ít xảy ra hơn so với một graceful shutdown với `SIGTERM`, tuy nhiên nó vẫn có khả năng xảy ra. Một cách tiếp cận được đề xuất là sử dụng một hệ thống queueing backend mạnh mẽ như Beanstalkd, nó sẽ trả các công việc về hàng chờ khi client bị ngắt kết nối hoặc time out. Dù cho sử dụng cách nào, một ứng dụng tuân theo bộ 12 quy chuẩn được thiết kế để xử lý những kết thúc (terminators) khó đoán và xấu xí. Các thiết kế mà luôn bị crash đưa khái niệm này vào kết luận logic của nó.

#### X. Dev/prod parity (Sự tương đồng trong giai đoạn dev/prod)
###### Duy trì sự tương đồng giữa giai đoạn development, staging, và production

Từ trước đến nay, luôn tồn tại khoảng cách nhất định giữa giai đoạn development (một developer tạo các chỉnh sửa trực tiếp tới các deploy cục bộ của ứng dụng) và production (một triển khai đang chạy của ứng dụng được truy cập bởi các người dùng cuối). Những khoảng cách này được mô tả trong 3 đặc điểm sau:

- Khoảng cách về thời gian: Một developer có thể mất vài ngày, vài tuần, hay thâm chí vài tháng làm việc với code trước khi chuyển sang giai đoạn production.
- Khoảng cách về con người: Các developer viết code, các kĩ sư Ops deploy chúng.
- Khoảng cách về tool: các developer có thể sử dụng một stack như Nginx, SQLite, và OS X, trong khi bản triển khai của production lại sử dụng Apache, MySQL, và Linux.

Bộ 12 quy chuẩn được thiết kế để duy trì khả năng deploy liên tục của ứng dụng bằng cách thu hẹp khoảng cách giữa giai đoạn development và production. Hãy cùng xem xét lại 3 yếu tố tạo ra khoảng cách được mô tả bên trên:

- Thu hẹp khoảng cách về thời gian: Một developer có thể chỉ mất vài giờ hoặc vài phút để viết code và deploy nó.
- Thu hẹp khoảng cách về con người: Những developer viết code phải có sự tham gia và liên quan tới việc deploy nó và theo dõi các hoạt động của nó trong production.
- Thu hẹp khoảng cách về tool: Tối đa hóa sự tương đồng giữa giai đoạn development và production.

Tổng kết lại những điều trên trong bảng dưới đây:

|  |  Ứng dụng cổ điển |  Ứng dụng tuân theo bộ 12 quy chuẩn |
| ------------- |:-------------:| -----:|
| Thời gian giữa các deploy |  Hàng tuần |  Hàng giờ |  
| Người viết code và người deploy code |  Những người khác nhau |  Cùng người |  
| Các môi trường dev và production |  Khác nhau |  Giống nhau nhất có thể | 

[Các dịch vụ nền][3], như là cơ sở dữ liệu của ứng dụng, hệ thống hàng đợi, hoặc bộ nhớ đệm, là những thành phần rất quan trọng mà sự tương đồng giữa giai đoạn dev/prod cần lưu ý. Nhiều ngôn ngữ cho phép các thư viện đơn giản hóa việc truy cập đến các dịch vụ nền, bao gồm _adapters_ cho tới các loại khác nhau của dịch vụ. Một số ví dụ được cho trong bảng dưới đây.


| Loại |  Ngôn ngữ |  Thư viện |  Adapters |  
| ------------- |:-------------:| -----:|-----:|
| Database |  Ruby/Rails |  ActiveRecord |  MySQL, PostgreSQL, SQLite |  
| Queue |  Python/Django |  Celery |  RabbitMQ, Beanstalkd, Redis |  
| Cache |  Ruby/Rails |  ActiveSupport::Cache |  Memory, filesystem, Memcached | 

Các developers đôi khi sẽ thích thú với việc sử dụng các dịch vụ nền nhẹ trong các môi trường local của họ, trong khi các dịch vụ nền mạnh mẽ và ổn định hơn sẽ được sử dụng trong môi trường production. Ví dụ, sử dụng SQLite local và PostgreSQL trong production; hay bộ nhớ local process cho việc caching trong development và Memcaches trong production.

**Developer tuân theo bộ 12 quy chuẩn sẽ không sử dụng các dịch vụ nền khác nhau giữa giai đoạn development và production**, thậm chí khi các adapter về mặt lý thuyết cũng đã loại bỏ sự khác biệt giữa các dịch vụ nền. Sự khác biệt giữa các dịch vụ nền đồng nghĩa những sự không tương thích nhỏ nhặt có thể xảy ra, điều này khiến code chạy và test tốt trong development hoặc staging có thể bị lỗi ở production. Những loại lỗi này tạo ra những xung đột và làm cản trở triển hoạt động deploy liên tục. Ảnh hưởng của lỗi này và những hậu quả của nó tác động lên việc deploy liên tục là rất lớn khi ta xét đến tổng thời gian của các lỗi trên vòng đời của một ứng dụng.

Các dịch vụ local gọn nhẹ giờ ngày càng trở nên kém hấp dẫn hơn so với ngày trước. Các dịch vụ nền hiện đại như Memcaches, PostgreSQL, và RabbitMQ không khó để cài đặt và chạy nhờ vào các hệ thống packaging hiện đại, như [Homebrew][4] và [apt-get][5]. Ngoài ra, các provisioning tool (tool chuẩn bị cho hệ thống) như [Chef][6] và [Puppet][7] đã được kết hợp với các môi trường máy ảo nhẹ như[Docker][8] và [Vagrant][9] cho phép các developer chạy các môi trường local gần như tương tự với các môi trường producion. Chi phí để cài đặt và sử dụng những hệ thống này là thấp nếu so với những lợi ích mà sự tương đồng hóa giai đoạn developemnt, production và khả năng deploy liên tục mang lại.

Các adapters sử dụng cho các dịch vụ sao lưu khác nhau vẫn còn gía trị, bởi chúng tạo cổng tới các dịch vụ nền mới một cách dễ dàng. Tuy nhiên tất cả các deploy của ứng dụng (môi trường của dev, staging, production) nên sử dụng cùng một loại và một phiên bản cho mỗi một dịch vụ nền.

#### XI. Logs
###### Coi các logs như các luồng sự kiện
Các log sẽ cho ta biết được hoạt động của ứng dụng đang chạy. Trong các môi trường hướng máy chủ chúng thường được viết vào một file trên đĩa (một "logfile"), tuy nhiên đó chỉ là định dạng đầu ra.

Các log là luồng sự kiện được tổng hợp lại, các sự kiện có thứ tự thời gian được thu thập từ các luồng đầu ra của tất cả các process đang chạy và các dịch vụ nền. Log ở dạng thô thường là định dạng văn bản với một sự kiện mỗi dòng (mặc dù backtraces từ các exception có thể trải trên nhiều dòng). Các log không có khởi đầu và kết thúc cố định, nhưng luồng thì liên tục có chừng nào ứng dụng còn hoạt động.

Một ứng dụng tuân theo bộ 12 quy chuẩn không bao giờ quan tâm đến các luồng đầu ra của routing hay storage. Nó không nên thử viết vào hay quản lý các file log. Thay vào đó, mỗi process đang chạy sẽ tự viết các luồng sự kiện, luồng đệm của chúng vào sdtout. Trong quá trình local development, các developer sẽ thấy luồng này ở màn hình terminal của họ để quan sát hoạt động của ứng dụng. 

Trong các deploy của staging hay production, mỗi luồng process sẽ được kiểm soát bởi môi trường thực thi, chúng được đối chiếu với tất cả các luồng khác của ứng dụng, và được điều hướng đến một hoặc nhiều các điểm cuối để hiển thị và lưu trữ lâu dài. Những thư mục lưu trữ này, ứng dụng sẽ không thể nhìn thấy hoặc cấu hình được, và thay vào đó chúng hoàn toàn được quản lý bởi môi trường thực thi. Bộ điều hướng log mã nguồn mở (Logplex và Fluentd) phù hợp với mục đích này.

Luồng sự kiện cho một ứng dụng có thể được điều hướng đến một file, hoặc được theo dõi thông qua lệnh tail thời gian thực trong một terminal. Quan trọng nhất, luồng có thể được gửi đến một log được đánh chỉ mục và hệ thống phân tích như Splunk, hoặc một hệ thống data warehousing như Hadoop/Hive. Những hệ thống này mang lại sự linh hoạt và khả năng theo dõi các hoạt động của ứng dụng qua thời gian, bao gồm:

- Tìm các sự kiện cụ thể trong quá khứ.
- Vẽ độ thị quy mô lớn theo các tiêu chí(ví dụ như số yêu cầu mỗi phút)
- Các cảnh báo được bật và sẵn sàng dựa trên heuristic được định nghĩa bởi người dùng (ví dụ như là một cảnh báo khi số lượng lỗi mỗi phút vượt quá một ngưỡng cụ thể).

#### XII. Admin processes
###### Chạy các công việc admin/quản lý như là các process chạy-một-lần

Process formation (hệ thống process) là một mảng các process được sử dụng để thực hiện các công việc định kỳ của ứng dụng (như xử lý các yêu cầu của web) khi nó đang chạy. Một cách riêng biệt, các developers sẽ mong muốn thực hiện các công việc quản lý hay bảo trì một lần cho ứng dụng, như là:

- Chạy database migrations (ví dụ `manage.py migrate` trong Django, `rake db:migrate` trong Rails).
- Chạy một console (REPL shell) để chạy code tùy ý hoặc xem xét các mô hình của ứng dụng đối với cơ sở dữ liệu thực tại. Hầu hết các ngôn ngữ cung cấp một REPL bằng cách chạy trình thông dịch mà không cần tham số (ví dụ `python` hay `perl`) hoặc trong vài trường hợp có một câu lênh tách rời (như `irb` cho Ruby, `rails console` cho Rails).
- Run script "chạy một lần" đối với repo của ứng dụng (e.g. `php scripts/fix_bad_records.php`).

Những process admin chạy một lần nên được chạy trong môi trường mà giống với môi trường chạy các process dài của ứng dụng. Chúng chạy với mỗi release, sử dụng cùng một codebase và cấu hình như bất cứ process nào chạy với release đó. Code admin phải đi kèm với code ứng dụng để tránh các vấn đề về đồng bộ.

Các kĩ thuật tách biệt các dependency có nên được sử dụng đối với tất cả các loại process. Ví dụ, nếu process của web Ruby sử dụng lệnh `bundle exec thin start`, sau đó database migration nên sử dụng `bundle exec rake db:migrate`. Tương tự như vậy, một chương trình Python sử dụng Virtualenv nên sử dụng vendored `bin/python` để chạy cả Tornado webserver và bất cứ tiến trình admin `manage.py` nào.

Bộ 12 quy chuẩn đặc biệt phù hợp với ngôn ngữ cung cấp REPL shell, điều này làm việc chạy các script một-lần trở nên dễ dàng hơn. Trong local deploy, các developers kích hoạt các admin process chạy một-lần bằng các lệnh shell trực tiếp bên trong thư mục checkout của ứng dụng. Trong production deploy, các developers có thể sử dụng ssh hay các cơ chế thực thi các lệnh một cách remote khác để chạy một tiến trình mà được cung cấp bởi môi trường thực thi của	deploy.





