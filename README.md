# Getting Started

### Cài đặt Socket.io
Thêm vào pom.xml
```xml
  <dependency>
    <groupId>com.corundumstudio.socketio</groupId>
    <artifactId>netty-socketio</artifactId>
    <version>1.7.17</version>
</dependency>
```
Thêm vào file application.properties
```properties
rt-server.port=9092
rt-server.host=localhost
```
Thêm vào config/socket_io/SocketIOConfig.java
```java
import com.corundumstudio.socketio.SocketIOServer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SocketIOConfig {
    @Value("${rt-server.host}")
    private String host;

    @Value("${rt-server.port}")
    private Integer port;

    @Bean
    public SocketIOServer socketIOServer() {
        com.corundumstudio.socketio.Configuration config = new com.corundumstudio.socketio.Configuration();
        config.setHostname(host);
        config.setPort(port);
        return new SocketIOServer(config);
    }
}
```
Thêm vào config/socket_io/ServerCommandLineRunner.java
```java
import com.corundumstudio.socketio.SocketIOServer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class ServerCommandLineRunner implements CommandLineRunner {

    private final SocketIOServer server;

    @Autowired
    public ServerCommandLineRunner(SocketIOServer server) {
        this.server = server;
    }

    @Override
    public void run(String... args) throws Exception {
        server.start();
    }
}
```
### Ví dụ sử dụng cho module chat

thêm vào socket_io/chat/ChatMessage.java
```java

public class ChatMessage {

    private String userName;
    private String message;

    public ChatMessage() {
    }

    public ChatMessage(String userName, String message) {
        super();
        this.userName = userName;
        this.message = message;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    @Override
    public String toString() {
        return "ChatMessage{" +
                "userName='" + userName + '\'' +
                ", message='" + message + '\'' +
                '}';
    }
}
```

thêm vào socket_io/chat/ChatModule.java
```java

@Component
public class ChatModule {

    private static final Logger log = LoggerFactory.getLogger(ChatModule.class);

    private final SocketIONamespace namespace;

    @Autowired
    public ChatModule(SocketIOServer server) {
        // muốn nhận tất cả thì server.addNamespace("")
        this.namespace = server.addNamespace("/chat");
        this.namespace.addConnectListener(onConnected());
        this.namespace.addDisconnectListener(onDisconnected());
        this.namespace.addEventListener("chat", ChatMessage.class, onChatReceived());
    }

    private DataListener<ChatMessage> onChatReceived() {
        return (client, data, ackSender) -> {
            System.out.println("Client[{}] - Received chat message '{}'");
            log.debug("Client[{}] - Received chat message '{}'", client.getSessionId().toString(), data);
            namespace.getBroadcastOperations().sendEvent("chat", data);
        };
    }

    private ConnectListener onConnected() {
        return client -> {
            System.out.println("Client[{}] - Connected to chat module through '{}'");
            HandshakeData handshakeData = client.getHandshakeData();
            log.debug("Client[{}] - Connected to chat module through '{}'", client.getSessionId().toString(), handshakeData.getUrl());
        };
    }

    private DisconnectListener onDisconnected() {
        return client -> {
            System.out.println("Client[{}] - Disconnected from chat module.");
            log.debug("Client[{}] - Disconnected from chat module.", client.getSessionId().toString());
        };
    }

}
```

* Giờ ta đã có thể sử dụng socket.io để gửi và nhận dữ liệu từ client với post là 9092
* Chạy Client để test


