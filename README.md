# Node.js Overview

Dưới đây là một số kiến thức nền về Node.js mà mình đã tổng hợp được trong thời gian học và làm việc với nó.

## 1. Introduction Node.js

- Node.js là một open-source và cross-platform JavaScript runtime. Node.js chạy trên V8 JavaScript engine, là core của Google Chrome.
- Một app Node.js chạy trên một single thread, không tạo thread mới cho mỗi request. Libraries trong Node.js được viết theo mô hình non-blocking, làm cho các hành vi blocking trở thành exceptions.
- Khi Node.js thực hiện một tác vụ I/O, ví dụ như các thao tác với network, thao tác với database hoặc filesytem, thay vì blocking thread lại để chờ đợi tác vụ I/O được thực hiện xong, Node.js sẽ tiếp tục xử lý request mới và quay trở lại khi tác vụ I/O thực hiện xong.

## 2. Node.js vs Browser

- Trong browser, phần lớn thời gian chúng ta làm việc với DOM, hoặc Web Platform APIs khác như Cookies. Đối với Node.js, chúng không tồn tại, bạn sẽ không có document, window và tất cả những objects khác mà browser cung cấp.
- Node.js cũng cung cấp những APIs mà trên browser không có, ví dụ như: filesystem functions,...
- Node.js hỗ trợ cả CommonJS và ES module systems (kể từ Node.js v12), trong khi browser mới chỉ bắt đầu triển khai ES Modules standard. Điều này có nghĩa là bạn có thể dùng cả "require()" và "import" trong Node.js, còn với browser thì chỉ dùng được "import".

## 3. Ưu điểm và nhược điểm.

## 4. Blocking vs Non-Blocking

- **Blocking** là khi việc thực thi JavaScript trong Node.js process phải chờ cho đến khi một hoạt động non-JavaScript (chẳng hạn như các tác vụ I/O, network, ...) được hoàn thành. Điều này xảy ra bởi vì **event-loop** không thể tiếp tục xử lý JavaScript trong khi một hoạt động **blocking** đang được thực thi. Đối với **non-blocking** thì việc thực thi JavaScript vẫn sẽ tiếp tục mà không chờ cho hoạt động non-JavaScript thực hiện xong, và Node.js process sẽ quay lại xử lý response của các hoạt đông non-Javascript khi chúng được thực hiện xong.
- Các **blocking methods** thì được thực thi đồng bộ (**synchronous**), còn các **non-blocking methods** thì được thực thi bất đồng bộ (**asynchronous**).

## 5. Event-loop và cách hoạt động của Node.js

- **Event-loop** là thứ giúp Node.js thực hiện các hoạt đông non-blocking I/O - mặc dù JavaScript là single-threaded. Nó làm điều này bằng cách gán các hoạt động đó cho hệ điều hành xử lý bất cứ khi nào có thể.
- Vì hầu hết các system kernel hiện nay đều là multi-threaded, chúng có thể xử lý nhiều hoạt động thực thi ở bên dưới nền background. Khi một trong số các hoạt động được hoàn thành, kernel sẽ báo cho Node.js và hàm callback xử lý tương ứng sẽ được thêm vào **poll queue** và được thực thi lần lượt.
- **Event-loop** là một vòng lặp vô tận, chờ các tác vụ, thực thi chúng, rồi ngủ cho đến khi nhận được các tác vụ mới.

### Cách hoạt động Node.js:

1. Khi Node.js khởi động, nó sẽ khởi tạo event-loop, xử lý tập lệnh input đầu vào được cung cấp có thể tạo lệnh gọi **async APIs**, lên lịch cho **timers**, và sau đó bắt đầu xử lý event-loop.
2. Ban đầu khi có request đến Node.js server, các tasks sẽ được đưa vào **call-stack** thực thi theo thứ tự LIFO.
3. Nếu như có bất cứ tasks nào trong **call-stack** quá nặng để thực hiện bởi event-loop (các tác vụ I/O operations), nó sẽ ủy quyền xử lý các tasks này cho **libuv thread pool** xử lý và tiếp tục xử lý các tasks khác.
4. Khi **libuv thread pool** hoàn thành một task, một hàm **callback** sẽ được gọi để xử lý lỗi (nếu có) hoặc xử lý kết quả trả về từ **thread pool**. Hàm **callback** này sẽ được đưa vào **event queue**.
5. **Event-loop** sẽ luôn luôn kiểm tra xem liệu **call-stack** có đang trống hay không. Nếu có nó sẽ gửi các **callbacks** từ **event queue** vào **call-stack** để thực thi.

## 6. Event, EventEmitter

- Trong Node.js, phần lớn các APIs được xây dựng theo kiến trúc hướng sự kiện (**event-driver**). Mọi hành động đều có thể được gọi là **Event** trong Node.js
- Node.js cung cấp class EventEmitter để xử lý các sự kiện đó.

## 7. Node.js REPL

