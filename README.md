<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quản Trị Viên - Sonion</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        * { box-sizing: border-box; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; display: flex; height: 100vh; background-color: #f0f2f5; overflow: hidden; }
        
        /* Sidebar sát lề trái */
        .sidebar { width: 280px; background-color: #2c3e50; color: white; flex-shrink: 0; display: flex; flex-direction: column; }
        .sidebar-header { padding: 25px; text-align: center; font-weight: bold; font-size: 1.2rem; border-bottom: 1px solid #3e4f5f; }
        .tab-btn { width: 100%; border: none; background: none; color: #bdc3c7; padding: 18px 25px; text-align: left; cursor: pointer; font-size: 1rem; transition: 0.3s; }
        .tab-btn:hover { background-color: #34495e; color: white; }
        .tab-btn.active { background-color: #3498db; color: white; }

        /* Vùng nội dung */
        .main-container { flex-grow: 1; overflow-y: auto; padding: 40px; }
        .tab-content { display: none; max-width: 900px; margin: 0 auto; }
        .tab-content.active { display: block; }

        .card { background: white; padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.05); margin-bottom: 25px; }
        h3 { margin-top: 0; color: #1a2a3a; border-left: 4px solid #3498db; padding-left: 15px; }
        
        input, textarea { width: 100%; padding: 12px; margin: 10px 0 20px 0; border: 1px solid #ddd; border-radius: 6px; font-size: 1rem; }
        button { cursor: pointer; border: none; border-radius: 6px; font-weight: 600; transition: 0.2s; }
        .btn-send { background-color: #27ae60; color: white; padding: 12px 30px; width: 100%; }
        .btn-send:hover { background-color: #2ecc71; }
        
        /* Lịch sử gửi tin */
        .history-section { margin-top: 30px; border-top: 2px solid #eee; padding-top: 20px; }
        .history-item { background: #f8f9fa; padding: 15px; border-radius: 8px; margin-bottom: 10px; border-left: 4px solid #95a5a6; }
        .history-item small { color: #7f8c8d; }
    </style>
</head>
<body>

    <div class="sidebar">
        <div class="sidebar-header">QUẢN TRỊ VIÊN</div>
        <button class="tab-btn active" onclick="openTab(event, 'tab-all')">📢 Thông báo chung</button>
        <button class="tab-btn" onclick="openTab(event, 'tab-excel')">👥 Gửi đến nhân viên</button>
    </div>

    <div class="main-container">
        <div id="tab-all" class="tab-content active">
            <div class="card">
                <h3>Gửi thông báo toàn công ty</h3>
                <label>Tiêu đề:</label>
                <input type="text" id="subject-all" placeholder="Nhập tiêu đề thông báo...">
                <label>Nội dung chi tiết:</label>
                <textarea id="message-all" rows="6" placeholder="Viết nội dung tại đây..."></textarea>
                <button class="btn-send" onclick="handleSend('all')">🚀 Gửi toàn bộ nhân viên</button>
            </div>

            <div class="history-section">
                <h4>🕒 Lịch sử đã gửi (Gần đây)</h4>
                <div id="history-list-all"></div>
            </div>
        </div>

        <div id="tab-excel" class="tab-content">
            <div class="card">
                <h3>Gửi theo danh sách Excel</h3>
                <p style="color: #666;">Hệ thống sẽ gửi email riêng biệt cho từng người trong file.</p>
                <button onclick="downloadTemplate()" style="background: none; color: #3498db; text-decoration: underline; padding: 0; margin-bottom: 20px;">📥 Tải file mẫu tại đây</button>
                
                <label>Chọn file dữ liệu (.xlsx):</label>
                <input type="file" id="fileInput" accept=".xlsx, .xls">
                <button class="btn-send" onclick="handleSend('excel')">📤 Tải lên & Chạy n8n</button>
            </div>

            <div class="history-section">
                <h4>🕒 Lịch sử gửi theo file</h4>
                <div id="history-list-excel"></div>
            </div>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>
