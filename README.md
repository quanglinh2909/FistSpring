### Kiến trúc 3 lớp (3-layer)

* * *
- 3-layer gồm có 3 lớp: Presentation Layer, Business Logic Layer, Data Access Layer
  * Presentation Layer: có nhiệm vụ nhận các yêu cầu từ người dùng và trả về kết quả cho người dùng cuối, lớp này chứa bộ sử lý gọi là Controller.
  * Business Logic Layer: kiểm tra tính hợp lệ validate của dữ liệu, xử lý các nghiệp vụ, gọi các phương thức của Data Access Layer để lấy dữ liệu từ CSDL.
  * Data Access Layer: lớp này chứa các phương thức để truy xuất dữ liệu từ CSDL, các phương thức này được gọi bởi Business Logic Layer.

- Ưu điểm:
  + code tường minh hơn
  + dễ bảo trì hơn
  + dễ phát triển và tái sử dụng

- Để phục vụ cho kiến trúc 3 lớp spring đưa ra 3 annotation để phân biệt các lớp:
  * @Controller: ứng với lớp Presentation Layer
  * @Service: ứng với lớp Business Logic Layer
  * @Repository: ứng với lớp Data Access Layer

- Về bản chất thì 3 annotation này đều là @Component, nói cách khác là 3 annotation này đều là các lớp được quản lý bởi Spring Container.
- Những để sử dụng cho đúng thì ta nên sử dụng 3 annotation này để phân biệt các lớp.
  * @Controller: Dùng để tiếp nhận request
  * @Service: Dùng để xử lý logic
  * @Repository: Dùng để Giao tiếp với CSDL

### Restful API

* * *
- Ví dụ tạo Restful API:

```java

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MyController {
    @RequestMapping("/hello")
    public String hello() {
        return "Hello World";
    }
}
```
- ví dụ trên sẽ trả về chuỗi "Hello World" khi gọi đường dẫn /hello

- Có thê truyên nhiều đương dẫn vào 1 phương thức như sau:

```java
 @RequestMapping(value = {"/hello", "/hi"})
```
- cả 2 phương thức trên sẻ trả về ngay cả khi gọi các phương thức khác như POST, PUT, DELETE, PATCH, OPTIONS, HEAD, TRACE, CONNECT
- để chỉ cho phép 1 phương thức thì ta thêm vào 1 annotation khác:

```java
 @RequestMapping(value = {"/hello", "/hi"}, method = RequestMethod.GET)
```
- Ở ví dụ trên chỉ cho phép phương thức GET
- Ngoài ra có thể sử dụng @GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping, @OptionsMapping, @HeadMapping, @TraceMapping, @ConnectMapping
- Ví dụ:

```java
 @GetMapping("/hello")
```

- nếu các phương thức có chung 1 đường dẫn thì ta có thể viết như sau:

```java

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/user")
public class MyController {
  @GetMapping("")
  public ResponseEntity<String> hello() {
    return new ResponseEntity<>("Hello", HttpStatus.OK);
  }

  @PostMapping("")
  public ResponseEntity<String> helloPost() {
    return new ResponseEntity<>("Hello", HttpStatus.OK);
  }

  @PutMapping("")
  public ResponseEntity<String> helloPut() {
    return new ResponseEntity<>("Hello", HttpStatus.OK);
  }
}

```

### Để trả về kèm theo mã lỗi

```java

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MyController {
    @RequestMapping(value = "/hello",method = RequestMethod.GET)
    public ResponseEntity<?> hello() {
        return  ResponseEntity.status(HttpStatus.OK).body("Hello");
    }
}
```