- REPL là viết tắt của Read-Evaluate-Print-Loop là công cụ để thực hiện code Node.js trên màn hình console như terminal.

## 8. Callback, Promise, Async/Await

### 8.1. Callback

- **Callback** được gọi là hàm gọi lại, là hàm được truyền vào một hàm khác dưới dạng đối số. Một hàm nhận một hàm khác làm đối số thì được gọi là higher-order function. Thông thường **callback** sẽ được thực thi sau khi hàm khác đã được thực thi xong.
- **Callback hell** là khi gọi các hàm callback lồng nhau quá nhiều làm cho function trở nên rất khó hiểu.

### 8.2. Promise

- Một promise đại diện cho việc hoàn thành một asynchronous function.
- Trong Node.js, **Promise** là một object được khởi tạo bằng 1 hàm bao gồm 2 tham số _resolve_ và _reject_ để xử lý cho việc thành công hoặc thất bại của hoạt động non-blocking I/O tương ứng.
- Một **Promise** có thể có 3 trạng thái:
  - **Pending**: là trạng thái ban đầu trước khi được resolved hoặc rejected.
  - **Fullfilled**: hoạt động thành công, Promise đã được resolved.
  - **Rejected**: hoạt động thất bại, Promise đã bị rejected.

### 8.3. Async/Await

- **Async/Await** function cho phép bạn thực thi thực hiện các code asynchronous trông giống như code synchronous. **async/await** function vẫn sử dụng Promise ngầm bên trong nó nhưng có cú pháp truyền thống hơn.
- Một **async/await** function thì luôn luôn trả về một Promise. Vì vậy có thể sử dụng cú pháp của Promise khi gọi hàm **async/await**.

## 9. NestJs framework

### 9.1. Overview

Nest là một framework để xây dựng một cách hiệu quả và có thể mở rộng các ứng dụng Node.js server-side. Một ứng dụng Nest được cấu tạo từ nhiều Module, bắt đầu từ một root Module lớn nhất Application Module là nơi khởi tạo app và import các Module con.

### 9.2. Request lifecycle

- Nhìn chung trong Nest, 1 request sẽ được truyền qua middlewares tới guards, tiếp theo tới interceptors, rồi đến các pipes, trước khi đến các controller handle, và cuối cùng lại qua các interceptors trước khi response.
- Chi tiết về một vòng đời như sau:
  1. Incoming request
  2. Global -> Module middlewares
  3. Global -> Controller -> Route guards
  4. Global -> Controller -> Route interceptors (pre-controller)
  5. Global -> Controller -> Route pipes
  6. Controllers
  7. Services
  8. Route -> Controller -> Global interceptors (port-request)
  9. Exception filters
  10. Server response

### 9.3. Controllers

- Controller chịu trách nhiệm xử lý các incoming request và returning respose cho client.
- Mục đích của controller là nhận các request, routing để điều hướng các controller handle tương ứng với request.

### 9.4. Providers

Providers là một khái niệm cơ bản trong Nest. Các services, repositories, factories, helpers, ... có thể xem như là các providers. Ý tưởng của providers là nó có thể **injected** như một dependency.

### 9.5. Modules

Modules là các class được chú thích bằng @Module() decolator. Cung cấp metadata mà Nest dùng để tổ chức cấu trúc ứng dụng.

### 9.6. Middlewares

Middleware là một function được gọi trước khi request đến route handler. Middleware có quyền truy cập vào req object, res object và middleware function tiếp theo trong request-response cycle.

Các chức năng của middlewares:

- thực thi bất kỳ mã code nào.
- thực hiện các thay đổi cho req và res object.
- kết thúc req-res cycle.
- call middleware tiếp theo.

### 9.7. Exception filters

Nest đi kèm với một **exceptions layer** built-in chịu trách nhiệm xử lý tất cả các ngoại lệ chưa được xử lý trên ứng dụng.

### 9.8. Pipes

Một pipe là một class được chú thích bằng decorator @Injectable() và phải implements interface **PipeTransform**

**Pipes** có 2 trường hợp sử dụng điển hình:

- **transformation**: chuyển đổi dữ liệu đầu vào thành dạng mong muốn
- **validation**: đánh giá tính valid của dữ liệu đầu vào, hoặc throw error nếu dữ liệu không phù hợp.

### 9.9. Guards

Một guard là một class được chú thích bằng decorator @Injectable() và phải implements interface **CanActivate**

**Guards** có 1 trách nhiệm duy nhất đó là authorization.

### 9.10. Interceptors

Một interceptor là một class được chú thích bằng decorator @Injectable() và phải implements interface **NestInterceptor**

Nhiệm vụ của **Interceptors**

- thêm các các logic trước và sau khi thực thi method
- transform kết quả trả về hoặc ngoại lệ được ném ra từ một function
- extend hành vi cơ bản của function
- override một function tùy thuộc vào điều kiện cụ thể (ví dụ như để caching)
