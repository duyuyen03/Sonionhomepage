<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ thống Thông báo Nội bộ</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 min-h-screen p-4 md:p-8">
    <div class="max-w-4xl mx-auto grid grid-cols-1 md:grid-cols-3 gap-6">
        
        <div class="md:col-span-2 bg-white p-6 rounded-lg shadow-md">
            <h2 class="text-2xl font-bold mb-6 text-blue-700">Tạo Thông Báo Mới</h2>
            <form id="announcementForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-semibold text-gray-700">Tiêu đề thông báo</label>
                    <input type="text" id="title" required class="w-full mt-1 p-2 border rounded-md focus:ring-2 focus:ring-blue-500 outline-none">
                </div>

                <div>
                    <label class="block text-sm font-semibold text-gray-700">Nội dung chi tiết</label>
                    <textarea id="content" rows="6" required class="w-full mt-1 p-2 border rounded-md focus:ring-2 focus:ring-blue-500 outline-none" placeholder="Nhập nội dung gửi đến nhân viên..."></textarea>
                </div>

                <div>
                    <label class="block text-sm font-semibold text-gray-700">Đính kèm (Hình ảnh/Tài liệu)</label>
                    <input type="file" id="attachment" class="w-full mt-1 text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-md file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100">
                </div>

                <button type="submit" id="submitBtn" class="w-full bg-blue-600 text-white font-bold py-3 rounded-md hover:bg-blue-700 transition duration-200">
                    GỬI THÔNG BÁO & EMAIL
                </button>
            </form>
        </div>

        <div class="bg-white p-6 rounded-lg shadow-md">
            <h2 class="text-xl font-bold mb-4 text-gray-800 border-b pb-2">Lịch sử gửi</h2>
            <div id="historyContainer" class="space-y-3 max-h-[500px] overflow-y-auto">
                <p class="text-gray-400 italic text-sm text-center">Đang tải lịch sử...</p>
            </div>
        </div>
    </div>

    <script>
        const WEBHOOK_URL = 'THAY_URL_WEBHOOK_CUA_BAN_TAI_DAY'; 
        const form = document.getElementById('announcementForm');
        const historyContainer = document.getElementById('historyContainer');

        // 1. Hàm chuyển file sang Base64 để gửi qua JSON
        const toBase64 = file => new Promise((resolve, reject) => {
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = () => resolve(reader.result.split(',')[1]);
            reader.onerror = error => reject(error);
        });

        // 2. Xử lý khi Submit form
        form.addEventListener('submit', async (e) => {
            e.preventDefault();
            const btn = document.getElementById('submitBtn');
            btn.disabled = true;
            btn.innerText = 'Đang xử lý...';

            const fileInput = document.getElementById('attachment');
            let fileData = null;
            let fileName = "";

            if (fileInput.files.length > 0) {
                const file = fileInput.files[0];
                fileName = file.name;
                fileData = await toBase64(file);
            }

            const payload = {
                title: document.getElementById('title').value,
                content: document.getElementById('content').value,
                fileName: fileName,
                fileContent: fileData,
                timestamp: new Date().toLocaleString('vi-VN')
            };

            try {
                const response = await fetch(WEBHOOK_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (response.ok) {
                    alert('Thành công! Thông báo đã được gửi và lưu trữ.');
                    form.reset();
                    // Tạm thời thêm vào UI history
                    addEntryToUI(payload);
                } else {
                    throw new Error('Lỗi từ Server');
                }
            } catch (error) {
                alert('Có lỗi xảy ra khi gửi dữ liệu.');
                console.error(error);
            } finally {
                btn.disabled = false;
                btn.innerText = 'GỬI THÔNG BÁO & EMAIL';
            }
        });

        function addEntryToUI(data) {
            const item = document.createElement('div');
            item.className = 'p-3 border-l-4 border-blue-500 bg-blue-50 rounded';
            item.innerHTML = `
                <div class="text-xs text-gray-500">${data.timestamp}</div>
                <div class="font-semibold text-gray-800">${data.title}</div>
            `;
            if(historyContainer.querySelector('p')) historyContainer.innerHTML = '';
            historyContainer.prepend(item);
        }

        // 3. (Tùy chọn) Hàm fetch lấy lịch sử từ file JSON hoặc Google Sheet
        async function loadHistory() {
            // Nếu bạn có API trả về danh sách lịch sử, hãy gọi ở đây.
            // Tạm thời để trống hoặc dùng LocalStorage.
        }
        loadHistory();
    </script>
</body>
</html>
