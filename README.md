<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Email Automation Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
</head>
<body class="bg-gray-50 flex h-screen overflow-hidden">

    <aside class="w-64 bg-slate-800 text-white flex flex-col">
        <div class="p-6 text-2xl font-bold border-b border-slate-700 text-blue-400">
            <i class="fas fa-paper-plane mr-2"></i>Email Bot
        </div>
        <nav class="flex-1 p-4 space-y-2">
            <button onclick="showSection('config')" id="menu-config" class="w-full flex items-center p-3 rounded-lg bg-slate-700 hover:bg-slate-600 transition">
                <i class="fas fa-cog w-6"></i> Cấu hình kết nối
            </button>
            <button onclick="showSection('broadcast')" id="menu-broadcast" class="w-full flex items-center p-3 rounded-lg hover:bg-slate-700 transition">
                <i class="fas fa-bullhorn w-6"></i> Gửi thông báo chung
            </button>
            <button onclick="showSection('excel')" id="menu-excel" class="w-full flex items-center p-3 rounded-lg hover:bg-slate-700 transition">
                <i class="fas fa-file-excel w-6"></i> Gửi theo file Excel
            </button>
        </nav>
        <div class="p-4 text-xs text-slate-500 border-t border-slate-700">
            Powered by n8n & GitHub
        </div>
    </aside>

    <main class="flex-1 flex flex-col overflow-y-auto">
        <header class="bg-white shadow-sm p-4 flex justify-between items-center">
            <h2 id="page-title" class="text-xl font-semibold text-gray-700">Cấu hình kết nối</h2>
            <div id="status-badge" class="px-3 py-1 rounded-full text-sm bg-gray-200 text-gray-600 italic">Đang chờ lệnh...</div>
        </header>

        <div class="p-8 max-w-4xl">
            
            <section id="section-config" class="space-y-6">
                <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-200">
                    <h3 class="text-lg font-medium mb-4">Kết nối với n8n</h3>
                    <div class="space-y-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">Webhook URL (n8n Production)</label>
                            <input type="text" id="webhookUrl" placeholder="https://n8n.yourdomain.com/webhook/..." class="w-full p-2.5 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none">
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">Mã bảo mật (Token)</label>
                            <input type="password" id="apiToken" placeholder="Nhập mã bí mật để n8n xác thực" class="w-full p-2.5 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none">
                        </div>
                    </div>
                </div>
            </section>

            <section id="section-broadcast" class="hidden space-y-6">
                <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-200">
                    <h3 class="text-lg font-medium mb-4">Gửi thông báo cho toàn bộ nhân viên</h3>
                    <textarea id="announcementText" rows="6" class="w-full p-3 border rounded-lg focus:ring-2 focus:ring-blue-500 outline-none" placeholder="Viết nội dung thông báo gửi đến toàn công ty..."></textarea>
                    <button onclick="sendAnnouncement()" class="mt-4 bg-blue-600 text-white px-6 py-2.5 rounded-lg hover:bg-blue-700 font-medium transition shadow-md">
                        <i class="fas fa-paper-plane mr-2"></i> Bấm để gửi ngay
                    </button>
                </div>
            </section>

            <section id="section-excel" class="hidden space-y-6">
                <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-200">
                    <h3 class="text-lg font-medium mb-2">Gửi theo danh sách Excel</h3>
                    <p class="text-sm text-gray-500 mb-4 italic">Hệ thống sẽ gửi mail cho từng dòng trong file. Cột chứa email phải có tên là "Email".</p>
                    <div class="border-2 border-dashed border-gray-300 rounded-xl p-10 text-center hover:border-blue-400 transition cursor-pointer bg-gray-50" onclick="document.getElementById('excelFile').click()">
                        <i class="fas fa-cloud-upload-alt text-4xl text-gray-400 mb-3"></i>
                        <p class="text-gray-600">Click để chọn file .xlsx hoặc .xls</p>
                        <input type="file" id="excelFile" accept=".xlsx, .xls" class="hidden">
                        <p id="fileNameDisplay" class="mt-2 text-blue-600 font-medium"></p>
                    </div>
                    <button onclick="sendExcelData()" class="mt-6 bg-green-600 text-white px-6 py-2.5 rounded-lg hover:bg-green-700 font-medium transition shadow-md w-full sm:w-auto">
                        <i class="fas fa-upload mr-2"></i> Xử lý và Gửi mail
                    </button>
                </div>
            </section>

        </div>
    </main>

    <script>
        // Điều hướng giữa các mục
        function showSection(id) {
            // Ẩn tất cả section
            ['config', 'broadcast', 'excel'].forEach(s => {
                document.getElementById('section-' + s).classList.add('hidden');
                document.getElementById('menu-' + s).classList.remove('bg-slate-700', 'text-blue-400');
            });
            
            // Hiển thị section được chọn
            document.getElementById('section-' + id).classList.remove('hidden');
            document.getElementById('menu-' + id).classList.add('bg-slate-700', 'text-blue-400');

            // Đổi tiêu đề header
            const titles = {config: 'Cấu hình kết nối', broadcast: 'Gửi thông báo chung', excel: 'Gửi theo file Excel'};
            document.getElementById('page-title').innerText = titles[id];
        }

        // Hiển thị tên file khi chọn
        document.getElementById('excelFile').addEventListener('change', function(e) {
            const fileName = e.target.files[0] ? e.target.files[0].name : "";
            document.getElementById('fileNameDisplay').innerText = fileName;
        });

        // Hàm thông báo trạng thái
        function updateStatus(msg, type = 'info') {
            const badge = document.getElementById('status-badge');
            badge.innerText = msg;
            if (type === 'success') badge.className = "px-3 py-1 rounded-full text-sm bg-green-100 text-green-700 font-bold";
            else if (type === 'error') badge.className = "px-3 py-1 rounded-full text-sm bg-red-100 text-red-700 font-bold";
            else badge.className = "px-3 py-1 rounded-full text-sm bg-gray-200 text-gray-600 italic";
        }

        // Gửi dữ liệu qua n8n
        async function postToN8n(payload) {
            const url = document.getElementById('webhookUrl').value;
            const token = document.getElementById('apiToken').value;

            if (!url) {
                alert("Vui lòng điền URL n8n tại phần Cấu hình!");
                showSection('config');
                return;
            }

            updateStatus("Đang gửi yêu cầu...");

            try {
                const response = await fetch(url, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                        'Authorization': `Bearer ${token}`
                    },
                    body: JSON.stringify(payload)
                });

                if (response.ok) updateStatus("Hoàn thành: Gửi thành công!", "success");
                else updateStatus("Lỗi: " + response.statusText, "error");
            } catch (err) {
                updateStatus("Lỗi kết nối server!", "error");
            }
        }

        function sendAnnouncement() {
            const content = document.getElementById('announcementText').value;
            if (!content) return alert("Vui lòng nhập nội dung!");
            postToN8n({ type: 'broadcast', content: content });
        }

        function sendExcelData() {
            const file = document.getElementById('excelFile').files[0];
            if (!file) return alert("Vui lòng chọn file Excel!");

            const reader = new FileReader();
            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, {type: 'array'});
                const jsonData = XLSX.utils.sheet_to_json(workbook.Sheets[workbook.SheetNames[0]]);
                postToN8n({ type: 'excel_list', data: jsonData });
            };
            reader.readAsArrayBuffer(file);
        }
    </script>
</body>
</html>
