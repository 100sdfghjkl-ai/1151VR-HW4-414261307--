https://youtu.be/-ea9LJ-DjyI
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/9ad3e4fc-6020-4394-b5ae-96d0baa8666c" />

遊戲操作說明:
控制方式：使用鍵盤的 A / D 鍵 或 ← / → (左右方向鍵) 進行操作。

移動限制：飛機會自動等速向前飛行，玩家僅能控制飛機在同一水平面上進行左、右平移。

遊戲目標：

閃避 (Obstacle)：避免撞上左右兩側的柱子。一旦撞擊，飛機將停止引擎聲、停止移動，並播放撞擊音效。

過關 (PassZone)：將飛機對準兩根柱子中間的空隙穿過。當飛機尾部完全離開該區域時，會播放順利通過的音效。

製作流程與關鍵技術
1. 素材準備與環境建置
模型匯入：使用 Unity Asset Store 匯入飛機模型

材質修復：針對舊版資源在 URP 渲染管線下呈現粉紅色的問題，透過 Render Pipeline Converter 將材質升級，並手動將 Shader 切換為 Universal Render Pipeline/Lit。

程式碼修復：修正外掛腳本中過時的 API 警告，將 GetInstanceID() 替換為新版的 GetEntityId()。

2. 場景佈置與標籤 (Tag) 設定
障礙物 (柱子)：建立 3D 柱狀物件，設定 Tag 為 Obstacle，並保留預設的實體碰撞體 (Box/Capsule Collider)，不勾選 Is Trigger。

過關判定區：在兩根柱子中間建立一個方塊，將 Tag 設為 PassZone。移除 Mesh Renderer 元件使其在遊戲中隱形，並將 Box Collider 的 Is Trigger 勾選，使其成為可穿透的觸發器。

3. 玩家 (Player) 物理與組件設定
碰撞與剛體：為飛機掛載 Box Collider (調整邊界包覆機身) 與 Rigidbody (取消勾選 Use Gravity 以免下墜)。

音效源：掛載 Audio Source 元件作為飛機發聲的「喇叭」。(註：測試時若覺得畫面上白色的喇叭圖示太干擾，可透過關閉 Game 視窗右上角的 Gizmos 來隱藏)。

攝影機跟隨：將 Main Camera 設為飛機的子物件，並移至飛機後上方，使視角跟隨飛機移動。

4. 核心邏輯腳本 (FlightController)
為飛機撰寫並掛載 C# 腳本，包含以下核心邏輯：

音效綁定：在腳本欄位中綁定 飛行引擎聲、撞擊聲、通過聲 三段音效。

移動控制 (Update)：使用 transform.Translate 讓物件在 Z 軸持續前進，並讀取 Input.GetAxis("Horizontal") 處理 X 軸的左右平移。

撞擊判定 (OnCollisionEnter)：當碰到標籤為 Obstacle 的物件時，改變布林值停止飛機移動，關閉引擎聲並播放撞機音效。

過關判定 (OnTriggerExit)：使用離開觸發器的事件，確保飛機 (包含機尾) 完全飛出標籤為 PassZone 的區域時，才播放過關加分音效，並關閉該觸發區避免重複計分。
