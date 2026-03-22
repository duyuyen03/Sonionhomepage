<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion Portal</title>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        /* CSS Reset & Layout */
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', sans-serif; }
        body { display: flex; height: 100vh; width: 100vw; overflow: hidden; background: #f1f5f9; }

        /* Sidebar bên trái */
        .sidebar {
            width: 260px; background: #1e293b; color: white;
            display: flex; flex-direction: column; flex-shrink: 0;
        }
        .sidebar-brand { padding: 25px; font-weight: 800; font-size: 1.2rem; border-bottom: 1px solid #334155; }
        .nav-menu { padding: 15px; flex-grow: 1; }
        .nav-item {
            width: 100%; padding: 12px; margin-bottom: 8px; border: none;
            background: none; color: #94a3b8; text-align: left; cursor: pointer;
            border-radius: 8px; display: flex; align-items: center; gap: 10px; font-weight: 600;
        }
        .nav-item.active { background: #3b82f6; color: white; }

        /* Vùng chính */
        .main-container { flex-grow: 1; display: flex; flex-direction: column; height: 100vh; position: relative; background: white; }
        header { padding: 15px 30px; border-bottom: 1px solid #e2e8f0; font-weight: bold; background: white; }

        /* Vùng Feed có thanh lăn (Scroll) */
        #history-feed {
            flex-grow: 1; overflow-y: auto; padding: 20px 30px;
            display: flex; flex-direction: column; gap: 20px;
        }
        #history-feed::-webkit-scrollbar { width: 6px; }
        #history-feed::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }

        .msg-card { display: flex; gap: 15px; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
        .avatar { width: 40px; height: 40px; background: #e2e8f0; border-radius: 8px; display: flex; align-items: center; justify-content: center; font-weight: bold; flex-shrink: 0; }

        /* Ô nhập liệu cố định ở đáy */
        .input-area { padding: 20px 30px; background: #f8fafc; border-top: 1px solid #e2e8f0; }
        .input-box { max-width: 900px; margin: 0 auto; background: white; border: 1px solid #cbd5e1; border-radius: 12px; padding: 15px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        .input-box input { width: 100%; border: none; outline: none; font-size: 1rem; font-weight: bold; margin-bottom: 10px; border-bottom: 1px solid #f1f5f9; padding-bottom: 8px; }
        .input-box textarea { width: 100%; border: none; outline: none; font-size: 0.95rem; resize: none; min-height: 80px; }
        .btn-send { background: #22c55e; color: white; border: none; padding: 10px 20px; border-radius: 8px; font-weight: bold; cursor: pointer; float: right; display: flex; align-items: center; gap: 8px; }

        /* Tab Excel */
        .excel-section { display: none; padding: 50px; text-align: center; }
    </style>
</head>
<body>

    <aside class="sidebar">
        <div class="sidebar-brand">SONION APP</div>
        <nav class="nav-menu">
            <button id="tab-all" class="nav-item active" onclick="switchTab('all')"><i data-lucide="megaphone"></i> Thông báo chung</button>
            <button id="tab-staff" class="nav-item" onclick="switchTab('staff')"><i data-lucide="users"></i> Gửi đến nhân viên</button>
        </nav>
    </aside>

    <main class="main-container">
        <header id="header-title"># thong_bao_chung</header>

        <div id="section-all" style="display: contents;">
            <div id="history-feed">
                <div class="msg-card">
                    <div class="avatar">AD</div>
                    <div>
                        <div style="font-size: 0.8rem; color: #94a3b8;">Admin Sonion • Hệ thống</div>
                        <div style="margin-top:5px;">Chào mừng! Hãy nhập nội dung vào ô bên dưới.</div>
                    </div>
                </div>
            </div>

            <div class="input-area">
                <div class="input-box">
                    <input type="text" id="title-in" placeholder="Tiêu đề (Ví dụ: MENU TUẦN W12.26)">
                    <textarea id="msg-in" placeholder="Nội dung chi tiết..."></textarea>
                    <button class="btn-send" onclick="sendAll()"><i data-lucide="send" size="18"></i> Gửi đi</button>
                    <div style="clear:both"></div>
                </div>
            </div>
        </div>

        <div id="section-staff" class="excel-section">
            <div style="max-width: 400px; margin: 0 auto; padding: 30px; border: 2px dashed #cbd5e1; border-radius: 15px;">
                <i data-lucide="file-up" size="40" color="#94a3b8"></i>
                <h3 style="margin: 15px 0;">Gửi theo danh sách Excel</h3>
                <input type="file" id="excel-file" accept=".xlsx, .xls" style="margin-bottom: 20px; width: 100%;">
                <button class="btn-send" style="float:none; width:100%; justify-content:center; background:#3b82f6;">Xử lý & Gửi</button>
            </div>
        </div>
    </main>

    <script>
        lucide.createIcons();
        const N8N_URL = 'DÁN_LINK_WEBHOOK_CỦA_BẠN_VÀO_ĐÂY';

        function switchTab(type) {
            document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
            if(type === 'all') {
                document.getElementById('tab-all').classList.add('active');
                document.getElementById('section-all').style.display = 'contents';
                document.getElementById('section-staff').style.display = 'none';
                document.getElementById('header-title').innerText = '# thong_bao_chung';
            } else {
                document.getElementById('tab-staff').classList.add('active');
                document.getElementById('section-all').style.display = 'none';
                document.getElementById('section-staff').style.display = 'block';
                document.getElementById('header-title').innerText = '# gui_den_nhan_vien';
            }
        }

        async function sendAll() {
            const title = document.getElementById('title-in').value;
            const msg = document.getElementById('msg-in').value;
            if(!title || !msg) return alert("Vui lòng nhập đủ thông tin!");

            const feed = document.getElementById('history-feed');
            const div = document.createElement('div');
            div.className = 'msg-card';
            div.innerHTML = `<div class="avatar">AD</div><div>
                <div style="font-size:0.8rem; color:#94a3b8;">Admin Sonion • ${new Date().toLocaleTimeString()}</div>
                <div style="font-weight:bold; margin:5px 0;">${title}</div>
                <div style="font-size:0.95rem;">${msg}</div>
            </div>`;
            feed.appendChild(div);
            feed.scrollTop = feed.scrollHeight;

            document.getElementById('title-in').value = '';
            document.getElementById('msg-in').value = '';

            try { await fetch(N8N_URL, { method: 'POST', body: JSON.stringify({title, msg}) }); } catch(e) {}
        }
    </script>
</body>
</html>
