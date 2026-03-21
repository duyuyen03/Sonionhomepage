<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Admin Dashboard - Sonion</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        * { box-sizing: border-box; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; margin: 0; display: flex; height: 100vh; background-color: #ffffff; color: #333; }
        
        /* Sidebar */
        .sidebar { width: 260px; background-color: #1e293b; color: white; flex-shrink: 0; display: flex; flex-direction: column; }
        .sidebar-header { padding: 20px; text-align: center; font-weight: bold; font-size: 1.1rem; border-bottom: 1px solid #334155; letter-spacing: 1px; }
        .tab-btn { width: 100%; border: none; background: none; color: #94a3b8; padding: 15px 20px; text-align: left; cursor: pointer; font-size: 0.95rem; transition: 0.2s; display: flex; align-items: center; }
        .tab-btn:hover { background-color: #334155; color: white; }
        .tab-btn.active { background-color: #3882f6; color: white; }
        .tab-btn i { margin-right: 10px; }

        /* Main Content */
        .main-container { flex-grow: 1; overflow-y: auto; display: flex; flex-direction: column; }
        .content-header { padding: 15px 30px; border-bottom: 1px solid #eee; background: #f8fafc; font-weight: bold; font-size: 1.2rem; }
        .tab-content { display: none; padding: 30px; max-width: 800px; margin: 0 auto; width: 100%; }
        .tab-content.active { display: block; }

        /* Form Card */
        .card { background: #f1f5f9; padding: 20px; border-radius: 12px; margin-bottom: 40px; }
        label { display: block; font-weight: 600; margin-bottom: 8px; font-size: 0.9rem; }
        input, textarea { width: 100%; padding: 12px; margin-bottom: 15px; border: 1px solid #cbd5e1; border-radius: 8px; font-size: 1rem; }
        .btn-send { background-color: #10b981; color: white; border: none; padding: 12px; border-radius: 8px; width: 100%; font-weight: bold; cursor: pointer; font-size: 1rem; }
        .btn-send:hover { background-color: #059669; }

        /* PHẦN LỊCH SỬ GIỐNG TRONG ẢNH (Message Feed) */
        .history-feed { margin-top: 20px; border-top: 1px solid #eee; padding-top: 20px; }
        .msg-item { display: flex; margin-bottom: 25px; align-items: flex-start; }
        .msg-avatar { width: 40px; height: 40px; background: #64748b; border-radius: 8px; color: white; display: flex; align-items: center; justify-content: center; font-weight: bold; margin-right: 12px; flex-shrink: 0; }
        .msg-body { flex-grow: 1; }
        .msg-header { display: flex; align-items: baseline; margin-bottom: 4px; }
        .msg-author { font-weight: 700; font-size: 0.95rem; margin-right: 8px; color: #1e293b; }
        .msg-time { font-size: 0.75rem; color: #94a3b8; }
        .msg-content { font-size: 0.95rem; line-height: 1.5; color: #334155; white-space: pre-wrap; }
        .msg-subject { font-weight: 700; color: #1e293b; display: block; margin-bottom: 2px; text-transform: uppercase; font-size: 0.85rem; }

        .excel-info { background: #e0f2fe; padding: 10px; border-radius: 4px; border-left: 4px solid #0ea5e9; font-size: 0.85rem; margin-top: 5px; }
    </style>
</head>
<body>

    <div class="sidebar">
        <div class="sidebar-header">SONION APP</div>
        <button class="tab-btn active" onclick="openTab(event, 'tab-all')">📢 Thông báo chung</button>
        <button class="tab-btn" onclick="openTab(event, 'tab-excel')">👥 Gửi đến nhân viên</button>
    </div>

    <div class="main-container">
        <div class="content-header" id="header-title">Thông báo chung</div>

        <div id="tab-all" class="tab-content active">
            <div class="card">
                <label>Tiêu đề thông báo:</label>
                <input type="text" id="subject-all" placeholder="Ví dụ: MENU TUẦN W12.26">
                <label>Nội dung chi tiết:</label>
                <textarea id="message-all" rows="4" placeholder="Nhập nội dung..."></textarea>
                <button class="btn-send" onclick="handleSend('all')">🚀 GỬI THÔNG BÁO</button>
            </div>
            
            <div class="history-feed" id="feed-all">
                <div class="msg-item">
                    <div class="msg-avatar">AD</div>
                    <div class="msg-body">
                        <div class="msg-header">
                            <span class="msg-author">Admin Sonion</span>
                            <span class="msg-time">Vừa xong</span>
                        </div>
                        <span class="msg-subject">HƯỚNG DẪN</span>
                        <div class="msg-content">Các thông báo bạn gửi sẽ hiện tại đây theo định dạng danh sách.</div>
                    </div>
                </div>
            </div>
        </div>

        <div id="tab-excel" class="tab-content">
            <div class="card">
                <h3 style="margin-top:0">Gửi file Excel</h3>
                <a href="#" onclick="downloadTemplate()" style="font-size: 0.8rem; color: #3882f6;">Tải file mẫu tại đây</a>
                <br><br>
                <label>Chọn file dữ liệu:</label>
                <input type="file" id="fileInput" accept=".xlsx, .xls">
                <button class="btn-send" onclick="handleSend('excel')" style="background-color: #3b82f6;">📤 GỬI THEO DANH SÁCH</button>
            </div>

            <div class="history-feed" id="feed-excel"></div>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>
