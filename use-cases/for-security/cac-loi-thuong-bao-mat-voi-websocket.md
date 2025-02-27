# Các lỗi thường bảo mật với Websocket

## **🔍 Pentest WebSocket & Các Lỗ Hổng Bảo Mật**

***

### **📌 1. Các Lỗ Hổng Bảo Mật Thường Gặp trên WebSocket**

Dưới đây là các **lỗ hổng bảo mật nguy hiểm** thường xảy ra trên WebSocket:

#### **1️⃣ No Authentication / Authorization Bypass**

🔥 **Lỗi:** Không kiểm tra **JWT / Token** khi client gửi request WebSocket.\
📌 **Cách kiểm tra bằng Burp Suite/ZAP:**

* Chặn WebSocket **và thay đổi `token` thành giá trị rỗng hoặc của user khác**.
* Nếu vẫn có thể gửi request → WebSocket không kiểm tra quyền truy cập.

✅ **Cách fix:**

* **Luôn xác thực JWT khi client kết nối**.

```javascript
io.use((socket, next) => {
  const token = socket.handshake.auth.token;
  if (!token) return next(new Error("Authentication error"));

  jwt.verify(token, process.env.JWT_SECRET, (err, decoded) => {
    if (err) return next(new Error("Invalid token"));
    socket.user = decoded;
    next();
  });
});
```

***

#### **2️⃣ WebSocket Data Manipulation**

🔥 **Lỗi:** Client có thể **gửi request độc hại** vì không có xác thực dữ liệu.\
📌 **Cách kiểm tra bằng Burp Suite/ZAP:**

* Chặn gói WebSocket và thay đổi nội dung gửi lên server.
* Nếu server không kiểm tra, có thể gây lỗi **SQL Injection / XSS**.

✅ **Cách fix:**

* **Luôn kiểm tra dữ liệu đầu vào** từ WebSocket.

```javascript
socket.on("sendMessage", (data) => {
  if (typeof data.message !== "string" || data.message.length > 500) {
    return socket.emit("error", "Invalid message format");
  }
  io.emit("message", data);
});
```

***

#### **3️⃣ WebSocket Cross-Site Hijacking (CSWSH)**

🔥 **Lỗi:** Một trang web độc hại có thể kết nối WebSocket đến server nếu không có bảo vệ CORS.\
📌 **Cách kiểm tra bằng Burp Suite/ZAP:**

* Mở trang độc hại trong trình duyệt và thử kết nối đến WebSocket server của bạn.

✅ **Cách fix:**

* **Chỉ cho phép WebSocket từ domain hợp lệ.**

```javascript
const io = new Server(server, {
  cors: {
    origin: "https://yourdomain.com",
    methods: ["GET", "POST"],
  },
});
```

***

#### **4️⃣ WebSocket Message Replay Attack**

🔥 **Lỗi:** Hacker có thể **chụp gói tin WebSocket** và gửi lại để lặp lại hành động.\
📌 **Cách kiểm tra bằng Burp Suite:**

* Chặn request WebSocket **và gửi lại** gói tin cũ.
* Nếu server không phát hiện ra **replay attack**, lỗi tồn tại.

✅ **Cách fix:**

* **Thêm timestamp và signature vào mỗi request** để chống replay.

```javascript
socket.on("transaction", (data) => {
  if (Date.now() - data.timestamp > 5000) {
    return socket.emit("error", "Request expired");
  }
});
```

***

#### **5️⃣ WebSocket Denial-of-Service (DoS)**

🔥 **Lỗi:** Hacker gửi **hàng triệu request WebSocket** để làm chậm hoặc crash server.\
📌 **Cách kiểm tra bằng Python:**

```python
import websocket
ws = websocket.WebSocket()
ws.connect("ws://your-websocket-url")
for _ in range(100000):
    ws.send("SPAM SPAM SPAM")
```

✅ **Cách fix:**

* **Dùng Rate Limiting với Redis để giới hạn request.**

```javascript
import rateLimit from "express-rate-limit";

const wsLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 phút
  max: 100, // 100 request mỗi phút
  message: "Too many WebSocket requests, please slow down.",
});

io.use((socket, next) => {
  wsLimiter(socket.request, socket.request.res, next);
});
```

***

### **🎯 Tổng Kết**

| **Lỗ Hổng**           | **Cách Kiểm Tra**                                         | **Cách Fix**                             |
| --------------------- | --------------------------------------------------------- | ---------------------------------------- |
| **No Authentication** | Dùng Burp Suite/ZAP để thử kết nối WebSocket không có JWT | Xác thực JWT trước khi kết nối           |
| **Data Manipulation** | Dùng Burp Suite/ZAP để sửa nội dung request WebSocket     | Kiểm tra dữ liệu đầu vào                 |
| **CSWSH Attack**      | Dùng trang web độc hại để kết nối WebSocket               | Cấu hình CORS chỉ cho phép domain hợp lệ |
| **Message Replay**    | Gửi lại request cũ bằng Burp Suite/ZAP                    | Thêm timestamp & signature vào request   |
| **WebSocket DoS**     | Gửi hàng triệu request bằng script Python                 | Thêm Rate Limiting vào WebSocket         |

&#x20;
