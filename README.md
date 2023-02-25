# Getting Started

### ApplicationContext
- Được tạo ra khi một ứng dụng Spring được khởi động.
- ApplicationContext là một container chứa các bean, nó được tạo ra bởi Spring IoC container.
- ApplicationContext được tạo ra sẽ load các bean được tạo ra trong project để đưa vào context lưu trữ.
### Bean
- Bean là một đối tượng được quản lý bởi ApplicationContext.
- Bean được tạo ra và quản lý bởi ApplicationContext.
- Bean khác với các đối tượng thông thường vì nó không cần từ khóa new để tạo ra. Vì vậy trong trường hợp 1 đối tượng thông thường
  chứa 1 đối tượng khác thì việc khai báo rất là phức tạp, còn với Bean thì chỉ cần khai báo 1 lần và nó sẽ được quản lý bởi ApplicationContext.

## ví dụ
- để tạo ra 1 bean thì ta sẽ sử dụng annotation @Component

```java
package com.example.demo;

import org.springframework.stereotype.Component;

@Component
public class Dress {
    public void wear() {
        System.out.println("Wearing a dress");
    }
}
```
- Để lấy ra bean thì ta sử dụng context.getBean() và truyền vào tên của bean đó.
```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

        Dress dress = context.getBean(Dress.class);
        System.out.println("Dress: " + dress);
        dress.wear();
    }

}

```
- Bean được viết theo design pattern Singleton, nghĩa là chỉ có 1 instance của bean được tạo ra và được sử dụng trong toàn bộ project.

## Ví dụ

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
       ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

       Dress dress = context.getBean(Dress.class);
       System.out.println("Dress: " + dress);
       dress.wear();

        Dress dress2 = context.getBean(Dress.class);
        System.out.println("Dress: " + dress2);
        dress2.wear();
    }

}
```

- Kết quả
```
Dress: com.example.demo.Dress@6b6b1b1f
Wearing a dress
Dress: com.example.demo.Dress@6b6b1b1f
Wearing a dress
```
- Như vậy ta thấy 2 đối tượng dress và dress2 đều trỏ đến cùng 1 đối tượng Dress.

### Nâng cao hơn

- Tạo 1 interface và 1 class implement interface đó
```java
public interface Outfit {
    void wear();
}
```
```java
import org.springframework.stereotype.Component;

@Component
public class Dress implements Outfit{
    @Override
    public void wear() {
        System.out.println("Wearing a dress");
    }
}
```
- Trong ApplicationContext ta có thể lấy ra bean theo kiểu interface
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
       ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

       Outfit outfit = context.getBean(Outfit.class);
       System.out.println("Dress: " + outfit);
       outfit.wear();
    }

}
```
- Kết quả
```
Dress: com.example.demo.Dress@6b6b1b1f
Wearing a dress
```
- Như vậy ta thấy mặc dù Outfit là interface nhưng ta vẫn có thể lấy ra bean của nó là Dress.

### Component Scan

- Mặc định thì Container sẽ tìm kiếm tất cả cac bean trong package main hiện tại và các package con của nó.
- Nếu muốn tìm kiếm các bean trong các package nhất định hoặc trong 1 phạm vi nhất định có 2 cách:
    - Sử dụng annotation @ComponentScan
    - Sử dụng scanBasePackages
- Ví dụ sử dụng annotation @ComponentScan
```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.ComponentScan;

@ComponentScan("com.example.demo.other_packet")
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
       ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

       Dress dress = context.getBean(Dress.class);
       System.out.println("Dress: " + dress);
        dress.wear();

    }

}
```
- trong ví dụ trên container chỉ tìm kiếm các bean trong package com.example.demo và package con của nó.
- Nếu bean được tạo ra trong package khác thì container sẽ không tìm thấy bean đó và sẽ báo lỗi.
- nếu muốn tìm kiếm các bean trong nhiêu package thì ta sẽ sử dụng annotation @ComponentScan như sau:
```java
@ComponentScan({"com.example.demo.other_packet", "com.example.demo.other_packet2"})
```
- Ví dụ sử dụng scanBasePackages
```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication(scanBasePackages = "com.example.demo.other_packet")
public class DemoApplication {
    public static void main(String[] args) {
       ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

       Dress dress = context.getBean(Dress.class);
       System.out.println("Dress: " + dress);
        dress.wear();

    }

}
```
- Nếu muốn tìm kiếm các bean trong nhiêu package thì ta sẽ sử dụng scanBasePackages như sau:
```java
@SpringBootApplication(scanBasePackages = {"com.example.demo.other_packet", "com.example.demo.other_packet2"})
```

