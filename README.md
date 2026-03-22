<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion Admin</title>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        /* CSS FIX LỖI TRÀN LỀ VÀ BỐ CỤC */
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: sans-serif; }
        body { display: flex; height: 100vh; overflow: hidden; background: #f1f5f9; }

        /* SIDEBAR SÁT LỀ TRÁI */
        .sidebar {
            width: 260px; background: #1e293b; color: white;
            display: flex; flex-direction: column; flex-shrink: 0;
        }
        .sidebar-brand { padding: 25px; font-weight: bold; font-size: 1.2rem; border-bottom: 1px solid #334155; }
        .nav-menu { padding: 15px; flex-grow: 1; }
        .nav-item {
            width: 100%; padding: 12px; margin-bottom: 8px; border: none;
            background: none; color: #94a3b8; text-align: left; cursor: pointer;
            border-radius: 8px; display: flex; align-items: center; gap: 10px;
        }
        .nav-item.active { background: #3b82f6; color: white; }

        /* VÙNG NỘI DUNG CHÍNH */
        .main-container { flex-grow: 1; display: flex; flex-direction: column; height: 100vh; }
        header { padding: 15px 30px; background: white; border-bottom: 1px solid #e2e8f0; font-weight: bold; }

        /* VÙNG LỊCH SỬ CÓ THANH LĂN (SCROLL) */
        #history-feed {
            flex-grow: 1; overflow-y: auto; padding: 20px 30px;
            display: flex; flex-direction: column; gap: 15px;
        }
        .msg-card { display: flex; gap: 12px; background: white; padding: 15px; border-radius: 8px; border: 1px solid #e2e8f0; }
        .avatar { width: 35px; height: 35px; background: #cbd5e1; border-radius: 6px; display: flex; align-items: center; justify-content: center; font-weight: bold; flex-shrink: 0; }

        /* Ô NHẬP LIỆU CỐ ĐỊNH Ở DƯỚI */
        .input-area { padding: 20px 30px; background: #f8fafc; border-top: 1px solid #e2e8f0; }
        .input-box { background: white; border: 1px solid #cbd5e1; border-radius: 10px; padding: 15px; }
        .input-box input, .input-box textarea {
            width: 100%; border: none; outline: none; margin-bottom: 10px; font-size: 0.95rem;
        }
        .input-box input { font-weight: bold; border-bottom: 1px solid #f1f5f9; padding-bottom: 5px; }
        .btn-send {
            background: #22c55e; color: white; border: none; padding: 10px 20px;
            border-radius: 6px; font-weight: bold; cursor: pointer; float: right;
        }
    </style>
</head>
<body>

    <aside class="sidebar">
        <div class="sidebar-brand">SONION APP</div>
        <nav class="nav-menu">
            <button id="btn-tab-1" class="nav-item active" onclick="showTab('all')">
                <i data-lucide="megaphone"></i> Thông báo chung
            </button>
            <button id="btn-tab-2" class="nav-item" onclick="showTab('staff')">
                <i data-lucide="users"></i> Gửi đến nhân viên
            </button>
        </nav>
    </aside>

    <main class="main-container">
        <header id="tab-title"># thong_bao_chung</header>

        <div id="history-feed">
            <div class="msg-card">
                <div class="avatar">AD</div>
                <div>
                    <div style="font-size: 0.8rem; color: #94a3b8;">Admin Sonion • Hệ thống</div>
                    <div style="margin-top:5px;">Chào mừng! Hãy nhập nội dung bên dưới.</div>
                </div>
            </div>
        </div>

        <div class="input-area">
            <div class="input-box">
                <input type="text" id="post-title" placeholder="Tiêu đề thông báo...">
                <textarea id="post-content" rows="3" placeholder="Nội dung chi tiết..."></textarea>
                <button class="btn-send" onclick="sendNotify()">GỬI ĐI</button>
                <div style="clear:both"></div>
            </div>
        </div>
    </main>

    <script>
        lucide.createIcons();

        // HÀM CHUYỂN TAB (SỬA LỖI KHÔNG NHẤN ĐƯỢC)
        function showTab(type) {
            document.querySelectorAll('.nav-item').forEach(el => el.classList.remove('active'));
            if(type === 'all') {
                document.getElementById('btn-tab-1').classList.add('active');
                document.getElementById('tab-title').innerText = '# thong_bao_chung';
            } else {
                document.getElementById('btn-tab-2').classList.add('active');
                document.getElementById('tab-title').innerText = '# gui_den_nhan_vien';
            }
        }

        // HÀM GỬI THÔNG BÁO (HIỂN THỊ LÊN FEED)
        function sendNotify() {
            const title = document.getElementById('post-title').value;
            const content = document.getElementById('post-content').value;
            if(!title || !content) return alert("Vui lòng nhập đủ!");

            const feed = document.getElementById('history-feed');
            const div = document.createElement('div');
            div.className = 'msg-card';
            div.innerHTML = `
                <div class="avatar">AD</div>
                <div>
                    <div style="font-size: 0.8rem; color: #94a3b8;">Admin Sonion • Vừa xong</div>
                    <div style="font-weight:bold; margin: 5px 0;">${title}</div>
                    <div>${content}</div>
                </div>
            `;
            feed.appendChild(div);
            feed.scrollTop = feed.scrollHeight; // Cuộn xuống tin mới nhất

            // Xóa trắng ô nhập sau khi gửi
            document.getElementById('post-title').value = '';
            document.getElementById('post-content').value = '';
        }
    </script>
</body>
</html>
