<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion Admin Portal</title>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        /* 1. Xóa bỏ khoảng trắng và định dạng khung lề trái */
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        
        body { 
            display: flex; 
            height: 100vh; 
            width: 100vw;
            overflow: hidden; /* Ngăn toàn bộ trang bị cuộn */
            background-color: #f8fafc;
        }

        /* 2. Sidebar dịch hẳn ra bên trái */
        .sidebar {
            width: 260px;
            background-color: #1e293b;
            color: white;
            display: flex;
            flex-direction: column;
            flex-shrink: 0; /* Không cho phép sidebar bị co lại */
        }

        .sidebar-brand {
            padding: 30px 20px;
            font-size: 1.2rem;
            font-weight: 800;
            border-bottom: 1px solid #334155;
            letter-spacing: 1px;
        }

        .nav-menu { padding: 20px 10px; flex-grow: 1; }
        
        .nav-item {
            width: 100%;
            padding: 12px 15px;
            margin-bottom: 8px;
            border: none;
            background: none;
            color: #94a3b8;
            text-align: left;
            cursor: pointer;
            border-radius: 8px;
            display: flex;
            align-items: center;
            gap: 12px;
            transition: 0.2s;
        }

        .nav-item:hover { background: #334155; color: white; }
        .nav-item.active { background: #3b82f6; color: white; }

        /* 3. Khu vực chính: Chia làm Header - Feed (Scroll) - Input */
        .main-container {
            flex-grow: 1;
            display: flex;
            flex-direction: column;
            height: 100vh;
            position: relative;
        }

        header {
            padding: 15px 30px;
            background: white;
            border-bottom: 1px solid #e2e8f0;
            font-weight: bold;
            font-size: 1.1rem;
            flex-shrink: 0;
        }

        /* Vùng hiển thị tin nhắn có thanh lăn riêng */
        #history-feed {
            flex-grow: 1;
            overflow-y: auto; /* Tạo thanh lăn tại đây */
            padding: 20px 30px;
            display: flex;
            flex-direction: column; /* Tin cũ ở trên, tin mới xuất hiện ở dưới */
            gap: 20px;
            background: white;
        }

        /* Tùy chỉnh thanh lăn */
        #history-feed::-webkit-scrollbar { width: 6px; }
        #history-feed::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }

        .msg-card {
            display: flex;
            gap: 15px;
            animation: fadeIn 0.3s ease;
        }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        .avatar {
            width: 40px; height: 40px;
            background: #e2e8f0;
            border-radius: 8px;
            display: flex; align-items: center; justify-content: center;
            font-weight: bold; color: #475569; flex-shrink: 0;
        }

        .msg-content { flex-grow: 1; }
        .msg-meta { font-size: 0.8rem; color: #94a3b8; margin-bottom: 4px; }
        .msg-meta b { color: #1e293b; margin-right: 8px; }
        .msg-title { font-weight: 800; color: #1e293b; margin-bottom: 2px; font-size: 0.9rem; text-transform: uppercase; }
        .msg-text { font-size: 0.95rem; color: #334155; line-height: 1.5; white-space: pre-wrap; }

        /* 4. Ô nhập liệu cố định ở dưới cùng */
        .input-sticky-area {
            padding: 20px 30px;
            background: #f8fafc;
            border-top: 1px solid #e2e8f0;
            flex-shrink: 0;
        }

        .input-wrapper {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            border: 1px solid #cbd5e1;
            border-radius: 12px;
            padding: 15px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }

        .input-wrapper input {
            width: 100%; border: none; outline: none;
            font-size: 0.95rem; font-weight: 700; margin-bottom: 8px;
            border-bottom: 1px solid #f1f5f9; padding-bottom: 8px;
        }

        .input-wrapper textarea {
            width: 100%; border: none; outline: none;
            font-size: 0.95rem; resize: none; min-height: 70px;
        }

        .btn-send {
            background-color: #22c55e;
            color: white; border: none;
            padding: 10px 20px; border-radius: 8px;
            font-weight: 600; cursor: pointer;
            display: flex; align-items: center; gap: 8px;
            float: right; margin-top: 10px;
        }
    </style>
</head>
<body>

    <aside class="sidebar">
        <div class="sidebar-brand">SONION APP</div>
        <nav class="nav-menu">
            <button class="nav-item active" onclick="location.hash='#thong-bao-chung'">
                <i data-lucide="megaphone" size="18"></i> Thông báo chung
            </button>
            <button class="nav-item" onclick="location.hash='#gui-nhan-vien'">
                <i data-lucide="users" size="18"></i> Gửi đến nhân viên
            </button>
        </nav>
    </aside>

    <main class="main-container">
        <header># thong_bao</header>

        <div id="history-feed">
            <div class="msg-card">
                <div class="avatar">AD</div>
                <div class="msg-content">
                    <div class="msg-meta"><b>Admin Sonion</b> Hệ thống</div>
                    <div class="msg-text">Chào mừng bạn! Nội dung thông báo sẽ được đẩy lên trên và có thanh lăn tại đây.</div>
                </div>
            </div>
        </div>

        <div class="input-sticky-area">
            <div class="input-wrapper">
                <input type="text" id="subject-in" placeholder="Tiêu đề (Ví dụ: MENU TUẦN W12.26)">
                <textarea id="message-in" placeholder="Nội dung chi tiết..."></textarea>
                <button class="btn-send" onclick="submitMessage()">
                    <i data-lucide="send" size="18"></i> Gửi thông báo
                </button>
                <div style="clear: both;"></div>
            </div>
        </div>
    </main>

    <script>
        lucide.createIcons();
        const WEBHOOK_URL = 'DÁN_LINK_WEBHOOK_CỦA_BẠN_TẠI_ĐÂY';

        async function submitMessage() {
            const sub = document.getElementById('subject-in').value;
            const msg = document.getElementById('message-in').value;
            if(!sub || !msg) return alert("Hãy nhập tiêu đề và nội dung!");

            const time = new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
            const data = { type: 'all', subject: sub, message: msg, time: time };

            try {
                // Thêm tin nhắn vào vùng cuộn
                const feed = document.getElementById('history-feed');
                const div = document.createElement('div');
                div.className = 'msg-card';
                div.innerHTML = `
                    <div class="avatar">AD</div>
                    <div class="msg-content">
                        <div class="msg-meta"><b>Admin Sonion</b> ${data.time}</div>
                        <div class="msg-title">${data.subject}</div>
                        <div class="msg-text">${data.message}</div>
                    </div>
                `;
                feed.appendChild(div);
                
                // Tự động cuộn xuống tin mới nhất
                feed.scrollTop = feed.scrollHeight;

                // Gửi tới n8n
                await fetch(WEBHOOK_URL, { method: 'POST', body: JSON.stringify(data) });

                // Reset ô nhập
                document.getElementById('subject-in').value = '';
                document.getElementById('message-in').value = '';
            } catch(e) { alert("Lỗi gửi dữ liệu!"); }
        }
    </script>
</body>
</html>
