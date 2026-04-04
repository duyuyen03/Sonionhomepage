<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HR Notification Center</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .sidebar-btn { @apply w-full py-4 px-2 text-white font-bold text-sm uppercase transition-all duration-200 border-b border-gray-700; }
        .sidebar-btn:hover { background-color: #333; }
        .active-tab { background-color: #1a1a1a; border-left: 4px solid #fff; }
    </style>
</head>
<body class="bg-gray-100 font-sans h-screen flex overflow-hidden">

    <div class="w-64 bg-[#2c2c2c] flex-shrink-0 flex flex-col">
        <div class="h-20 bg-black"></div> <button onclick="switchTab('all')" id="btn-all" class="sidebar-btn active-tab">Thông báo chung</button>
        <button onclick="switchTab('group')" id="btn-group" class="sidebar-btn">Thông báo nhóm nhân viên</button>
        <div class="flex-grow bg-[#8b8b8b]"></div>
    </div>

    <main class="flex-grow flex flex-col bg-white relative">
        
        <div id="tab-all" class="flex flex-col h-full">
            <div class="flex-grow p-6 overflow-y-auto bg-gray-50">
                <div class="max-w-2xl mx-auto space-y-4">
                    <div class="bg-white p-4 rounded shadow-sm border border-gray-200">
                        <p class="text-xs text-gray-400">Mar 18th</p>
                        <div class="flex items-center space-x-2 mt-1">
                            <div class="w-8 h-8 bg-green-700 rounded-full flex items-center justify-center text-white text-xs">AC</div>
                            <span class="font-bold text-sm">Admin Canteen 2</span>
                        </div>
                        <p class="text-sm mt-2 font-semibold text-blue-600 underline">MENU W12.26</p>
                    </div>
                </div>
            </div>
            
            <div class="p-4 bg-gray-100 border-t flex items-center space-x-3">
                <div class="relative flex-grow">
                    <input type="text" id="msg-all" placeholder="POST MESSAGE HERE" class="w-full border-2 border-gray-400 rounded-full py-2 px-10 focus:outline-none focus:border-black">
                    <button class="absolute left-3 top-2 text-gray-500 text-xl">+</button>
                </div>
                <button onclick="sendToN8N('all')" class="bg-white border-2 border-cyan-500 rounded-full p-2 hover:bg-cyan-50 transition">
                    <i class="fas fa-arrow-right text-cyan-500 text-xl"></i>
                </button>
            </div>
        </div>

        <div id="tab-group" class="hidden flex flex-col h-full items-center justify-center bg-gray-50">
            <div class="w-full max-w-lg p-8 text-center space-y-6">
                <a href="#" class="inline-block bg-[#e57373] text-white font-bold py-4 px-10 rounded-3xl shadow-lg hover:bg-red-500 transition uppercase tracking-wider">
                    Download Template Here
                </a>
                
                <div class="flex flex-col items-center space-y-4">
                    <div class="flex items-center space-x-4 w-full">
                        <label for="file-upload" class="cursor-pointer">
                            <i class="fas fa-paperclip text-4xl text-gray-800"></i>
                        </label>
                        <input id="file-upload" type="file" class="hidden" accept=".xlsx, .xls">
                        
                        <div class="flex-grow border border-gray-400 rounded p-2 text-left">
                            <input type="text" id="msg-title" placeholder="VUI LÒNG NHẬP TIÊU ĐỀ" class="w-full focus:outline-none">
                        </div>
                        
                        <button onclick="sendToN8N('group')" class="text-4xl text-gray-800 hover:text-black">
                            <i class="fas fa-paper-plane"></i>
                        </button>
                    </div>
                    <p id="file-name" class="text-sm text-green-600 font-medium"></p>
                </div>
            </div>
        </div>

    </main>

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
            document.getElementById('file-name').innerText = "Đã chọn: " + fileName;
        });

        // Gửi dữ liệu tới n8n
        async function sendToN8N(type) {
            const WEBHOOK_URL = 'URL_WEBHOOK_N8N_CUA_BAN'; // Thay bằng URL từ n8n
            let payload = {};

            if (type === 'all') {
                const message = document.getElementById('msg-all').value;
                if(!message) return alert("Vui lòng nhập nội dung!");
                payload = { type: 'all', content: message };
            } else {
                const title = document.getElementById('msg-title').value;
                const fileInput = document.getElementById('file-upload');
                if(!title || fileInput.files.length === 0) return alert("Vui lòng nhập tiêu đề và đính kèm file!");
                
                // Lưu ý: Gửi file qua Webhook cần xử lý FormData hoặc Base64.
                // Ở đây demo gửi thông tin cơ bản.
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
                if (response.ok) alert("Đã gửi đến n8n thành công!");
            } catch (error) {
                console.error("Lỗi:", error);
                alert("Không thể kết nối đến n8n.");
            }
        }
    </script>
</body>
</html>
