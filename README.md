<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion Admin Dashboard</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        :root {
            --sidebar-bg: #1e293b;
            --primary-color: #3b82f6;
            --success-color: #10b981;
            --bg-body: #f8fafc;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }
        
        body { 
            font-family: 'Inter', -apple-system, sans-serif; 
            background-color: var(--bg-body);
            display: flex;
            height: 100vh;
            overflow: hidden;
        }

        /* Sidebar cố định bên trái */
        .sidebar {
            width: 260px;
            background-color: var(--sidebar-bg);
            color: white;
            display: flex;
            flex-direction: column;
            box-shadow: 4px 0 10px rgba(0,0,0,0.1);
            z-index: 100;
        }

        .sidebar-brand {
            padding: 30px 20px;
            font-size: 1.5rem;
            font-weight: 800;
            text-align: center;
            letter-spacing: -1px;
            border-bottom: 1px solid #334155;
            color: #f8fafc;
        }

        .nav-menu { flex: 1; padding: 20px 0; }
        
        .nav-item {
            width: 100%;
            padding: 14px 24px;
            border: none;
            background: none;
            color: #94a3b8;
            text-align: left;
            font-size: 1rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
        }

        .nav-item:hover { background: #334155; color: white; }
        .nav-item.active {
            background: var(--primary-color);
            color: white;
            border-right: 4px solid #fff;
        }

        /* Vùng nội dung chính */
        .main-content {
            flex: 1;
            display: flex;
            flex-direction: column;
            overflow-y: auto;
        }

        header {
            background: white;
            padding: 20px 40px;
            border-bottom: 1px solid #e2e8f0;
            font-size: 1.25rem;
            font-weight: 700;
            color: #1e293b;
            position: sticky;
            top: 0;
        }

        .container {
            padding: 40px;
            max-width: 900px;
            width: 100%;
            margin: 0 auto;
        }

        .form-card {
            background: white;
            padding: 30px;
            border-radius: 16px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.05);
            margin-bottom: 40px;
        }

        .input-group { margin-bottom: 20px; }
        label { display: block; font-weight: 600; margin-bottom: 8px; color: #475569; font-size: 0.9rem; }
        
        input, textarea {
            width: 100%;
            padding: 12px 16px;
            border: 1px solid #e2e8f0;
            border-radius: 10px;
            font-size: 1rem;
            outline: none;
            transition: border 0.2s;
        }

        input:focus, textarea:focus { border-color: var(--primary-color); box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1); }

        .btn-send {
            background: var(--success-color);
            color: white;
            border: none;
            padding: 14px;
            border-radius: 10px;
            font-weight: 700;
            font-size: 1rem;
            cursor: pointer;
            width: 100%;
            transition: transform 0.1s, background 0.2s;
        }

        .btn-send:hover { background: #059669; transform: translateY(-1px); }
        .btn-send:active { transform: translateY(0); }

        /* Message Feed giống ảnh mẫu */
        .feed-container { margin-top: 20px; }
        .feed-item {
            display: flex;
            background: white;
            padding: 20px;
            border-radius: 12px;
            margin-bottom: 16px;
            border: 1px solid #f1f5f9;
        }

        .avatar {
            width: 44px;
            height: 44px;
            background: #cbd5e1;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: #475569;
            margin-right: 16px;
            flex-shrink: 0;
        }

        .msg-content-area { flex: 1; }
        .msg-info { display: flex; align-items: baseline; margin-bottom: 6px; }
        .author { font-weight: 700; color: #1e293b; margin-right: 10px; }
        .time { font-size: 0.8rem; color: #94a3b8; }
        .subject-tag { 
            font-weight: 800; 
            font-size: 0.85rem; 
            color: var(--primary-color); 
            display: block; 
            margin-bottom: 4px;
            text-transform: uppercase;
        }
        .message-text { color: #475569; line-height: 1.6; white-space: pre-wrap; }

        .excel-badge {
            display: inline-block;
            background: #dcfce7;
            color: #166534;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: 600;
            margin-top: 8px;
        }
    </style>
</head>
<body>

    <aside class="sidebar">
        <div class="sidebar-brand">SONION APP</div>
        <nav class="nav-menu">
            <button class="nav-item active" onclick="openTab(event, 'tab-all')">📢 Thông báo chung</button>
            <button class="nav-item" onclick="openTab(event, 'tab-excel')">👥 Gửi đến nhân viên</button>
        </nav>
    </aside>

    <main class="main-content">
        <header id="header-title">Thông báo chung</header>

        <div class="container">
            <div id="tab-all" class="tab-content">
                <div class="form-card">
                    <div class="input-group">
                        <label>Tiêu đề thông báo</label>
                        <input type="text" id="subject-all" placeholder="Ví dụ: MENU TUẦN W12.26">
                    </div>
                    <div class="input-group">
                        <label>Nội dung chi tiết</label>
                        <textarea id="message-all" rows="4" placeholder="Nhập nội dung thông báo..."></textarea>
                    </div>
                    <button class="btn-send" onclick="handleSend('all')">🚀 GỬI THÔNG BÁO</button>
                </div>

                <div class="feed-container" id="feed-all">
                    </div>
            </div>

            <div id="tab-excel" class="tab-content" style="display:none">
                <div class="form-card">
                    <h2 style="margin-bottom: 15px; font-size: 1.1rem;">Gửi qua File Excel</h2>
                    <p style="margin-bottom: 20px; color: #64748b; font-size: 0.9rem;">Hệ thống sẽ tự bóc tách dữ liệu và gửi email theo từng dòng.</p>
                    <div class="input-group">
                        <label>Chọn file (.xlsx)</label>
                        <input type="file" id="fileInput" accept=".xlsx, .xls">
                    </div>
                    <button class="btn-send" onclick="handleSend('excel')" style="background: var(--primary-color);">📤 TẢI LÊN & GỬI</button>
                    <div style="margin-top: 15px; text-align: center;">
                        <a href="#" onclick="downloadTemplate()" style="color: var(--primary-color); font-size: 0.85rem; font-weight: 600;">📥 Tải file mẫu tại đây</a>
                    </div>
                </div>

                <div class="feed-container" id="feed-excel"></div>
            </div>
        </div>
    </main>

    <script src="script.js"></script>
</body>
</html>
