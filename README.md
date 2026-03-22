<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion App Admin</title>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --sidebar-bg: #1e293b;
            --primary-blue: #3b82f6;
            --bg-gray: #f1f5f9;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Inter', sans-serif; }
        
        body { display: flex; height: 100vh; background-color: white; }

        /* THANH LỰA CHỌN BÊN TRÁI (SIDEBAR) */
        .sidebar {
            width: 260px;
            background-color: var(--sidebar-bg);
            color: white;
            display: flex;
            flex-direction: column;
            flex-shrink: 0;
        }

        .sidebar-brand {
            padding: 30px 20px;
            font-size: 1.2rem;
            font-weight: 800;
            text-align: center;
            border-bottom: 1px solid #334155;
            letter-spacing: 1px;
        }

        .nav-menu { padding: 20px 10px; flex-grow: 1; }
        
        .nav-item {
            width: 100%;
            padding: 12px 20px;
            margin-bottom: 5px;
            border: none;
            background: none;
            color: #94a3b8;
            text-align: left;
            font-size: 0.95rem;
            font-weight: 500;
            cursor: pointer;
            border-radius: 8px;
            display: flex;
            align-items: center;
            gap: 12px;
            transition: 0.3s;
        }

        .nav-item:hover { background: #334155; color: white; }
        .nav-item.active { background: var(--primary-blue); color: white; }

        /* KHU VỰC NỘI DUNG CHÍNH */
        .main-content {
            flex-grow: 1;
            display: flex;
            flex-direction: column;
            overflow: hidden; /* Để scroll riêng vùng Feed */
        }

        header {
            padding: 15px 30px;
            border-bottom: 1px solid #e2e8f0;
            font-size: 1.1rem;
            font-weight: 700;
            color: #1e293b;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        /* VÙNG FEED (LỊCH SỬ) */
        #history-container {
            flex-grow: 1;
            overflow-y: auto;
            padding: 20px 30px;
            display: flex;
            flex-direction: column-reverse; /* Tin mới nhất nằm dưới cùng giống chat, hoặc bỏ nếu muốn mới nhất lên đầu */
        }

        .msg-item {
            display: flex;
            gap: 15px;
            margin-bottom: 25px;
            animation: fadeIn 0.4s ease;
        }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .avatar {
            width: 40px; height: 40px;
            background: #cbd5e1;
            border-radius: 8px;
            display: flex; align-items: center; justify-content: center;
            font-weight: bold; color: #475569; flex-shrink: 0;
        }

        .msg-body { flex-grow: 1; }
        .msg-header { display: flex; align-items: baseline; gap: 10px; margin-bottom: 4px; }
        .author { font-weight: 700; font-size: 0.95rem; color: #1e293b; }
        .time { font-size: 0.75rem; color: #94a3b8; }
        .subject { font-weight: 700; color: #1e293b; display: block; margin-bottom: 3px; font-size: 0.9rem; text-transform: uppercase; }
        .content-text { font-size: 0.95rem; line-height: 1.5; color: #334155; white-space: pre-wrap; }

        /* KHU VỰC NHẬP LIỆU (NẰM DƯỚI CÙNG) */
        .input-area {
            padding: 20px 30px;
            border-top: 1px solid #e2e8f0;
            background: #f8fafc;
        }

        .input-box {
            background: white;
            border: 1px solid #cbd5e1;
            border-radius: 12px;
            padding: 15px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.05);
        }

        .input-box input {
            width: 100%; border: none; outline: none;
            font-size: 1rem; font-weight: 600; margin-bottom: 10px;
            color: #1e293b;
        }

        .input-box textarea {
            width: 100%; border: none; outline: none;
            font-size: 0.95rem; resize: none; min-height: 60px;
            color: #475569;
        }

        .input-footer {
            display: flex;
            justify-content: flex-end;
            margin-top: 10px;
        }

        .btn-send {
            background-color: #10b981;
            color: white;
            border: none;
            padding: 8px 20px;
            border-radius: 6px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center; gap: 8px;
            transition: 0.2s;
        }

        .btn-send:hover { background-color: #059669; }

        /* TAB EXCEL */
        .excel-section { padding: 40px; display: none; text-align: center; }
        .excel-card {
            max-width: 500px; margin: 0 auto;
            padding: 40px; border: 2px dashed #cbd5e1; border-radius: 20px;
        }
    </style>
</head>
<body>

    <aside class="sidebar">
        <div class="sidebar-brand">SONION APP</div>
        <nav class="nav-menu">
            <button class="nav-item active" onclick="switchTab('all', this)">
                <i data-lucide="megaphone" size="18"></i> Thông báo chung
            </button>
            <button class="nav-item" onclick="switchTab('excel', this)">
                <i data-lucide="file-text" size="18"></i> Gửi đến nhân viên
            </button>
        </nav>
    </aside>

    <main class="main-content">
        <header id="tab-title"># thong_bao</header>

        <div id="section-all" style="display: flex; flex-direction: column; height: 100%;">
            <div id="history-container">
                <div class="msg-item">
                    <div class="avatar">AD</div>
                    <div class="msg-body">
                        <div class="msg-header">
                            <span class="author">Admin Sonion</span>
                            <span class="time">Hệ thống</span>
                        </div>
                        <div class="content-text">Chào mừng bạn! Các thông báo đã gửi sẽ xuất hiện tại đây.</div>
                    </div>
                </div>
            </div>

            <div class="input-area">
                <div class="input-box">
                    <input type="text" id="sub-all" placeholder="Tiêu đề thông báo (Ví dụ: MENU TUẦN W12.26)">
                    <textarea id="msg-all" placeholder="Nhập nội dung chi tiết..."></textarea>
                    <div class="input-footer">
                        <button class="btn-send" onclick="sendAll()">
                            <i data-lucide="send" size="16"></i> Gửi thông báo
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div id="section-excel" class="excel-section">
            <div class="excel-card">
                <i data-lucide="upload-cloud" size="48" color="#94a3b8"></i>
                <h3 style="margin: 15px 0;">Gửi theo danh sách Excel</h3>
                <input type="file" id="file-input" accept=".xlsx, .xls" style="margin-bottom: 20px;">
                <button class="btn-send" style="width: 100%; justify-content: center; background: #3b82f6;" onclick="sendExcel()">
                    Xử lý & Gửi Mail
                </button>
                <p style="margin-top: 15px;"><a href="#" onclick="downloadTemp()" style="color: var(--primary-blue); font-size: 0.8rem;">Tải file mẫu tại đây</a></p>
            </div>
        </div>
    </main>

    <script>
        lucide.createIcons();
        const WEBHOOK_URL = 'DÁN_WEBHOOK_N8N_CỦA_BẠN_TẠI_ĐÂY';

        function switchTab(type, btn) {
            document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
            btn.classList.add('active');
            
            if(type === 'all') {
                document.getElementById('section-all').style.display = 'flex';
                document.getElementById('section-excel').style.display = 'none';
                document.getElementById('tab-title').innerText = '# thong_bao';
            } else {
                document.getElementById('section-all').style.display = 'none';
                document.getElementById('section-excel').style.display = 'block';
                document.getElementById('tab-title').innerText = '# gui_nhan_vien';
            }
        }

        async function sendAll() {
            const sub = document.getElementById('sub-all').value;
            const msg = document.getElementById('msg-all').value;
            if(!sub || !msg) return alert("Vui lòng nhập đủ!");

            const payload = {
                type: 'all',
                subject: sub,
                message: msg,
                time: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})
            };

            // Gửi sang n8n
            try {
                await fetch(WEBHOOK_URL, { method: 'POST', body: JSON.stringify(payload) });
                addMessageToFeed(payload);
                document.getElementById('sub-all').value = '';
                document.getElementById('msg-all').value = '';
            } catch (e) { alert("Lỗi gửi dữ liệu!"); }
        }

        function addMessageToFeed(data) {
            const container = document.getElementById('history-container');
            const div = document.createElement('div');
            div.className = 'msg-item';
            div.innerHTML = `
                <div class="avatar">AD</div>
                <div class="msg-body">
                    <div class="msg-header">
                        <span class="author">Admin Sonion</span>
                        <span class="time">${data.time}</span>
                    </div>
                    <span class="subject">${data.subject}</span>
                    <div class="content-text">${data.message}</div>
                </div>
            `;
            container.appendChild(div);
            container.scrollTop = container.scrollHeight; // Tự cuộn xuống tin mới nhất
        }

        function downloadTemp() {
            const data = [["ma_nhan_vien", "tieu_de", "noi_dung", "email"], ["NV01", "Thông báo lương", "Nội dung...", "test@sonion.com"]];
            const ws = XLSX.utils.aoa_to_sheet(data);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Sheet1");
            XLSX.writeFile(wb, "Sonion_Template.xlsx");
        }
    </script>
</body>
</html>