### @Autowired

- @Autowired là 1 annotation được sử dụng để inject các bean vào các bean khác.
- @Autowired có thể được sử dụng ở các trường, constructor, setter method.
- Ví dụ Theo cách thông thường
```java
package com.example.demo;

import org.springframework.stereotype.Component;

@Component
public class GirlFriend {
    public Outfit outfit;

    public GirlFriend() {
        this.outfit = new Dress();
    }

    public GirlFriend(Outfit outfit) {
        this.outfit = outfit;
    }

    public Outfit getOutfit() {
        return outfit;
    }

    public void setOutfit(Outfit outfit) {
        this.outfit = outfit;
    }
}
```
```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication()
public class DemoApplication {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

        GirlFriend girlFriend = context.getBean(GirlFriend.class);

        System.out.println("Instance girlFriend: " + girlFriend);
        System.out.println("Instance girlFriend.outfit: " + girlFriend.outfit);
        girlFriend.outfit.wear();

    }

}
```
- Kết quả
```
Instance girlFriend: com.example.demo.GirlFriend@10823d72
Instance girlFriend.outfit: com.example.demo.Dress@7cea0110
Wearing a dress
```
- Như vậy ta thấy khi khởi tạo bean GirlFriend thì bean Dress cũng được khởi tạo.
- Nhưng vì Outfit là interface nên ta không thể khởi tạo trực tiếp được.
- Nên ta phải new Dress() trong constructor của GirlFriend hoăc truyên Dress vào constructor của GirlFriend.
- Như vậy nếu trong Outfit có nhiều class implement nó thì ta phải khởi tạo nhiều class trong constructor của GirlFriend.
- Ví dụ sử dụng @Autowired
```java
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class GirlFriend {
    @Autowired
    public Outfit outfit;

}
```
```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication()
public class DemoApplication {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

        GirlFriend girlFriend = context.getBean(GirlFriend.class);

        System.out.println("Instance girlFriend: " + girlFriend);
        System.out.println("Instance girlFriend.outfit: " + girlFriend.outfit);
        girlFriend.outfit.wear();

    }

}
```
- Kết quả
```
Instance girlFriend: com.example.demo.GirlFriend@10823d72
Instance girlFriend.outfit: com.example.demo.Dress@7cea0110
Wearing a dress
```
- Như vậy ta thấy khi khởi tạo bean GirlFriend thì bean Outfit cũng được khởi tạo.

### Scope

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication()
public class DemoApplication {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

        Dress dress1 = context.getBean(Dress.class);
        System.out.println("dress1 = " + dress1);

