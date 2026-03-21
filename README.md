<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ thống Thông báo Nhân viên</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/water.css">
</head>
<body>
    <h2>📣 Gửi thông báo nhân viên</h2>
    
    <label for="subject">Tiêu đề thông báo:</label>
    <input type="text" id="subject" placeholder="Nhập tiêu đề...">

    <label for="message">Nội dung:</label>
    <textarea id="message" rows="5" placeholder="Nhập nội dung thông báo..."></textarea>

    <hr>

    <h3>Tùy chọn gửi:</h3>
    <div>
        <input type="radio" id="all" name="target" value="all" checked>
        <label for="all">Gửi toàn bộ nhân viên (Sử dụng danh sách mặc định trên n8n)</label>
    </div>
    <div>
        <input type="radio" id="excel" name="target" value="excel">
        <label for="excel">Gửi theo danh sách file Excel</label>
    </div>

    <div id="excel-section" style="display:none; margin-top: 10px; border: 1px dashed #ccc; padding: 10px;">
        <label>Chọn file Excel (.xlsx):</label>
        <input type="file" id="fileInput" accept=".xlsx, .xls">
        <p><small>* Lưu ý: File cần có cột mang tên "email"</small></p>
    </div>

    <button id="sendBtn" style="margin-top: 20px; width: 100%;">🚀 Bấm để gửi ngay</button>

    <div id="status"></div>

    <script src="script.js"></script>
</body>
</html>
