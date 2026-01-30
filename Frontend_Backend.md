## 📐 Kiến trúc Frontend & Backend
Dù là ứng dụng Desktop, dự án được thiết kế tách biệt rõ ràng giữa lớp hiển thị (Presentation Layer) và lớp xử lý (Logic Layer):

### 🎨 Frontend (Presentation Layer - Pygame)
Chịu trách nhiệm hiển thị hình ảnh và phản hồi thị giác cho người chơi:
* **Rendering Engine:** Sử dụng `Pygame` để vẽ 60 khung hình/giây (FPS).
* **UI/UX:**
  * Hệ thống Menu (Button hover, click).
  * **HUD (Head-up Display):** Hiển thị Điểm, Mạng, Level thời gian thực.
  * **Visual Effects:** Hiệu ứng nổ hạt (Particles) khi bóng chạm vợt, hiệu ứng rung màn hình (Screen Shake).
* **Camera Overlay:** Hiển thị luồng video từ webcam sau khi đã được xử lý xóa phông.

### ⚙️ Backend (Logic & Processing Layer)
Chịu trách nhiệm tính toán logic và xử lý dữ liệu đầu vào phức tạp:

* **Computer Vision Module (`HandTracker`):**
  * **Input:** Nhận luồng dữ liệu thô từ Webcam qua `OpenCV`.
  * **Processing:** Sử dụng `MediaPipe Hands` để trích xuất tọa độ xương tay (Landmarks) và `Selfie Segmentation` để tách nền người chơi.
  * **Output:** Trả về tọa độ tay (x, y) và trạng thái cử chỉ (Nắm/Duỗi) cho Game Controller.

* **Physics Engine (`Ball`, `Paddle`):**
  * Tính toán vector di chuyển của bóng.
  * Xử lý va chạm vật lý (AABB Collision) và va chạm logic (Check màu sắc hợp lệ).

* **Game Manager (`Game`):**
  * **Máy trạng thái (State Machine):** Điều phối chuyển cảnh giữa `Menu` -> `Tutorial` -> `Show Rules` -> `Playing` -> `Game Over`.
  * **Hệ thống luật chơi:** Random màu sắc và ghi nhớ quy luật theo từng Level.
### 📊 Sơ đồ luồng dữ liệu (Data Flow)
```mermaid
graph LR
    subgraph FRONTEND [🎨 FRONTEND / Pygame View]
        Screen[Màn hình Game]
        UI[Giao diện Điểm/Menu]
        VFX[Hiệu ứng Hạt/Rung]
        CamView[Hiển thị Webcam]
    end

    subgraph BACKEND [⚙️ BACKEND / Logic Core]
        CV[Computer Vision Engine]
        Rules[Luật Game & Level]
        Physics[Vật lý & Va chạm]
    end

    Input(Webcam & Phím) --> CV
    CV -->|Tọa độ tay & Cử chỉ| Physics
    Physics -->|Trạng thái bóng| Rules
    
    Rules -->|Cập nhật dữ liệu| UI
    Physics -->|Vị trí mới| Screen
    Physics -->|Va chạm| VFX
    CV -->|Hình ảnh đã xóa nền| CamView
```
