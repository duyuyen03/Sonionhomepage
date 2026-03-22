<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion Communications Hub</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    
    <style>
        :root {
            --primary: #6366f1;
            --primary-hover: #4f46e5;
            --bg-main: #f8fafc;
            --sidebar: #0f172a;
            --text-main: #1e293b;
            --glass: rgba(255, 255, 255, 0.8);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; }
        
        body { background-color: var(--bg-main); display: flex; height: 100vh; color: var(--text-main); overflow: hidden; }

        /* Sidebar Design */
        .sidebar { width: 280px; background: var(--sidebar); color: white; display: flex; flex-direction: column; transition: 0.3s; position: relative; }
        .sidebar-header { padding: 40px 24px; font-weight: 800; font-size: 1.4rem; letter-spacing: -1px; display: flex; align-items: center; gap: 10px; }
        .sidebar-header i { color: var(--primary); }

        .nav-list { flex: 1; padding: 10px 16px; }
        .nav-item { 
            display: flex; align-items: center; gap: 12px; padding: 14px 16px; 
            border-radius: 12px; cursor: pointer; transition: 0.2s; color: #94a3b8; border: none; background: none; width: 100%; font-size: 0.95rem; font-weight: 600; margin-bottom: 8px;
        }
        .nav-item:hover { background: rgba(255,255,255,0.05); color: white; }
        .nav-item.active { background: var(--primary); color: white; box-shadow: 0 10px 15px -3px rgba(99, 102, 241, 0.3); }

        /* Main Content Area */
        .main-wrapper { flex: 1; display: flex; flex-direction: column; overflow-y: auto; background: radial-gradient(circle at top right, #e0e7ff, transparent 400px); }
        
        header { padding: 30px 50px; display: flex; justify-content: space-between; align-items: center; background: rgba(248, 250, 252, 0.5); backdrop-filter: blur(10px); sticky; top: 0; z-index: 10; }
        header h1 { font-size: 1.5rem; font-weight: 800; color: #0f172a; }

        .content-body { padding: 0 50px 50px 50px; max-width: 1000px; }

        /* Card Design */
        .glass-card { 
            background: var(--glass); backdrop-filter: blur(12px); border: 1px solid rgba(255,255,255,0.4); 
            border-radius: 24px; padding: 32px; box-shadow: 0 20px 25px -5px rgba(0,0,0,0.05); margin-bottom: 40px; 
        }

        .input-label { font-size: 0.85rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.5px; color: #64748b; margin-bottom: 10px; display: block; }
        
        input[type="text"], textarea, input[type="file"] {
            width: 100%; border: 2px solid #e2e8f0; border-radius: 16px; padding: 14px 20px; font-size: 1rem; transition: 0.2s; background: white; outline: none; margin-bottom: 24px;
        }
        input:focus, textarea:focus { border-color: var(--primary); box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.1); }

        .btn-action {
            background: var(--primary); color: white; border: none; padding: 16px 32px; border-radius: 16px; font-weight: 700; cursor: pointer; width: 100%; font-size: 1rem;
            display: flex; align-items: center; justify-content: center; gap: 10px; transition: 0.3s;
        }
        .btn-action:hover { background: var(--primary-hover); transform: translateY(-2px); box-shadow: 0 10px 20px rgba(99, 102, 241, 0.2); }

        /* Feed List Style */
        .feed-item { 
            display: flex; gap: 16px; background: white; padding: 24px; border-radius: 20px; margin-bottom: 16px; 
            border: 1px solid #f1f5f9; transition: 0.2s; animation: slideIn 0.4s ease-out;
        }
        @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        .avatar-ui { width: 48px; height: 48px; border-radius: 14px; background: linear-gradient(135deg, #6366f1, #a855f7); display: flex; align-items: center; justify-content: center; color: white; font-weight: bold; }

        .msg-meta { display: flex; align-items: center; gap: 10px; margin-bottom: 6px; }
        .msg-sender { font-weight: 700; color: #0f172a; }
        .msg-date { font-size: 0.8rem; color: #94a3b8; }
        .msg-subject { color: var(--primary); font-weight: 800; font-size: 0.9rem; text-transform: uppercase; margin-bottom: 4px; display: block; }
        .msg-txt { line-height: 1.6; color: #475569; }

        /* Badge for Excel */
        .excel-tag { background: #f0fdf4; color: #16a34a; padding: 4px 12px; border-radius: 8px; font-size: 0.75rem; font-weight: 700; margin-top: 10px; display: inline-flex; align-items: center; gap: 5px; }
    </style>
</head>
<body>

    <nav class="sidebar">
        <div class="sidebar-header">
            <i data-lucide="zap"></i> SONION APP
        </div>
        <div class="nav-list">
            <button class="nav-item active" onclick="switchTab(this, 'all')">
                <i data-lucide="megaphone"></i> Thông báo chung
            </button>
            <button class="nav-item" onclick="switchTab(this, 'excel')">
                <i data-lucide="users"></i> Gửi đến nhân viên
            </button>
        </div>
    </nav>

    <div class="main-wrapper">
        <header>
            <h1 id="tab-title">📢 Thông báo chung</h1>
            <div style="color: #64748b; font-size: 0.9rem; font-weight: 600;">Admin Portal</div>
        </header>

        <div class="content-body">
            <section id="content-all">
                <div class="glass-card">
                    <span class="input-label">Tiêu đề thông báo</span>
                    <input type="text" id="sub-all" placeholder="E.g. THÔNG BÁO MENU TUẦN MỚI">
                    
                    <span class="input-label">Nội dung thông điệp</span>
                    <textarea id="msg-all" rows="4" placeholder="Nhập nội dung bạn muốn gửi..."></textarea>
                    
                    <button class="btn-action" onclick="submitSend('all')">
                        <i data-lucide="send"></i> PHÁT HÀNH THÔNG BÁO
                    </button>
                </div>
                <div id="feed-all"></div>
            </section>

            <section id="content-excel" style="display:none">
                <div class="glass-card">
                    <h2 style="margin-bottom: 10px; font-size: 1.2rem;">Gửi qua danh sách Excel</h2>
                    <p style="color: #64748b; font-size: 0.9rem; margin-bottom: 25px;">Tự động gửi email cá nhân hóa dựa trên dữ liệu file.</p>
                    
                    <span class="input-label">Tệp dữ liệu (.xlsx)</span>
                    <input type="file" id="file-input" accept=".xlsx, .xls">
                    
                    <button class="btn-action" onclick="submitSend('excel')" style="background: #0f172a;">
                        <i data-lucide="file-up"></i> TẢI LÊN & XỬ LÝ GỬI
                    </button>
                    
                    <div style="margin-top: 20px; text-align: center;">
                        <a href="#" onclick="getTemplate()" style="color: var(--primary); font-size: 0.85rem; font-weight: 700; text-decoration: none;">📥 Tải xuống cấu trúc file mẫu</a>
                    </div>
                </div>
                <div id="feed-excel"></div>
            </section>
        </div>
    </div>

    <script>
        // Khởi tạo icons
        lucide.createIcons();

        const WEBHOOK = 'DÁN_LINK_WEBHOOK_N8N_CỦA_BẠN_Ở_ĐÂY';

        function switchTab(btn, tab) {
            document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
            btn.classList.add('active');
            
            document.getElementById('content-all').style.display = tab === 'all' ? 'block' : 'none';
            document.getElementById('content-excel').style.display = tab === 'excel' ? 'block' : 'none';
            document.getElementById('tab-title').innerText = tab === 'all' ? '📢 Thông báo chung' : '👥 Gửi đến nhân viên';
        }

        async function submitSend(type) {
            let payload = { type, time: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) };
            
            if (type === 'all') {
                const s = document.getElementById('sub-all').value;
                const m = document.getElementById('msg-all').value;
                if(!s || !m) return alert("Vui lòng điền đủ!");
                payload.subject = s; payload.message = m;
            } else {
                const f = document.getElementById('file-input');
                if(f.files.length === 0) return alert("Chưa chọn file!");
                const data = await f.files[0].arrayBuffer();
                const wb = XLSX.read(data);
                payload.data = XLSX.utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]]);
                payload.filename = f.files[0].name;
            }

            try {
                const res = await fetch(WEBHOOK, { method: 'POST', headers: {'Content-Type': 'application/json'}, body: JSON.stringify(payload)});
                if(res.ok) {
                    addFeedItem(type, payload);
                    if(type === 'all') { document.getElementById('sub-all').value = ''; document.getElementById('msg-all').value = ''; }
                }
            } catch(e) { alert("Lỗi kết nối n8n!"); }
        }

        function addFeedItem(type, data) {
            const container = document.getElementById(type === 'all' ? 'feed-all' : 'feed-excel');
            const item = document.createElement('div');
            item.className = 'feed-item';
            
            let html = `
                <div class="avatar-ui">AD</div>
                <div style="flex:1
