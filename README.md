<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ thống thông báo nhân viên</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>📢 Hệ thống thông báo nhân viên</h1>
        </header>

        <main>
            <!-- Tab Navigation -->
            <div class="tab-navigation">
                <button class="tab-btn active" data-tab="broadcast">
                    📣 Gửi toàn bộ
                </button>
                <button class="tab-btn" data-tab="group">
                    👥 Gửi theo nhóm
                </button>
            </div>

            <!-- Tab Content -->
            <div class="tab-content">
                
                <!-- Tab 1: Broadcast to All -->
                <div id="broadcast" class="tab-panel active">
                    <form id="broadcastForm" class="notification-form">
                        <h2>Gửi thông báo đến toàn bộ nhân viên</h2>
                        
                        <div class="form-group">
                            <label for="broadcast-subject">Tiêu đề *</label>
                            <input 
                                type="text" 
                                id="broadcast-subject" 
                                name="subject" 
                                placeholder="Nhập tiêu đề thông báo..."
                                required
                            >
                        </div>

                        <div class="form-group">
                            <label for="broadcast-priority">Mức độ ưu tiên</label>
                            <select id="broadcast-priority" name="priority">
                                <option value="normal">Bình thường</option>
                                <option value="important">Quan trọng</option>
                                <option value="urgent">Khẩn cấp</option>
                            </select>
                        </div>

                        <div class="form-group">
                            <label for="broadcast-message">Nội dung thông báo *</label>
                            <textarea 
                                id="broadcast-message" 
                                name="message" 
                                rows="8"
                                placeholder="Nhập nội dung thông báo..."
                                required
                            ></textarea>
                        </div>

                        <div class="form-group">
                            <label for="broadcast-sender">Người gửi *</label>
                            <input 
                                type="text" 
                                id="broadcast-sender" 
                                name="sender" 
                                placeholder="Tên của bạn..."
                                required
                            >
                        </div>

                        <button type="submit" class="btn btn-primary btn-broadcast">
                            📤 Gửi thông báo đến toàn bộ nhân viên
                        </button>
                    </form>
                </div>

                <!-- Tab 2: Send to Group -->
                <div id="group" class="tab-panel">
                    <form id="groupForm" class="notification-form">
                        <h2>Gửi thông báo đến nhóm nhân viên</h2>
                        
                        <div class="form-group">
                            <label for="group-select">Chọn nhóm nhân viên *</label>
                            <select id="group-select" name="group" required>
                                <option value="">-- Chọn nhóm --</option>
                            </select>
                        </div>

                        <div class="form-group">
                            <label>Hoặc nhập danh sách email thủ công</label>
                            <textarea 
                                id="custom-emails" 
                                name="customEmails" 
                                rows="3"
                                placeholder="Nhập email, phân cách bằng dấu phẩy hoặc xuống dòng&#10;vd: a@company.com, b@company.com"
                            ></textarea>
                        </div>

                        <div class="form-group email-preview">
                            <label>Danh sách email sẽ nhận:</label>
                            <div id="email-list" class="email-tags"></div>
                            <span class="email-count">Tổng: <strong id="email-count">0</strong> người</span>
                        </div>

                        <div class="form-group">
                            <label for="group-subject">Tiêu đề *</label>
                            <input 
                                type="text" 
                                id="group-subject" 
                                name="subject" 
                                placeholder="Nhập tiêu đề thông báo..."
                                required
                            >
                        </div>

                        <div class="form-group">
                            <label for="group-priority">Mức độ ưu tiên</label>
                            <select id="group-priority" name="priority">
                                <option value="normal">Bình thường</option>
                                <option value="important">Quan trọng</option>
                                <option value="urgent">Khẩn cấp</option>
                            </select>
                        </div>

                        <div class="form-group">
                            <label for="group-message">Nội dung thông báo *</label>
                            <textarea 
                                id="group-message" 
                                name="message" 
                                rows="8"
                                placeholder="Nhập nội dung thông báo..."
                                required
                            ></textarea>
                        </div>

                        <div class="form-group">
                            <label for="group-sender">Người gửi *</label>
                            <input 
                                type="text" 
                                id="group-sender" 
                                name="sender" 
                                placeholder="Tên của bạn..."
                                required
                            >
                        </div>

                        <button type="submit" class="btn btn-primary btn-group">
                            📤 Gửi thông báo đến nhóm đã chọn
                        </button>
                    </form>
                </div>
            </div>
        </main>

        <!-- Toast Notification -->
        <div id="toast" class="toast"></div>

        <!-- Loading Overlay -->
        <div id="loading" class="loading-overlay">
            <div class="spinner"></div>
            <p>Đang gửi thông báo...</p>
        </div>
    </div>

    <script src="js/app.js"></script>
</body>
</html>
