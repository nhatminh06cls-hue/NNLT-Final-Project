Hand Tracking Game
Game hứng bóng điều khiển bằng tay – kết hợp trí nhớ & phản xạ
   Giới thiệu:
Hand Tracking Game là một game mini được xây dựng bằng Python, sử dụng:
•	Pygame để xử lý game & đồ họa
•	OpenCV + MediaPipe để nhận diện tay qua webcam
•	Selfie Segmentation để xóa phông webcam theo thời gian thực
Người chơi không cần chuột hay bàn phím (trừ menu), mà điều khiển trực tiếp bằng cử chỉ tay:
•	✊ Nắm tay
•	✋ Duỗi tay
Game yêu cầu ghi nhớ luật màu sắc và phản xạ nhanh để hứng bóng đúng cách.
________________________________________
🧠 Gameplay
•	Ở đầu mỗi level, game sẽ hiển thị bảng luật trong 5 giây:
o	Màu nào cần NẮM tay
o	Màu nào cần DUỖI tay
•	Bóng rơi xuống với màu ngẫu nhiên
•	Người chơi phải:
o	Đặt thanh đỡ đúng vị trí
o	Thực hiện đúng cử chỉ tay theo màu bóng
•	Sai cử chỉ ➝ bóng xuyên qua ➝ mất mạng
•	Càng lên level cao:
o	Tốc độ bóng tăng
o	Luật màu thay đổi → tăng độ khó trí nhớ
________________________________________
✋ Điều khiển
Hành động	Cách thực hiện
Di chuyển thanh	Di chuyển tay trái/phải trước webcam
Nắm tay	✊ Fist
Duỗi tay	✋ Open
Thoát game	ESC
Chơi lại (Game Over)	R
________________________________________
🎥 Webcam & Xóa phông
•	Webcam hiển thị góc phải màn hình
•	Sử dụng MediaPipe Selfie Segmentation để:
o	Xóa nền
o	Chỉ giữ lại người chơi
•	Giúp tập trung vào chuyển động tay và bóng rơi
________________________________________
🧩 Công nghệ sử dụng
•	Python 3.9+
•	Pygame
•	OpenCV
•	MediaPipe
•	NumPy
________________________________________
Sơ đồ kiến trúc tổng thể (MVC + Computer Vision)
┌──────────────────────────┐
│        WEBCAM            │
│   (Camera Input)         │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│  HandTracker (Vision)    │
│  - MediaPipe Hands       │
│  - Fist / Open Detect    │
│  - Selfie Segmentation   │
└────────────┬─────────────┘
             │ Hand State (x, y, fist)
             ▼
┌──────────────────────────────────────────┐
│              CONTROLLER                  │
│            GameManager                   │
│ - Handle input (hand, keyboard)          │
│ - Update game state                      │
│ - Apply rules & collisions               │
└────────────┬───────────────┬─────────────┘
             │               │
             ▼               ▼
┌───────────────────┐   ┌──────────────────┐
│      MODEL        │   │       VIEW       │
│  (Game Objects)   │   │   (Rendering)    │
│                   │   │                  │
│ - Ball             │   │ - Pygame Screen │
│ - Paddle           │   │ - UI / HUD      │
│ - Particle         │   │ - Menu           │
│ - Rules / Score    │   │ - Webcam Overlay │
└───────────────────┘   └──────────────────┘
________________________________________
📝 Mô tả kiến trúc 
Hệ thống được thiết kế theo mô hình MVC (Model–View–Controller), kết hợp với Computer Vision module để nhận diện cử chỉ tay theo thời gian thực.
Camera đóng vai trò là nguồn dữ liệu đầu vào. Dữ liệu hình ảnh được xử lý bởi module HandTracker sử dụng MediaPipe để trích xuất vị trí tay và trạng thái cử chỉ (nắm / duỗi).
GameManager đóng vai trò Controller, nhận dữ liệu từ Vision module, xử lý logic game, luật chơi, va chạm và cập nhật trạng thái.
Các đối tượng game như bóng, thanh đỡ và hiệu ứng được tổ chức trong Model, trong khi View chịu trách nhiệm hiển thị đồ họa, giao diện người dùng và hình ảnh webcam đã xóa phông.
________________________________________
2️⃣ CHUẨN HÓA OOP / MVC THEO CODE 
Giờ map code hiện tại → MVC chuẩn.
________________________________________
🟦 MODEL (Dữ liệu & Logic cốt lõi)
📁 src/entities/
Class	Vai trò
Ball	Trạng thái bóng, chuyển động, va chạm
Paddle	Thanh đỡ, trạng thái fist/open
Particle	Hiệu ứng va chạm
Rules (đề xuất thêm)	Quản lý luật màu
GameState (enum)	MENU / PLAYING / GAME_OVER
👉 Model không vẽ, chỉ tính toán & dữ liệu.
________________________________________
🟩 VIEW (Hiển thị)
📁 src/ui/
Thành phần	Chức năng
Button	UI menu
HUDRenderer (đề xuất)	Score, lives, level
MenuRenderer	Menu, tutorial
GameRenderer	Vẽ paddle, ball, particles
👉 View không xử lý logic game.
________________________________________
🟥 CONTROLLER (Điều phối)
📁 src/game/game_manager.py
Vai trò chính:
•	Nhận input từ:
o	HandTracker
o	Keyboard
•	Cập nhật:
o	Paddle
o	Ball
o	Rules
•	Chuyển state game
•	Gọi View để render
class GameManager:
    def update(self):
        self.handle_input()
        self.update_model()
        self.check_collisions()
trung tâm MVC.
________________________________________
🟨 VISION MODULE 
📁 src/vision/hand_tracker.py
class HandTracker:
    def get_hand_state(self):
        return hand_x, hand_y, is_fist
•	Không biết game là gì
•	Chỉ trả dữ liệu tay
•	Có thể tái sử dụng cho project khác

