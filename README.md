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
            --glass: rgba(255, 255, 255, 0.9);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Plus Jakarta Sans', sans-serif; }
        body { background-color: var(--bg-main); display: flex; height: 100vh; color: var(--text-main); overflow: hidden; }

        /* Sidebar */
        .sidebar { width: 280px; background: var(--sidebar); color: white; display: flex; flex-direction: column; flex-shrink: 0; }
        .sidebar-header { padding: 40px 24px; font-weight: 800; font-size: 1.4rem; display: flex; align-items: center; gap: 10px; }
        .sidebar-header i { color: var(--primary); }

        .nav-list { flex: 1; padding: 10px 16px; }
        .nav-item { 
            display: flex; align-items: center; gap: 12px; padding: 14px 16px; 
            border-radius: 12px; cursor: pointer; transition: 0.2s; color: #94a3b8; border: none; background: none; width: 100%; font-size: 0.95rem; font-weight: 600; margin-bottom: 8px;
        }
        .nav-item:hover { background: rgba(255,255,255,0.05); color: white; }
        .nav-item.active { background: var(--primary); color: white; box-shadow: 0 10px 15px -3px rgba(99, 102, 241, 0.3); }

        /* Main Content */
        .main-wrapper { flex: 1; display: flex; flex-direction: column; overflow-y: auto; background: radial-gradient(circle at top right, #e0e7ff, transparent 400px); }
        header { padding: 30px 50px; background: rgba(248, 250, 252, 0.5); backdrop-filter: blur(10px); position: sticky; top: 0; z-index: 10; border-bottom: 1px solid #e2e8f0; }
        header h1 { font-size: 1.5rem; font-weight: 800; }

        .content-body { padding: 40px 50px; max-width: 1000px; width: 100%; margin: 0 auto; }

        /* Card */
        .glass-card { 
            background: var(--glass); border: 1px solid rgba(255,255,255,0.4); 
            border-radius: 24px; padding: 32px; box-shadow: 0 20px 25px -5px rgba(0,0,0,0.05); margin-bottom: 30px; 
        }

        .input-label { font-size: 0.85rem; font-weight: 700; color: #64748b; margin-bottom: 10px; display: block; }
        input[type="text"], textarea, input[type="file"] {
            width: 100%; border: 2px solid #e2e8f0; border-radius: 16px; padding: 14px 20px; font-size: 1rem; transition: 0.2s; background: white; outline: none; margin-bottom: 20px;
        }
        input:focus, textarea:focus { border-color: var(--primary); box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.1); }

        .btn-action {
            background: var(--primary); color: white; border: none; padding: 16px; border-radius: 16px; font-weight: 700; cursor: pointer; width: 100%; font-size: 1rem;
            display: flex; align-items: center; justify-content: center; gap: 10px; transition: 0.3s;
        }
        .btn-action:hover { background: var(--primary-hover); transform: translateY(-2px); }

        /* Feed */
        .feed-item { 
            display: flex; gap: 16px; background: white; padding: 24px; border-radius: 20px; margin-bottom: 16px; 
            border: 1px solid #f1f5f9; animation: slideIn 0.3s ease-out;
        }
        @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .avatar { width: 45px; height: 45px; border-radius: 12px; background: linear-gradient(135deg, #6366f1, #a855f7); color: white; display: flex; align-items: center; justify-content: center; font-weight: bold; flex-shrink: 0; }
        .msg-sender { font-weight: 700; font-size: 0.95rem; }
        .msg-time { font-size: 0.8rem; color: #94a3b8; margin-left: 10px; }
        .msg-subject { color: var(--primary); font-weight: 800; font-size: 0.85rem; text-transform: uppercase; margin: 4px 0; display: block; }
        .msg-text { color: #475569; line-height: 1.5; white-space: pre-wrap; }
        .excel-tag { background: #f0fdf4; color: #16a34a; padding: 6px 12px; border-radius: 8px; font-size: 0.75rem; font-weight: 700; margin-top: 10px; display: inline-flex; align-items: center; gap: 6px; }
    </style>
</head>
<body>

    <nav class="sidebar">
        <div class="sidebar-header"><i data-lucide="zap"></i> SONION APP</div>
        <div class="nav-list">
            <button class="nav-item active" id="btn-all" onclick="switchTab('all')"><i data-lucide="megaphone"></i> Thông báo chung</button>
            <button class="nav-item" id="btn-excel" onclick="switchTab('excel')"><i data-lucide="users"></i> Gửi đến nhân viên</button>
        </div>
    </nav>

    <div class="main-wrapper">
        <header><h1 id="tab-title">📢 Thông báo chung</h1></header>

        <div class="content-body">
            <div id="section-all" class="tab-pane">
                <div class="glass-card">
                    <span class="input-label">Tiêu đề thông báo</span>
                    <input type="text" id="sub-all" placeholder="E.g. MENU TUẦN W12.26">
                    <span class="input-label">Nội dung</span>
                    <textarea id="msg-all" rows="4" placeholder="Nhập nội dung..."></textarea>
                    <button class="btn-action" onclick="processSend('all')"><i data-lucide="send"></i> PHÁT HÀNH</button>
                </div>
                <div id="feed-all"></div>
            </div>

            <div id="section-excel" class="tab-pane" style="display:none">
                <div class="glass-card">
                    <h2 style="margin-bottom: 20px;">Gửi qua danh sách Excel</h2>
                    <span class="input-label">Chọn tệp dữ liệu</span>
                    <input type="file" id="file-input" accept=".xlsx, .xls">
                    <button class="btn-action" onclick="processSend('excel')" style="background: #0f172a;"><i data-lucide="file-up"></i> TẢI LÊN & GỬI</button>
                    <div style="margin-top: 20px; text-align: center;">
                        <a href="#" onclick="exportTemplate()" style="color: var(--primary); font-size: 0.85rem; font-weight: 700; text-decoration: none;">📥 Tải file mẫu</a>
                    </div>
                </div>
                <div id="feed-excel"></div>
            </div>
        </div>
    </div>

    <script>
        lucide.createIcons();
        const N8N_URL = 'DÁN_LINK_WEBHOOK_N8N_CỦA_BẠN_Ở_ĐÂY';

        function switchTab(tab) {
            // Cập nhật nút
            document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
            document.getElementById('btn-' + tab).classList.add('active');
            
            // Cập nhật nội dung
            document.getElementById('section-all').style.display = tab === 'all' ? 'block' : 'none';
            document.getElementById('section-excel').style.display = tab === 'excel' ? 'block' : 'none';
            
            // Cập nhật Title
            document.getElementById('tab-title').innerText = tab === 'all' ? '📢 Thông báo chung' : '👥 Gửi đến nhân viên';
        }

        async function processSend(type) {
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
                const res = await fetch(N8N_URL, { method: 'POST', headers: {'Content-Type': 'application/json'}, body: JSON.stringify(payload)});
                if(res.ok) {
                    renderFeed(type, payload);
                    if(type === 'all') { document.getElementById('sub-all').value = ''; document.getElementById('msg-all').value = ''; }
                }
            } catch(e) { alert("Lỗi gửi n8n!"); }
        }

        function renderFeed(type, data) {
            const container = document.getElementById(type === 'all' ? 'feed-all' : 'feed-excel');
            const div = document.createElement('div');
            div.className = 'feed-item';
            
            let html = `<div class="avatar">AD</div><div style="flex:1"><div class="msg-sender">Admin Sonion <span class="msg-time">${data.time}</span></div>`;
            if(type === 'all') {
                html += `<span class="msg-subject">${data.subject}</span><div class="msg-text">${data.message}</div>`;
            } else {
                html += `<div class="msg-text">Đã xử lý file gửi nhân viên</div><div class="excel-tag"><i data-lucide="file-check" style="width:14px"></i> ${data.filename} (${data.data.length} hàng)</div>`;
            }
            html += `</div>`;
            div.innerHTML = html;
            container.prepend(div);
            lucide.createIcons();
        }

        function exportTemplate() {
            const data = [["ma_nhan_vien", "tieu_de", "noi_dung", "email"], ["SN01", "Thông báo lương", "Nội dung...", "test@sonion.com"]];
            const ws = XLSX.utils.aoa_to_sheet(data);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Sheet1");
            XLSX.writeFile(wb, "Sonion_Template.xlsx");
        }
    </script>
</body>
</html>