        Dress dress2 = context.getBean(Dress.class);
        System.out.println("dress2 = " + dress2);

    }

}
```
- Kết quả
```
dress1 = com.example.demo.Dress@d0865a3
dress2 = com.example.demo.Dress@d0865a3
```
- Như vậy ta thấy 2 bean dress1 và dress2 đều trỏ đến cùng 1 instance.
- Nếu muốn tạo ra nhiều instance thì ta sẽ sử dụng scope.
- Scope là 1 annotation được sử dụng để chỉ ra scope của bean.
- Có 2 scope là singleton và prototype.
- Singleton là scope mặc định của bean.
- Prototype là scope của bean khi ta sử dụng annotation @Scope("prototype").
  nó sẽ cho phép tạo ra nhiều instance của bean có địa bộ nhớ khác nhau.
- Ví dụ sử dụng scope
```java
package com.example.demo;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component
@Scope("prototype")
public class Dress implements Outfit{
    @Override
    public void wear() {
        System.out.println("Wearing a dress");
    }
}
```
- Kết quả
```
dress1 = com.example.demo.Dress@38aafb53
dress2 = com.example.demo.Dress@1729ec00
```
- Như vậy ta thấy 2 bean dress1 và dress2  trỏ đến 2 instance khác nhau.

### @Qualifier và @Primary

- @Qualifier là 1 annotation được sử dụng để chỉ ra bean nào sẽ được inject vào bean khác.
- @Qualifier có thể được sử dụng ở các trường, constructor, setter method.
- @Qualifier có thể được sử dụng cùng với @Autowired.
- @Qualifier có thể được sử dụng cùng với @Primary.
- @Primary là 1 annotation được sử dụng để chỉ ra bean nào sẽ được sử dụng khi có nhiều bean có cùng kiểu.

- Ví dụ sử dụng  @Primary
- Tạo 2 class Dress và Bikini implement interface Outfit
```java
package com.example.demo.other_packet;

import com.example.demo.Outfit;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

@Component
@Primary
public class Bikini implements Outfit {
    @Override

    public void wear() {
        System.out.println("Wearing a bikini");
    }
}
```
```java
package com.example.demo.other_packet;

import com.example.demo.Outfit;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

@Component
@Primary
public class Bikini implements Outfit {
    @Override

    public void wear() {
        System.out.println("Wearing a bikini");
    }
}
```
```java
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class GirlFriend {
    @Autowired
    public Outfit outfit;

}

```
```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication()
public class DemoApplication {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(DemoApplication.class, args);

        GirlFriend girlFriend = context.getBean(GirlFriend.class);

        System.out.println("Instance girlFriend: " + girlFriend);
        System.out.println("Instance girlFriend.outfit: " + girlFriend.outfit);
        girlFriend.outfit.wear();

    }

}
```
- Kết quả
```
Instance girlFriend: com.example.demo.GirlFriend@14b0e127
Instance girlFriend.outfit: com.example.demo.other_packet.Bikini@10823d72
Wearing a bikini
```
- Như vậy ta thấy khi khởi tạo bean GirlFriend thì bean Bikini được khởi tạo.
- Vì 2 class Dress và Bikini đều implement interface Outfit nên khi khởi tạo bean GirlFriend thì Spring sẽ không biết nên khởi tạo bean nào.
- Vì vậy ta sẽ sử dụng @Primary để chỉ ra bean nào sẽ được sử dụng khi có nhiều bean có cùng kiểu.
- Ví dụ sử dụng @Qualifier

```java
package com.example.demo.other_packet;

import com.example.demo.Outfit;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

@Component("bikini")
public class Bikini implements Outfit {
    @Override

    public void wear() {
        System.out.println("Wearing a bikini");
    }
}
```
```java
package com.example.demo.other_packet;

import com.example.demo.Outfit;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

@Component("bikini")
public class Bikini implements Outfit {
    @Override

    public void wear() {
        System.out.println("Wearing a bikini");
    }
}
```
```java
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class GirlFriend {
    @Autowired
    @Qualifier("dress")
    public Outfit outfit;

}
    
```
- Kết quả
```
Instance girlFriend: com.example.demo.GirlFriend@3727f0ee
Instance girlFriend.outfit: com.example.demo.Dress@5c448433
Wearing a dress
```
- Như vậy ta thấy khi khởi tạo bean GirlFriend thì bean Dress được khởi tạo.
- Vì @Qualifier("dress") chỉ ra bean Dress sẽ được sử dụng.
- Cách sử dụng ở constructor, setter method
```java
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class GirlFriend {
    public Outfit outfit;
    @Autowired
    public GirlFriend( @Qualifier("dress") Outfit outfit) {
        this.outfit = outfit;
    }

}
```







