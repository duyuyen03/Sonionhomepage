<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sonion Staff Notification Center</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        /* Tùy chỉnh màu sắc và hiệu ứng cho sidebar */
        .sidebar-btn {
            @apply w-full py-4 px-3 text-gray-300 font-medium text-sm text-left uppercase transition-all duration-200;
        }
        .sidebar-btn:hover {
            background-color: #3f3f3f;
            color: white;
        }
        .active-tab {
            background-color: #1c1c1c;
            color: white;
            border-left: 4px solid #4fd1c5; /* Màu Teal làm điểm nhấn */
        }
    </style>
</head>
<body class="bg-gray-100 font-sans h-screen flex overflow-hidden">

    <div class="w-65 bg-[#2d2d2d] flex-shrink-0 flex flex-col border-r border-gray-700">
        <div class="h-24 bg-black flex items-center justify-center border-b border-gray-700">
            <span class="text-white text-3xl font-bold tracking-tight">SONION</span>
        </div>
        
        <nav class="flex-grow">
            <button onclick="switchTab('all')" id="btn-all" class="sidebar-btn active-tab mt-2">
                Thông báo chung
            </button>
            <button onclick="switchTab('group')" id="btn-group" class="sidebar-btn">
                Thông báo nhóm nhân viên
            </button>
        </nav>
        
        <div class="p-4 bg-[#808080] border-t border-gray-700">
            <p class="text-xs text-center text-gray-200">HR Tools v1.0</p>
        </div>
    </div>

    <main class="w-[520px] mx-auto flex flex-col bg-white border-l border-r border-gray-200 shadow-xl h-full">
        
        <div id="tab-all" class="flex flex-col h-full">
            
            <div class="flex-grow p-6 overflow-y-auto space-y-6 bg-gray-50">
                <div class="flex items-start space-x-3">
                    <div class="w-9 h-9 rounded-full bg-green-700 flex items-center justify-center text-white text-sm font-bold flex-shrink-0">
                        AC
                    </div>
                    <div class="flex-grow bg-white p-4 rounded-xl shadow-sm border border-gray-100">
                        <div class="flex items-center justify-between">
                            <span class="font-bold text-gray-800 text-sm">Admin Canteen 2</span>
                            <span class="text-xs text-gray-400">Mar 18th</span>
                        </div>
                        <p class="text-blue-600 text-sm mt-2 font-medium hover:underline cursor-pointer">
                            MENU W12.26
                        </p>
                    </div>
                </div>
            </div>
            
            <div class="p-4 bg-gray-100 border-t border-gray-200 flex items-center space-x-3">
                <div class="relative flex-grow">
                    <input type="text" id="msg-all" placeholder="POST MESSAGE HERE" class="w-full border-2 border-gray-400 rounded-full py-2.5 px-12 focus:outline-none focus:border-cyan-500 transition shadow-inner">
                    <button class="absolute left-4 top-3 text-gray-500 text-xl font-bold">+</button>
                </div>
                <button onclick="sendToN8N('all')" class="bg-white border-2 border-cyan-500 rounded-full p-2.5 hover:bg-cyan-50 transition shadow">
                    <i class="fas fa-arrow-right text-cyan-500 text-xl"></i>
                </button>
            </div>
        </div>

        <div id="tab-group" class="hidden flex flex-col h-full items-center justify-center bg-gray-50 p-10 space-y-8">
            <a href="#" class="inline-block bg-[#e57373] text-white font-bold py-4 px-12 rounded-full shadow-lg hover:bg-red-500 transition-all uppercase tracking-wider text-sm transform hover:scale-105">
                Download Template Here
            </a>
            
            <div class="w-full space-y-6 border-t border-gray-200 pt-8">
                <div class="flex items-center space-x-4 w-full">
                    <label for="file-upload" class="cursor-pointer">
                        <i class="fas fa-paperclip text-5xl text-gray-800 hover:text-black transition"></i>
                    </label>
                    <input id="file-upload" type="file" class="hidden" accept=".xlsx, .xls">
                    
                    <div class="flex-grow border-2 border-gray-400 rounded-lg p-3 bg-white shadow-inner">
                        <input type="text" id="msg-title" placeholder="VUI LÒNG NHẬP TIÊU ĐỀ" class="w-full focus:outline-none text-lg">
                    </div>
                    
                    <button onclick="sendToN8N('group')" class="text-5xl text-gray-800 hover:text-cyan-600 transition">
                        <i class="fas fa-paper-plane"></i>
                    </button>
                </div>
                <p id="file-name" class="text-sm text-center text-cyan-700 font-medium h-5"></p>
            </div>
        </div>

    </main>

    <div class="flex-grow bg-gray-100"></div>

    <script>
        // Chuyển đổi giữa các Tab
        function switchTab(type) {
            const tabAll = document.getElementById('tab-all');
            const tabGroup = document.getElementById('tab-group');
            const btnAll = document.getElementById('btn-all');
            const btnGroup = document.getElementById('btn-group');

            if(type === 'all') {
                tabAll.classList.remove('hidden');
                tabGroup.classList.add('hidden');
                btnAll.classList.add('active-tab');
                btnGroup.classList.remove('active-tab');
            } else {
                tabAll.classList.add('hidden');
                tabGroup.classList.remove('hidden');
                btnAll.classList.remove('active-tab');
                btnGroup.classList.add('active-tab');
            }
        }

        // Hiển thị tên file khi chọn
        document.getElementById('file-upload').addEventListener('change', function(e) {
            const fileName = e.target.files[0]?.name || "";
            if (fileName) {
                document.getElementById('file-name').innerText = "Đã chọn file: " + fileName;
            } else {
                document.getElementById('file-name').innerText = "";
            }
        });

        // Hàm demo gửi dữ liệu tới n8n (Cần cấu hình URL n8n)
        async function sendToN8N(type) {
            // THAY THẾ 'URL_WEBHOOK_N8N_CUA_BAN' bằng URL thực tế từ Webhook Node của n8n
            const WEBHOOK_URL = 'URL_WEBHOOK_N8N_CUA_BAN'; 
            if (WEBHOOK_URL === 'URL_WEBHOOK_N8N_CUA_BAN') {
                return alert("Vui lòng cấu hình URL Webhook n8n trong code trước khi gửi.");
            }

            let payload;

            if (type === 'all') {
                const message = document.getElementById('msg-all').value;
                if(!message) return alert("Vui lòng nhập nội dung thông báo chung!");
                payload = { type: 'all', content: message };
            } else {
                const title = document.getElementById('msg-title').value;
                const fileInput = document.getElementById('file-upload');
                if(!title || fileInput.files.length === 0) return alert("Vui lòng nhập tiêu đề và đính kèm file template Excel!");
                
                // Demo gửi thông tin cơ bản. Để gửi file thật, cần dùng FormData.
                payload = { 
                    type: 'group', 
                    title: title, 
                    fileName: fileInput.files[0].name 
                };
            }

            try {
                const response = await fetch(WEBHOOK_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (response.ok) alert("Thông báo đã được gửi đến n8n thành công!");
                else alert("Đã có lỗi xảy ra khi gửi đến n8n.");
            } catch (error) {
                console.error("Lỗi kết nối:", error);
                alert("Không thể kết nối đến n8n. Vui lòng kiểm tra lại URL Webhook.");
            }
        }
    </script>
</body>
</html>
