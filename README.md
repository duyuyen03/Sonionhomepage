<!DOCTYPE html>
<html>
<head>
    <title>Company Announcement System</title>
    <style>
        body { font-family: sans-serif; max-width: 600px; margin: 20px auto; padding: 20px; }
        .form-group { margin-bottom: 15px; }
        label { display: block; font-weight: bold; }
        input, textarea { width: 100%; padding: 8px; margin-top: 5px; }
        button { padding: 10px 20px; background: #007bff; color: white; border: none; cursor: pointer; }
        #history { margin-top: 30px; border-top: 1px solid #ccc; }
    </style>
</head>
<body>
    <h2>Tạo thông báo mới</h2>
    <form id="announcementForm">
        <div class="form-group">
            <label>Tiêu đề:</label>
            <input type="text" id="title" required>
        </div>
        <div class="form-group">
            <label>Nội dung:</label>
            <textarea id="content" rows="5" required></textarea>
        </div>
        <div class="form-group">
            <label>Đính kèm (Hình ảnh/File):</label>
            <input type="file" id="attachment">
        </div>
        <button type="submit">Gửi thông báo</button>
    </form>

    <div id="history">
        <h3>Lịch sử thông báo</h3>
        <ul id="historyList"></ul>
    </div>

    <script>
        const form = document.getElementById('announcementForm');
        
        form.addEventListener('submit', async (e) => {
            e.preventDefault();
            
            const fileInput = document.getElementById('attachment');
            let fileData = null;

            if (fileInput.files.length > 0) {
                const file = fileInput.files[0];
                fileData = await toBase64(file);
            }

            const payload = {
                title: document.getElementById('title').value,
                content: document.getElementById('content').value,
                fileName: fileInput.files[0]?.name || "",
                fileContent: fileData,
                timestamp: new Date().toISOString()
            };

            // Gửi đến Webhook của bạn (n8n hoặc Power Automate)
            const webhookUrl = 'YOUR_WEBHOOK_URL_HERE';
            
            try {
                await fetch(webhookUrl, {
                    method: 'POST',
                    body: JSON.stringify(payload),
                    headers: { 'Content-Type': 'application/json' }
                });
                alert('Thông báo đã được gửi thành công!');
                form.reset();
                updateHistory(payload);
            } catch (error) {
                console.error('Lỗi:', error);
            }
        });

        const toBase64 = file => new Promise((resolve, reject) => {
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = () => resolve(reader.result.split(',')[1]);
            reader.onerror = error => reject(error);
        });

        function updateHistory(data) {
            const list = document.getElementById('historyList');
            const item = document.createElement('li');
            item.textContent = `${new Date(data.timestamp).toLocaleString()}: ${data.title}`;
            list.prepend(item);
        }
    </script>
</body>
</html>
