<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ thống Gửi Thông báo Nội bộ</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; margin: 0; display: flex; height: 100vh; background-color: #f4f7f6; }
        /* Sidebar */
        .sidebar { width: 250px; background-color: #2c3e50; color: white; padding: 20px 0; flex-shrink: 0; }
        .sidebar h2 { text-align: center; font-size: 1.2rem; margin-bottom: 30px; color: #ecf0f1; }
        .tab-btn { width: 100%; border: none; background: none; color: #bdc3c7; padding: 15px 20px; text-align: left; cursor: pointer; font-size: 1rem; transition: 0.3s; }
        .tab-btn:hover { background-color: #34495e; color: white; }
        .tab-btn.active { background-color: #3498db; color: white; border-left: 5px solid #fff; }
        
        /* Main Content */
        .content { flex-grow: 1; padding: 40px; overflow-y: auto; }
        .tab-content { display: none; background: white; padding: 30px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); max-width: 800px; }
        .tab-content.active { display: block; }

        h3 { margin-top: 0; color: #2c3e50; }
        label { display: block; margin: 15px 0 5px; font-weight: bold; }
        input, textarea, border { width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 4px; box-sizing: border-box; }
        button.btn-send { background-color: #27ae60; color: white; border: none; padding: 12px 25px; border-radius: 4px; cursor: pointer; margin-top: 20px; font-size: 1rem; }
        button.btn-send:hover { background-color: #219150; }
        .template-link { display: inline-block; margin-bottom: 15px; color: #3498db; text-decoration: none; font-weight: bold; cursor: pointer; }
        #status { margin-top: 20px; padding: 10px; border-radius: 4px; display: none; }
    </style>
</head>
<body>

    <div class="sidebar">
        <h2>QUẢN TRỊ VIÊN</h2>
        <button class="tab-btn active" onclick="openTab(event, 'thong-bao-chung')">📢 Thông báo chung</button>
        <button class="tab-btn" onclick="openTab(event, 'gui-nhan-vien')">👥 Gửi đến nhân viên</button>
    </div>

    <div class="content">
        
        <div id="thong-bao-chung" class="tab-content active">
            <h3>Gửi thông báo toàn công ty</h3>
            <p><small>Thông báo này sẽ được n8n gửi đến danh sách email mặc định (Toàn bộ nhân viên).</small></p>
            
            <label>Tiêu đề:</label>
            <input type="text" id="subject-all" placeholder="Ví dụ: Thông báo nghỉ lễ 2/9">
            
            <label>Nội dung thông báo:</label>
            <textarea id="message-all" rows="8" placeholder="Nhập nội dung chi tiết..."></textarea>
            
            <button class="btn-send" onclick="sendData('all')">🚀 Gửi toàn bộ nhân viên</button>
        </div>

        <div id="gui-nhan-vien" class="tab-content">
            <h3>Gửi thông báo theo danh sách Excel</h3>
            
            <div class="template-box">
                <span class="template-link" onclick="downloadTemplate()">📥 Tải file Excel mẫu tại đây</span>
            </div>

            <label>Chọn file Excel đã nhập liệu:</label>
            <input type="file" id="fileInput" accept=".xlsx, .xls">
            <p><small>Hệ thống sẽ tự động lấy Tiêu đề, Nội dung và Email từ từng dòng trong file.</small></p>
            
            <button class="btn-send" onclick="sendData('excel')">📤 Tải lên và Gửi ngay</button>
        </div>

        <div id="status"></div>
    </div>

    <script src="script.js"></script>
</body>
</html>
