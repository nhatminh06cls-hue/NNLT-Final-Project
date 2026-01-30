# 🎮 Hand Tracking Game
**Game hứng bóng điều khiển bằng tay – kết hợp trí nhớ & phản xạ**

## 📖 Giới thiệu
**Hand Tracking Game** là một game mini được xây dựng bằng Python, sử dụng:
* **Pygame** để xử lý game & đồ họa.
* **OpenCV + MediaPipe** để nhận diện tay qua webcam.
* **Selfie Segmentation** để xóa phông webcam theo thời gian thực.

Người chơi không cần chuột hay bàn phím (trừ menu), mà điều khiển trực tiếp bằng cử chỉ tay:
* ✊ **Nắm tay**
* ✋ **Duỗi tay**

Game yêu cầu ghi nhớ luật màu sắc và phản xạ nhanh để hứng bóng đúng cách.

---

## 🧠 Gameplay
* Ở đầu mỗi level, game sẽ hiển thị bảng luật trong **5 giây**:
    * 🔴 Màu nào cần **NẮM** tay.
    * 🟢 Màu nào cần **DUỖI** tay.
* Bóng rơi xuống với màu ngẫu nhiên.
* Người chơi phải:
    * Di chuyển thanh đỡ đúng vị trí.
    * Thực hiện đúng cử chỉ tay theo màu bóng.
* **Sai cử chỉ** → bóng xuyên qua → mất mạng.
* Càng lên level cao:
    * Tốc độ bóng tăng.
    * Luật màu thay đổi → tăng độ khó trí nhớ.

---

## ✋ Điều khiển

| Hành động | Cách thực hiện |
| :--- | :--- |
| **Di chuyển thanh đỡ** | Di chuyển tay trái/phải trước webcam |
| **Nắm tay** | ✊ Fist |
| **Duỗi tay** | ✋ Open |
| **Thoát game** | Phím `ESC` |
| **Chơi lại (Game Over)** | Phím `R` |

---

## 📷 Webcam & Xóa phông
* Webcam hiển thị góc phải màn hình.
* Sử dụng **MediaPipe Selfie Segmentation** để:
    * Xóa nền.
    * Chỉ giữ lại người chơi.
* Giúp tập trung vào chuyển động tay và bóng rơi.

---

## 🛠 Công nghệ sử dụng
* Python 3.9+
* Pygame
* OpenCV
* MediaPipe
* NumPy

---

## 🏗 Sơ đồ kiến trúc tổng thể (MVC + Computer Vision)

```mermaid
graph TD
    User[WEBCAM / Camera Input] --> Vision[HandTracker Vision]
    Vision -->|MediaPipe Hands| Detect[Fist / Open Detect]
    Vision -->|Selfie Segmentation| RemoveBG[Xóa Phông]
    
    Detect -->|Hand State x,y, fist| Controller[CONTROLLER / GameManager]
    
    Controller --> Logic1[Handle input hand, keyboard]
    Controller --> Logic2[Update game state]
    Controller --> Logic3[Apply rules & collisions]
    
    Logic1 & Logic2 & Logic3 --> Model[MODEL / Game Objects]
    Model -->|Ball, Paddle, Particle| View[VIEW / Rendering]
    
    View --> Screen[Pygame Screen]
    View --> UI[UI / HUD]
    View --> CamOverlay[Webcam Overlay]
## 📐 Kiến trúc Frontend & Backend
Dù là ứng dụng Desktop, dự án được thiết kế tách biệt rõ ràng giữa lớp hiển thị (Presentation Layer) và lớp xử lý (Logic Layer):

### 🎨 Frontend (Presentation Layer - Pygame)
Chịu trách nhiệm hiển thị hình ảnh và phản hồi thị giác cho người chơi:
* **Rendering Engine:** Sử dụng `Pygame` để vẽ 60 khung hình/giây (FPS).
* **UI/UX:**
    * Hệ thống Menu (Button hover, click).
    * HUD (Head-up Display): Hiển thị Điểm, Mạng, Level thời gian thực.
    * Visual Effects: Hiệu ứng nổ hạt (Particles) khi bóng chạm vợt, hiệu ứng rung màn hình (Screen Shake).
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
    * Máy trạng thái (State Machine): Điều phối chuyển cảnh giữa Menu -> Tutorial -> Show Rules -> Playing -> Game Over.
    * Hệ thống luật chơi: Random màu sắc và ghi nhớ quy luật theo từng Level.

---

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
