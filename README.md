<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion Dashboard</title>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        /* Reset và cấu trúc khung lề trái */
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        body { display: flex; height: 100vh; overflow: hidden; background: #f4f7f9; }

        /* Sidebar cố định bên trái */
        .sidebar {
            width: 280px; background: #1e293b; color: white;
            display: flex; flex-direction: column; flex-shrink: 0;
        }
        .sidebar-header { padding: 30px 20px; font-size: 1.5rem; font-weight: 800; border-bottom: 1px solid #334155; }
        .nav-menu { padding: 20px 10px; flex-grow: 1; }
        .nav-item {
            width: 100%; padding: 15px; margin-bottom: 10px; border: none;
            background: none; color: #94a3b8; text-align: left; cursor: pointer;
            border-radius: 10px; display: flex; align-items: center; gap: 10px; font-weight: 600;
        }
        .nav-item.active { background: #3b82f6; color: white; }

        /* Vùng nội dung chính */
        .main-content { flex-grow: 1; display: flex; flex-direction: column; height: 100vh; }
        header { padding: 20px 40px; background: white; border-bottom: 1px solid #e2e8f0; font-weight: bold; }

        /* Vùng lịch sử có thanh cuộn (Scroll) */
        #history-feed {
            flex-grow: 1; overflow-y: auto; padding: 30px 40px;
            display: flex; flex-direction: column; gap: 20px;
        }
        .msg-item { display: flex; gap: 15px; background: white; padding: 20px; border-radius: 12px; border: 1px solid #e2e8f0; }
        .avatar { width: 45px; height: 45px; background: #cbd5e1; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-weight: bold; flex-shrink: 0; }
        .msg-info { flex: 1; }
        .msg-title { font-weight: 800; color: #1e293b; text-transform: uppercase; margin-bottom: 5px; }

        /* Khu vực nhập liệu tách biệt 2 ô ở dưới cùng */
        .input-container { padding: 25px 40px; background: white; border-top: 1px solid #e2e8f0; }
        .input-card { background: #f8fafc; border: 1px solid #cbd5e1; border-radius: 15px; padding: 20px; }
        
        .field-label { font-size: 0.8rem; font-weight: 700; color: #64748b; margin-bottom: 5px; display: block; }
        .title-input { width: 100%; padding: 10px; border: 1px solid #e2e8f0; border-radius: 8px; margin-bottom: 15px; font-weight: bold; outline: none; }
        .content-input { width: 100%; padding: 10px; border: 1px solid #e2e8f0; border-radius: 8px; min-height: 80px; resize: none; outline: none; }

        .btn-send {
            margin-top: 15px; background: #22c55e; color: white; border: none;
            padding: 12px 25px; border-radius: 8px; font-weight: 700; cursor: pointer;
            float: right; display: flex; align-items: center; gap: 8px;
        }
    </style>
</head>
<body>

    <aside class="sidebar">
        <div class="sidebar-header">SONION APP</div>
        <nav class="nav-menu">
            <button class="nav-item active"><i data-lucide="megaphone"></i> Thông báo chung</button>
            <button class="nav-item"><i data-lucide="users"></i> Gửi đến nhân viên</button>
        </nav>
    </aside>

    <main class="main-content">
        <header># thong_bao_chung</header>

        <div id="history-feed">
            <div class="msg-item">
                <div class="avatar">AD</div>
                <div class="msg-info">
                    <div style="font-size: 0.8rem; color: #94a3b8; margin-bottom: 5px;">Admin Sonion • Hệ thống</div>
                    <div class="msg-text">Chào mừng! Lịch sử thông báo sẽ hiển thị tại đây.</div>
                </div>
            </div>
        </div>

        <div class="input-container">
            <div class="input-card">
                <label class="field-label">TIÊU ĐỀ THÔNG BÁO</label>
                <input type="text" id="subject" class="title-input" placeholder="Ví dụ: MENU TUẦN W12.26">
                
                <label class="field-label">NỘI DUNG CHI TIẾT</label>
                <textarea id="message" class="content-input" placeholder="Nhập nội dung..."></textarea>
                
                <button class="btn-send" onclick="sendData()">
                    <i data-lucide="send"></i> GỬI THÔNG BÁO
                </button>
                <div style="clear: both;"></div>
            </div>
        </div>
    </main>

    <script>
        lucide.createIcons();
        const WEBHOOK = 'DÁN_LINK_N8N_CỦA_BẠN_VÀO_ĐÂY';

        async function sendData() {
            const sub = document.getElementById('subject').value;
            const msg = document.getElementById('message').value;
            if(!sub || !msg) return alert("Vui lòng nhập đủ thông tin!");

            const payload = { subject: sub, message: msg, time: new Date().toLocaleTimeString() };

            try {
                // Thêm vào lịch sử ngay lập tức
                const feed = document.getElementById('history-feed');
                const item = document.createElement('div');
                item.className = 'msg-item';
                item.innerHTML = `
                    <div class="avatar">AD</div>
                    <div class="msg-info">
                        <div style="font-size: 0.8rem; color: #94a3b8; margin-bottom: 5px;">Admin Sonion • ${payload.time}</div>
                        <div class="msg-title">${payload.subject}</div>
                        <div class="msg-text">${payload.message}</div>
                    </div>
                `;
                feed.appendChild(item);
                feed.scrollTop = feed.scrollHeight; // Tự động cuộn xuống cuối

                // Gửi n8n
                await fetch(WEBHOOK, { method: 'POST', body: JSON.stringify(payload) });
                
                // Xóa ô nhập
                document.getElementById('subject').value = '';
                document.getElementById('message').value = '';
            } catch(e) { alert("Lỗi kết nối!"); }
        }
    </script>
</body>
</html>
