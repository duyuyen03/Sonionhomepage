<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion Admin</title>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        :root {
            --sidebar-color: #1e293b;
            --primary-blue: #3b82f6;
            --bg-light: #f8fafc;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        
        body { display: flex; height: 100vh; background-color: white; overflow: hidden; }

        /* SIDEBAR (KHUNG MÀU VÀNG TRONG ẢNH) */
        .sidebar {
            width: 260px;
            background-color: var(--sidebar-color);
            color: white;
            display: flex;
            flex-direction: column;
            flex-shrink: 0;
            z-index: 100;
        }

        .sidebar-brand {
            padding: 25px 20px;
            font-size: 1.3rem;
            font-weight: bold;
            color: #fff;
            border-bottom: 1px solid #334155;
            background: #111827;
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
            font-size: 0.95rem;
            font-weight: 500;
            cursor: pointer;
            border-radius: 8px;
            display: flex;
            align-items: center;
            gap: 12px;
            transition: 0.2s;
        }

        .nav-item:hover { background: #334155; color: white; }
        .nav-item.active { background: var(--primary-blue); color: white; }

        /* KHU VỰC CHÍNH */
        .main-container {
            flex-grow: 1;
            display: flex;
            flex-direction: column;
            background: white;
            position: relative;
        }

        header {
            padding: 15px 30px;
            border-bottom: 1px solid #e2e8f0;
            background: white;
            font-weight: bold;
            font-size: 1.1rem;
        }

        /* VÙNG HIỂN THỊ TIN NHẮN (CÓ THANH LĂN) */
        .feed-container {
            flex-grow: 1;
            overflow-y: auto; /* Tạo thanh lăn */
            padding: 30px;
            display: flex;
            flex-direction: column-reverse; /* Tin mới nhất ở dưới cùng giống Zalo/Slack */
        }

        /* Tùy chỉnh thanh lăn cho đẹp */
        .feed-container::-webkit-scrollbar { width: 6px; }
        .feed-container::-webkit-scrollbar-thumb { background: #e2e8f0; border-radius: 10px; }

        .msg-card {
            display: flex;
            gap: 15px;
            margin-bottom: 25px;
            animation: fadeIn 0.3s ease;
        }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        .avatar {
            width: 40px; height: 40px;
            background: #cbd5e1;
            border-radius: 8px;
            display: flex; align-items: center; justify-content: center;
            font-weight: bold; color: #475569; flex-shrink: 0;
        }

        .msg-body { flex-grow: 1; }
        .msg-header { display: flex; align-items: baseline; gap: 8px; margin-bottom: 4px; }
        .author { font-weight: 700; font-size: 0.95rem; }
        .time { font-size: 0.75rem; color: #94a3b8; }
        .msg-title { font-weight: 800; color: #1e293b; margin-bottom: 4px; text-transform: uppercase; font-size: 0.85rem; }
        .msg-text { font-size: 0.95rem; color: #334155; line-height: 1.5; white-space: pre-wrap; }

        /* KHU VỰC NHẬP LIỆU CỐ ĐỊNH Ở DƯỚI */
        .input-section {
            padding: 20px 30px;
            border-top: 1px solid #e2e8f0;
            background: #f8fafc;
        }

        .input-box {
            background: white;
            border: 1px solid #cbd5e1;
            border-radius: 12px;
            padding: 15px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }

        .input-box input {
            width: 100%; border: none; outline: none;
            font-size: 0.95rem; font-weight: 700; margin-bottom: 10px;
            color: #1e293b; border-bottom: 1px solid #f1f5f9; padding-bottom: 8px;
        }

        .input-box textarea {
            width: 100%; border: none; outline: none;
            font-size: 0.95rem; resize: none; min-height: 80px;
            color: #475569;
        }

        .input-footer { display: flex; justify-content: flex-end; margin-top: 10px; }

        .btn-send {
            background-color: #22c55e;
            color: white; border: none;
            padding: 10px 20px; border-radius: 8px;
            font-weight: 600; cursor: pointer;
            display: flex; align-items: center; gap: 8px;
            transition: 0.2s;
        }

        .btn-send:hover { background-color: #16a34a; }

        /* TAB EXCEL */
        #section-excel { padding: 50px; display: none; text-align: center; }
        .excel-card {
            max-width: 450px; margin: 0 auto;
            padding: 40px; border: 2px dashed #cbd5e1; border-radius: 20px;
            background: #f8fafc;
        }
    </style>
</head>
<body>

    <aside class="sidebar">
        <div class="sidebar-brand">SONION APP</div>
        <nav class="nav-menu">
            <button class="nav-item active" id="btn-all" onclick="switchTab('all')">
                <i data-lucide="megaphone" size="18"></i> Thông báo chung
            </button>
            <button class="nav-item" id="btn-excel" onclick="switchTab('excel')">
                <i data-lucide="file-spreadsheet" size="18"></i> Gửi đến nhân viên
            </button>
        </nav>
    </aside>

    <main class="main-container">
        <header id="header-title"># thong_bao</header>

        <div id="section-all" style="display: flex; flex-direction: column; height: 100%;">
            <div class="feed-container" id="history-feed">
                <div class="msg-card">
                    <div class="avatar">AD</div>
                    <div class="msg-body">
                        <div class="msg-header">
                            <span class="author">Admin Sonion</span>
                            <span class="time">Hệ thống</span>
                        </div>
                        <div class="msg-text">Chào mừng bạn! Hãy nhập thông báo ở ô bên dưới để bắt đầu.</div>
                    </div>
                </div>
            </div>

            <div class="input-section">
                <div class="input-box">
                    <input type="text" id="sub-input" placeholder="Tiêu đề (Ví dụ: MENU TUẦN W12.26)">
                    <textarea id="msg-input" placeholder="Nội dung chi tiết..."></textarea>
                    <div class="input-footer">
                        <button class="btn-send" onclick="handleSend()">
                            <i data-lucide="send" size="18"></i> Gửi thông báo
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div id="section-excel">
            <div class="excel-card">
                <i data-lucide="file-up" size="48" color="#94a3b8"></i>
                <h3 style="margin: 15px 0;">Gửi theo danh sách Excel</h3>
                <input type="file" id="file-input" accept=".xlsx, .xls" style="margin-bottom: 20px; width: 100%;">
                <button class="btn-send" style="width: 100%; justify-content: center; background: #3b82f6;" onclick="handleExcel()">
                    Tải lên và xử lý n8n
                </button>
                <p style="margin-top: 20px;"><a href="#" onclick="downloadTemplate()" style="color: var(--primary-blue); font-size: 0.85rem; font-weight: 600;">📥 Tải file mẫu</a></p>
            </div>
        </div>
    </main>

    <script>
        // Khởi tạo icon
        lucide.createIcons();

        // THAY LINK WEBHOOK N8N CỦA BẠN VÀO ĐÂY
        const N8N_WEBHOOK_URL = 'DÁN_LINK_WEBHOOK_CỦA_BẠN_Ở_ĐÂY';

        function switchTab(tab) {
            document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
            document.getElementById('btn-' + tab).classList.add('active');
            
            if(tab === 'all') {
                document.getElementById('section-all').style.display = 'flex';
                document.getElementById('section-excel').style.display = 'none';
                document.getElementById('header-title').innerText = '# thong_bao';
            } else {
                document.getElementById('section-all').style.display = 'none';
                document.getElementById('section-excel').style.display = 'block';
                document.getElementById('header-title').innerText = '# gui_nhan_vien';
            }
        }

        async function handleSend() {
            const sub = document.getElementById('sub-input').value;
            const msg = document.getElementById('msg-input').value;
            
            if(!sub || !msg) return alert("Vui lòng điền đủ Tiêu đề và Nội dung!");

            const now = new Date();
            const timeStr = now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0');

            const payload = {
                type: 'all',
                subject: sub,
                message: msg,
                time: timeStr
            };

            try {
                // Gửi sang n8n
                await fetch(N8N_WEBHOOK_URL, { method: 'POST', body: JSON.stringify(payload) });
                
                // Thêm vào lịch sử hiển thị
                addMessageToFeed(payload);

                // Xóa ô nhập
                document.getElementById('sub-input').value = '';
                document.getElementById('msg-input').value = '';
            } catch (e) {
                alert("Lỗi kết nối n8n. Vui lòng kiểm tra lại URL!");
            }
        }

        function addMessageToFeed(data) {
            const feed = document.getElementById('history-feed');
            const div = document.createElement('div');
            div.className = 'msg-card';
            div.innerHTML = `
                <div class="avatar">AD</div>
                <div class="msg-body">
                    <div class="msg-header">
                        <span class="author">Admin Sonion</span>
                        <span class="time">${data.time}</span>
                    </div>
                    <div class="msg-title">${data.subject}</div>
                    <div class="msg-text">${data.message}</div>
                </div>
            `;
            feed.prepend(div); // Thêm lên trên cùng
            feed.scrollTop = feed.scrollHeight;
        }

        function downloadTemplate() {
            const data = [["ma_nhan_vien", "tieu_de", "noi_dung", "email"], ["NV01", "Thông báo", "Nội dung...", "test@sonion.com"]];
            const ws = XLSX.utils.aoa_to_sheet(data);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Sheet1");
            XLSX.writeFile(wb, "Sonion_Template.xlsx");
        }

        async function handleExcel() {
            const f = document.getElementById('file-input');
            if(f.files.length === 0) return alert("Chọn file trước!");
            const data = await f.files[0].arrayBuffer();
            const workbook = XLSX.read(data);
            const jsonData = XLSX.utils.sheet_to_json(workbook.Sheets[workbook.SheetNames[0]]);
            
            fetch(N8N_WEBHOOK_URL, { 
                method: 'POST', 
                body: JSON.stringify({ type: 'excel', data: jsonData, filename: f.files[0].name }) 
            });
            alert("Đã gửi dữ liệu Excel sang n8n!");
        }
    </script>
</body>
</html>
