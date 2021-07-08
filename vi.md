
[Source](https://12factor.net/ "Permalink to The Twelve-Factor App")

# 12-Factor

Trong thời đại hiện đại, phần mềm thường được cung cấp dưới dạng dịch vụ: được gọi là _ứng dụng web_, hoặc _phần mềm dịch vụ_. 12-factor là một phương pháp để xây dựng các ứng dụng phần mềm như một dịch vụ:

* Sử dụng định dạng **declarative** để tự động thiết lập, tđể giảm thiểu thời gian và chi phí cho các nhà phát triển mới tham gia dự án;
* Có một **quy ước rõ ràng** với hệ điều hành cơ bản, cung cấp **tối đa tính di động** giữa các môi trường thực thi;
* Phù hợp cho viẹc **triển khai** trên **các nền tảng đám mây** hiện đại, thực sự cần thiết cho các máy chủ và quản trị hệ thống;
* Giảm thiểu tối đa sự khác biệt giữa giai đoạn phát triển và thực thi, tạo điều kiện cho việc phát triển liên tục diễn ra một cách nhanh nhất.
* Và có thể **mở rộng quy mô** mà không cần có thay đổi đáng kể về công cụ, kiến trúc hoặc phát triển thực tiễn.

Phương pháp 12-factor có thể được áp dụng cho các ứng dụng được viết bằng bất kỳ ngôn ngữ lập trình nào và sử dụng bất kỳ kết hợp dịch vụ sao lưu nào (cơ sở dữ liệu, hàng đợi, bộ nhớ cache, v.v.).

Những người đóng góp cho tài liệu này đã trực tiếp tham gia vào việc phát triển và triển khai hàng trăm ứng dụng, và gián tiếp chứng kiến sự phát triển, vận hành và mở rộng hàng trăm nghìn ứng dụng thông qua công việc của chúng tôi trên nền tảng [Heroku][1].

Tài liệu này tổng hợp tất cả các kinh nghiệm và quan sát của chúng tôi về một loạt các ứng dụng phần mềm như một dịch vụ trong tự nhiên.  Nó bao gồm 3 yếu tố quan trọng mà bất kỳ quy trình phát triển ứng dụng lý tưởng nào cũng cần có, đặc biệt chú ý đến động lực của sự phát triển của ứng dụng theo thời gian, tính linh hoạt của sự cộng tác giữa các nhà phát triển làm việc trên codebase của ứng dụng và [tránh chi phí phát sinh của phần mềm][2].

Động lực của chúng tôi là nâng cao nhận thức về một số vấn đề hệ thống mà chúng tôi đã thấy trong phát triển ứng dụng hiện đại, để cung cấp vốn từ vựng chung để thảo luận những vấn đề đó và cung cấp một loạt giải pháp khái niệm cho những vấn đề kèm theo những thuật ngữ. Định dạng được lấy cảm hứng từ sách của Martin Fowler [_Các mẫu của kiến trúc ứng dụng doanh nghiệp][3]_ and [Tái cấu trúc][4]_.

## I. Codebase

### Một codebase được theo dõi trong sự kiểm soát sửa đổi, nhiều triển khai

12-factor luôn được theo dõi trong hệ thống kiểm soát phiên bản, ví dụ như [Git][1], [Mercurial][2], or [Subversion][3]. Một bản sao của của việc kiểm soát sửa đổi csdl được biết đến  như là _code repository_, thường viến tắt là _code repo_ hoặc chỉ là _repo_.

Một _codebase_ là bất kì repo đơn nào (trong một hệ thống điều khiển sửa đổi tập trung như Subversion), hoặc bất kì bộ repos người mà chia sẻ commit gốc (trong một hệ thống kiểm soát sửa đổi phân cấp như Git).

![Một codebase map tới nhiều triển khai][4]

Luôn có mối tương quan một-một giữa codebase và ứng dụng:

* Nếu có nhiều codebases, nó không phải là một ứng dụng - đó là một hệ thống phân tán. Mỗi thành phần trong một hệ thống phân tán là một ứng dụng và mỗi thành phần có thể tuân thủ riêng với 12-factor.
* Nhiều ứng dụng chia sẻ cùng một mã là một sự vi phạm đối với bộ quy tắc 12-factor. Giải pháp ở đây là để chia sẻ code trở thành các thư viện có thể được đưa vào [quản lý phụ thuộc][5].

Chỉ có duy nhất 1 codebase trên một app, nhưng có thể có nhiều triển khai trên app đó. Một _triển khai_ ilà một phiên bản đang chạy của ứng dụng. Đây thường là một trang web sản xuất và một hoặc nhiều trang web dàn dựng. Ngoài ra, mọi nhà phát triển đều có một bản sao của ứng dụng đang chạy trong môi trường phát triển cục bộ của họ, mỗi một trong số đó cũng đủ điều kiện để triển khai.

Ví dụ, một nhà phát triển có một số commit chưa triển khai để dàn dựng; dàn dựng có một số commit chưa được triển khai để sản xuất. Nhưng tất cả đều chia sẻ cùng một codebase, do đó làm cho chúng có thể nhận dạng như các triển khai khác nhau của cùng một ứng dụng.

## II. Các phụ thuộc

### Khai báo rõ ràng và tách biệt các phụ thuộc

Hầu hết các ngôn ngữ lập trình đều cung cấp hệ thống đóng gói để phân phối các thư viện hỗ trợ, chẳng hạn như [CPAN][1] cho Perl hoặc [Rubygems][2] cho Ruby. Thư viện được cài đặt thông qua hệ thống đóng gói có thể được cài đặt trên toàn hệ thống (được gọi là "gói trang web") hoặc được đưa vào thư mục chứa ứng dụng (được biết đến như là  "vendoring" hoặc "bundling").

**Một 12-factor không bao giờ dựa vào sự tồn tại tiềm ẩn của các gói hệ thống.** Nó khai báo tất cả các phụ thuộc, hoàn toàn và chính xác, qua một _khai báo phụ thuộc_. Hơn thế nữa, nó sử dụng  một  công cụ _phụ thuộc tách biệt_ trong khi thực hiện để đảm bảo rằng không có phụ thuộc ngầm "rò rỉ" ftừ hệ thống xung quanh. Đặc tả phụ thuộc đầy đủ và rõ ràng được áp dụng thống nhất cho cả sản xuất và phát triển.

Ví dụ, [Bundler][3] cho Ruby cung cấp định  dạng `Gemfile` cho khai báo phụ thuộc và `bundle exec` cho phụ thuộc tách biệt. Trong Python có hai công cụ riêng biệt cho các bước này – [Pip][4] được sử dụng để khai báo và [Virtualenv][5] cho phân tách. Thậm chí C có [Autoconf][6] để khai báo phụ thuộc, và liên kết tĩnh có thể cung cấp sự phân tách phụ thuộc. Bất kể dùng tập công cụ nào, khai báo và tách biệt phụ thuộc luôn phải được sử dụng cùng nhau - chỉ một là không đủ để thỏa mãn 12-factor.

1 lợi ích của việc khai báo phụ thuộc rõ ràng là nó đơn giản hóa việc cài đặt cho những người mới phát triển ứng dụng. nhà phát triển mới có thể kiểm tra codebase của ứng dụng trên máy phát triển của họ, với điều kiện tiên quyết chỉ yêu cầu ngôn ngữữ chạy và quản lý phụ thuộc được cài đặt. Họ sẽ có thể thiết lập mọi thứ cần thiết để chạy mã của ứng dụng với _câu lẹnh xây dựng_ xác định. Ví dụ, câu lệnh xây dựng cho Ruby/Bundler là `bundle install`, trong khi đó Clojure/[Leiningen][7] là `lein deps`.

12-factor  cũng không phụ thuộc vào các tồn tại ngầm của bất kỳ hệ thống công cụ nào. Ví dụ bao gồm cả với ImageMagick hay `curl`.Mặc dù các công cụ này có thể tồn tại trên nhiều hoặc thậm chí hầu hết các hệ thống, không có gì đảm bảo rằng chúng sẽ tồn tại trên tất cả các hệ thống mà ứng dụng có thể chạy trong tương lai hoặc liệu phiên bản tìm thấy trên một hệ thống tương lai có tương thích với ứng dụng hay không. Nếu ứng dụng cần một công cụ hệ thống, công cụ đó cần được đưa vào ứng dụng.

## III. Cấu hình

### Lưu trữ cấu hình trong môi trường

Một _cấu hình_ của ứng dụng là mọi thứ có khả năng thay đổi giữa [triển khai][1] (dàn dựng, sản xuất, môi trường phát triển, v.v.). Nó bao gồm:

* Nguồn xử lý cơ sở dữ liệu, Memcaches, và các dịch vụ hỗ trợ khác. [backing services][2]
* Thông tin đăng nhập cho các dịch vụ bên ngoài như Amazon S3 hoặc Twitter
* Các giá trị của mỗi-triển-khai như là tên máy chủ của triển khai

Đôi khi, các ứng dụng lưu trữ cấu hình dưới dạng hằng số trong mã. Đây là vi phạm 12-factor, yêu cầu **phân tách chặt chẽ cấu hình từ code**. Cấu hình khác nhau đáng kể trên các triển khai, còn mã thì không.

Một test để kiểm tra xem liệu ứng dụng đã có tất cả các cấu hình mà được tách biệt với code được cài đặt chính xác hay chưa là xác định xem liệu nền code có thể trở thành mã nguồn mở bất cứ lúc nào mà không ảnh hưởng đến bất cứ thông tin xác thực nào hay không.

Lưu ý rằng định nghĩa "cầu hình" này *không* bao gồm cấu hình ứng dụng nội bộ, chẳng hạn như `config / routes.rb` trong Rails, hoặc cách [code modules are connected][3] in [Spring][4]. T Loại cấu hình này không thay đổi giữa các triển khai, và ví thể nó hoàn thiện nhất.

Một cách tiếp cận khác để cấu hình là sử dụng các tệp cấu hình không được kiểm tra trong viẹc kiểm soát sửa đổi, chẳng hạn như `config/database.yml` trong Rails. Đây là 1 cải thiện lớn so với việc sử dụng các hằng số được kiểm soát trong code repo, nhưng vẫn còn những nhược điểm: nó rất dễ kiểm soát lỗi 1 file cấu hình trong repo, có 1 xu hướng là các file cấu hình sẽ nằm rải rác ở những chỗ khác nhau và trong những định dạng khác nhau, khiên nó khó để thấy và kiểm soát tất cả các cấu hình trong 1 chỗ. Hơn nữa những định dạng này có xu hướng riêng biệt với từng ngôn ngữ hay framework.

**12-factor lưu trữ cấu hình trong _biến môi trường_** (thường viết tắt là _các biến env_ hoặc _env_). Biến env dễ thay đổi giữa triển khai mà không thay đổi bất kỳ mã nào; không giống như các tập tin cấu hình, có rất ít khả năng chúng được kiểm tra vào mã repo một cách vô tình; và không giống như các tệp cấu hình tùy chỉnh, hoặc các cơ chế cấu hình khác như thuộc tính hệ thống Java, chúng là một tiêu chuẩn bất khả tri về ngôn ngữ và hệ điều hành.

1 khía cạnh khác của quản lý cấu hình là gom nhóm. Đôi khi các ứng dụng gom cấu hình thành các tên nhóm ( thường gọi là “các môi trường”) được đặt tên sau các triển khai cụ thể, như là môi trường `development`, `test`, và `production` trong Rails. Phương pháp này không được gọn cho lắm: vì sẽ có nhiều triển khai của ứng dụng được tạo ra, các tên môi trường mới sẽ cần thiết, như là `staging` hoặc `qa`. Vì dự án sẽ càng phát triển hơn, các người phát triển có thể sẽ thêm các môi trường đặc biệt riêng của họ như `joes-staging`, kết quả của việc kết hợp đống cấu hình này sẽ khiến việc quản lý triển khải của ứng dụng trở nên mong manh.

Trong các ứng dụng 12-chuẩn, env vars là các điều khiển chi tiết, mỗi chúng trực giao đầy đủ với các env vars khác. Chúng không vào giờ được nhóm lại với nhau như làà "các môi trường", nhưng thay vào đó chúng quản lý độc lập cho từng triển khai. Đây là 1 mô hình nâng cao sự mượt mà, ứng dụng sự mở rộng 1 cách tự nhiên đến nhiều triển khai hơn trong suốt vòng đời của nó.

[1]: http://www.heroku.com/
[2]: http://blog.heroku.com/archives/2011/6/28/the_new_heroku_4_erosion_resistance_explicit_contracts/
[3]: https://books.google.com/books/about/Patterns_of_enterprise_application_archi.html?id=FyWZt5DdvFkC
[4]: https://books.google.com/books/about/Refactoring.html?id=1MsETFPD3I0C

  
