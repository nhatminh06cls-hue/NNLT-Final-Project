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
## Link tải app: https://drive.google.com/file/d/1i2fBYeIK3jWkwuoqwhj9imJcgMnhhNdY/view?usp=sharing
