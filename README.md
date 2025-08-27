<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ Thống Nộp Báo Cáo</title>
    <script src="https://apis.google.com/js/api.js" async defer></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .container {
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            padding: 40px;
            max-width: 600px;
            width: 100%;
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
        }

        .header h1 {
            color: #333;
            font-size: 28px;
            margin-bottom: 10px;
        }

        .header p {
            color: #666;
            font-size: 16px;
        }

        .status-box {
            padding: 15px;
            border-radius: 10px;
            margin: 20px 0;
            text-align: center;
            font-weight: 500;
        }

        .status-loading {
            background: #fff3cd;
            color: #856404;
            border-left: 4px solid #ffc107;
        }

        .status-ready {
            background: #d4edda;
            color: #155724;
            border-left: 4px solid #28a745;
        }

        .status-error {
            background: #f8d7da;
            color: #721c24;
            border-left: 4px solid #dc3545;
        }

        .debug-section {
            background: #f8f9fa;
            border: 1px solid #dee2e6;
            border-radius: 8px;
            padding: 15px;
            margin: 15px 0;
            font-family: 'Courier New', monospace;
            font-size: 12px;
            max-height: 200px;
            overflow-y: auto;
            display: none;
        }

        .debug-line {
            padding: 2px 0;
            border-bottom: 1px solid #eee;
        }

        .debug-error {
            color: #dc3545;
            font-weight: bold;
        }

        .debug-success {
            color: #28a745;
            font-weight: bold;
        }

        .debug-info {
            color: #17a2b8;
        }

        .form-group {
            margin-bottom: 25px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #333;
        }

        input[type="text"], select {
            width: 100%;
            padding: 12px 16px;
            border: 2px solid #e1e5e9;
            border-radius: 8px;
            font-size: 16px;
            transition: border-color 0.3s ease;
        }

        input[type="text"]:focus, select:focus {
            outline: none;
            border-color: #667eea;
        }

        .file-input-container {
            position: relative;
            display: inline-block;
            width: 100%;
        }

        .file-input {
            width: 100%;
            padding: 12px 16px;
            border: 2px dashed #e1e5e9;
            border-radius: 8px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s ease;
            background: #f8f9fa;
        }

        .file-input:hover {
            border-color: #667eea;
            background: #f0f4ff;
        }

        .file-input.dragover {
            border-color: #667eea;
            background: #e8f0ff;
        }

        input[type="file"] {
            position: absolute;
            opacity: 0;
            width: 100%;
            height: 100%;
            cursor: pointer;
        }

        .report-list {
            max-height: 300px;
            overflow-y: auto;
            border: 2px solid #e1e5e9;
            border-radius: 8px;
            padding: 15px;
            background: #f8f9fa;
        }

        .report-item {
            display: flex;
            align-items: center;
            padding: 12px;
            margin-bottom: 10px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
            transition: all 0.3s ease;
        }

        .report-item:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }

        .report-item.submitted {
            background: #e8f5e8;
            opacity: 0.7;
        }

        .report-item input[type="radio"] {
            margin-right: 12px;
            transform: scale(1.2);
        }

        .report-info {
            flex: 1;
        }

        .report-title {
            font-weight: 600;
            color: #333;
            margin-bottom: 4px;
        }

        .report-recipient {
            font-size: 14px;
            color: #666;
        }

        .report-status {
            font-size: 12px;
            color: #28a745;
            font-style: italic;
        }

        .btn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 15px 30px;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            width: 100%;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 16px rgba(102, 126, 234, 0.3);
        }

        .btn:disabled {
            background: #ccc;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        .login-btn {
            background: #4285f4;
            margin-bottom: 20px;
        }

        .debug-btn {
            background: #6c757d;
            padding: 8px 16px;
            font-size: 12px;
            margin-bottom: 15px;
        }

        .login-section, .upload-section {
            display: none;
        }

        .login-section.active, .upload-section.active {
            display: block;
        }

        .progress-bar {
            width: 100%;
            height: 6px;
            background: #e1e5e9;
            border-radius: 3px;
            overflow: hidden;
            margin: 20px 0;
            display: none;
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(90deg, #667eea, #764ba2);
            width: 0%;
            transition: width 0.3s ease;
        }

        .notification {
            padding: 15px;
            border-radius: 8px;
            margin: 20px 0;
            display: none;
        }

        .notification.success {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .notification.error {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }

        .notification.info {
            background: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }

        .user-info {
            background: #e8f0ff;
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 25px;
            text-align: center;
        }

        .loading {
            text-align: center;
            padding: 20px;
            color: #666;
        }

        .spinner {
            border: 3px solid #f3f3f3;
            border-top: 3px solid #667eea;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
            margin: 0 auto 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .troubleshoot-panel {
            background: #f1f3f4;
            border: 1px solid #dadce0;
            border-radius: 8px;
            padding: 20px;
            margin: 20px 0;
            display: none;
        }

        .troubleshoot-step {
            margin: 10px 0;
            padding: 10px;
            background: white;
            border-radius: 6px;
            border-left: 4px solid #4285f4;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Hệ Thống Nộp Báo Cáo</h1>
            <p>Nộp báo cáo một cách nhanh chóng và hiệu quả</p>
        </div>

        <button class="btn debug-btn" onclick="toggleDebug()">Debug Console</button>
        
        <div class="debug-section" id="debugSection">
            <div id="debugLog">Starting initialization...</div>
        </div>

        <div class="status-box status-loading" id="statusBox">
            <div class="spinner" style="width: 20px; height: 20px; display: inline-block; margin-right: 10px;"></div>
            Đang khởi tạo Google APIs...
        </div>

        <div class="troubleshoot-panel" id="troubleshootPanel">
            <h3>Hướng dẫn khắc phục lỗi</h3>
            <div class="troubleshoot-step">
                <strong>1. Kiểm tra Chrome Extensions:</strong><br>
                Tắt tất cả extensions hoặc thử Incognito mode
            </div>
            <div class="troubleshoot-step">
                <strong>2. Kiểm tra Console Errors:</strong><br>
                Mở DevTools > Console để xem chi tiết lỗi
            </div>
            <div class="troubleshoot-step">
                <strong>3. Thử HTTPS:</strong><br>
                Sử dụng ngrok hoặc host trên HTTPS domain
            </div>
            <div class="troubleshoot-step">
                <strong>4. Alternative Solution:</strong><br>
                <button class="btn" onclick="switchToDemo()">Chuyển sang Demo Mode</button>
            </div>
        </div>

        <!-- Phần đăng nhập -->
        <div class="login-section" id="loginSection">
            <button class="btn login-btn" id="signInBtn" onclick="performSignIn()" disabled>
                Đăng nhập với Google
            </button>
        </div>

        <!-- Phần upload -->
        <div class="upload-section" id="uploadSection">
            <div class="user-info" id="userInfo"></div>

            <div class="form-group">
                <label for="schoolName">Tên trường (viết tắt):</label>
                <input type="text" id="schoolName" placeholder="VD: DHBK, DHKT..." required>
            </div>

            <div class="form-group">
                <label for="reportFile">Chọn file báo cáo:</label>
                <div class="file-input-container">
                    <div class="file-input" id="fileInputArea">
                        <span>Click để chọn file hoặc kéo thả file vào đây</span>
                        <br><small>Chỉ nhận: PDF, DOC, DOCX, XLS, XLSX, MP4</small>
                    </div>
                    <input type="file" id="reportFile" accept=".pdf,.doc,.docx,.xls,.xlsx,.mp4" required>
                </div>
                <div id="selectedFile" style="margin-top: 10px; font-size: 14px; color: #666;"></div>
            </div>

            <div class="form-group">
                <label>Chọn báo cáo cần nộp:</label>
                <div class="loading" id="loadingReports">
                    <div class="spinner"></div>
                    Đang tải danh sách báo cáo...
                </div>
                <div class="report-list" id="reportList" style="display: none;"></div>
            </div>

            <div class="progress-bar" id="progressBar">
                <div class="progress-fill" id="progressFill"></div>
            </div>

            <button class="btn" id="submitBtn" onclick="submitReport()" disabled>
                Nộp Báo Cáo
            </button>

            <div class="notification" id="notification"></div>
        </div>
    </div>

    <script>
        // Cấu hình
        const CONFIG = {
            CLIENT_ID: 'AIzaSyAHUdJ7bykPbOs7hgBQ09t8oSs-M2GrFG8',
            SPREADSHEET_ID: '1JVXMPoAC9QmD_YmCamhY9iqgUsoH1wo1laCvFg9Z-vE',
            PARENT_FOLDER_ID: '1cGPO867sCGWI48K2ypCDBM2ZYWbyLGsq',
            SCOPES: 'https://www.googleapis.com/auth/spreadsheets https://www.googleapis.com/auth/drive.file'
        };

        // Biến global
        let gapi, google;
        let authInstance = null;
        let currentUser = null;
        let reportData = [];
        let submissionHistory = [];
        let initAttempts = 0;
        const MAX_INIT_ATTEMPTS = 3;

        // Debug functions
        function debugLog(message, type = 'info') {
            const timestamp = new Date().toLocaleTimeString();
            const logElement = document.getElementById('debugLog');
            const className = `debug-${type}`;
            
            console.log(`[${timestamp}] ${message}`);
            
            if (logElement) {
                logElement.innerHTML += `<div class="${className}">[${timestamp}] ${message}</div>`;
                logElement.scrollTop = logElement.scrollHeight;
            }
        }

        function toggleDebug() {
            const debugSection = document.getElementById('debugSection');
            debugSection.style.display = debugSection.style.display === 'none' ? 'block' : 'none';
        }

        function updateStatus(type, message) {
            const statusBox = document.getElementById('statusBox');
            statusBox.className = `status-box status-${type}`;
            statusBox.innerHTML = message;
        }

        // Khởi tạo khi window load
        window.addEventListener('load', function() {
            debugLog('Window loaded, waiting for GAPI...');
            waitForGAPI();
        });

        function waitForGAPI() {
            if (typeof gapi !== 'undefined') {
                debugLog('GAPI is available, starting initialization');
                initializeGoogleAPI();
            } else {
                debugLog('Waiting for GAPI to load...');
                setTimeout(() => {
                    initAttempts++;
                    if (initAttempts < MAX_INIT_ATTEMPTS) {
                        waitForGAPI();
                    } else {
                        handleInitFailure();
                    }
                }, 1000);
            }
        }

        function initializeGoogleAPI() {
            debugLog('Loading GAPI modules...');
            
            gapi.load('auth2:client', {
                callback: initClient,
                onerror: function(error) {
                    debugLog('Failed to load GAPI modules: ' + JSON.stringify(error), 'error');
                    handleInitFailure();
                }
            });
        }

        async function initClient() {
            try {
                debugLog('Initializing GAPI client...');
                
                await gapi.client.init({
                    clientId: CONFIG.CLIENT_ID,
                    scope: CONFIG.SCOPES,
                    discoveryDocs: [
                        'https://sheets.googleapis.com/$discovery/rest?version=v4',
                        'https://www.googleapis.com/discovery/v1/apis/drive/v3/rest'
                    ]
                });

                debugLog('GAPI client initialized successfully', 'success');
                
                authInstance = gapi.auth2.getAuthInstance();
                
                // Listen for sign-in state changes
                authInstance.isSignedIn.listen(updateSignInStatus);
                
                // Handle initial sign-in state
                updateSignInStatus(authInstance.isSignedIn.get());
                
                updateStatus('ready', 'Sẵn sàng đăng nhập');
                
            } catch (error) {
                debugLog('Client initialization failed: ' + error.message, 'error');
                handleInitFailure();
            }
        }

        function updateSignInStatus(isSignedIn) {
            debugLog(`Sign-in status changed: ${isSignedIn}`);
            
            if (isSignedIn) {
                currentUser = authInstance.currentUser.get();
                const profile = currentUser.getBasicProfile();
                
                debugLog(`Signed in as: ${profile.getEmail()}`, 'success');
                
                document.getElementById('loginSection').classList.remove('active');
                document.getElementById('uploadSection').classList.add('active');
                document.getElementById('statusBox').style.display = 'none';
                
                document.getElementById('userInfo').innerHTML = `
                    <strong>Xin chào, ${profile.getName()}</strong><br>
                    <small>${profile.getEmail()}</small>
                `;
                
                loadData();
                setupFileHandlers();
                
            } else {
                debugLog('User not signed in');
                document.getElementById('loginSection').classList.add('active');
                document.getElementById('uploadSection').classList.remove('active');
                document.getElementById('signInBtn').disabled = false;
            }
        }

        function performSignIn() {
            if (!authInstance) {
                showNotification('Google API chưa sẵn sàng', 'error');
                return;
            }
            
            debugLog('Attempting sign in...');
            authInstance.signIn().catch(error => {
                debugLog('Sign in error: ' + error.error, 'error');
                showNotification('Lỗi đăng nhập: ' + error.error, 'error');
            });
        }

        function handleInitFailure() {
            debugLog('Initialization failed after multiple attempts', 'error');
            updateStatus('error', 'Không thể khởi tạo Google APIs');
            document.getElementById('troubleshootPanel').style.display = 'block';
            
            showNotification(
                'Không thể kết nối Google APIs. Có thể do:\n' +
                '• Browser security policies\n' +
                '• Network/firewall issues\n' +
                '• OAuth configuration\n\n' +
                'Vui lòng thử Demo mode hoặc kiểm tra troubleshooting panel.',
                'error'
            );
        }

        function switchToDemo() {
            debugLog('Switching to demo mode', 'info');
            updateStatus('ready', 'Demo Mode - Không cần Google APIs');
            
            // Simulate demo user
            document.getElementById('loginSection').classList.remove('active');
            document.getElementById('uploadSection').classList.add('active');
            document.getElementById('statusBox').style.display = 'none';
            document.getElementById('troubleshootPanel').style.display = 'none';
            
            document.getElementById('userInfo').innerHTML = `
                <strong>Demo User</strong><br>
                <small>demo@example.com</small><br>
                <em style="color: #ffa500;">Demo mode - không upload thật</em>
            `;
            
            loadDemoData();
            setupFileHandlers();
        }

        function loadDemoData() {
            debugLog('Loading demo data...');
            
            // Demo data
            reportData = [
                ['1', 'Phòng Tài Chính', 'Đại học Bách Khoa', 'Báo cáo tài chính quý III', 'TC_Q3_2024', 'Chờ'],
                ['2', 'Ban Giám Hiệu', 'Đại học Bách Khoa', 'Báo cáo hoạt động tháng 8', 'BGH_T8_2024', 'Chờ'],
                ['3', 'Phòng Đào Tạo', 'Đại học Bách Khoa', 'Báo cáo học vụ học kỳ 1', 'DT_HK1_2024', 'Chờ']
            ];
            
            submissionHistory = [
                ['demo@example.com', 'BGH_T8_2024', '24/08/2024 14:30', 'DHBK_BGH_T8_2024_demo.pdf']
            ];
            
            displayReports();
        }

        async function loadData() {
            try {
                debugLog('Loading data from Google Sheets...');
                
                const recipientsResponse = await gapi.client.sheets.spreadsheets.values.get({
                    spreadsheetId: CONFIG.SPREADSHEET_ID,
                    range: 'NguoiNhan!A:F'
                });
                
                debugLog('Recipients data loaded', 'success');

                let historyResponse;
                try {
                    historyResponse = await gapi.client.sheets.spreadsheets.values.get({
                        spreadsheetId: CONFIG.SPREADSHEET_ID,
                        range: 'LichSuNop!A:D'
                    });
                    debugLog('History data loaded', 'success');
                } catch (error) {
                    debugLog('Creating LichSuNop sheet...', 'info');
                    await createHistorySheet();
                    historyResponse = { result: { values: [] } };
                }

                processData(recipientsResponse.result.values || [], historyResponse.result.values || []);
                
            } catch (error) {
                debugLog('Data loading error: ' + error.message, 'error');
                showNotification('Lỗi tải dữ liệu: ' + error.message, 'error');
            }
        }

        function processData(recipients, history) {
            const userEmail = currentUser.getBasicProfile().getEmail();
            
            reportData = recipients.slice(1).filter(row => row && row.length >= 6 && row[5] === 'Chờ');
            submissionHistory = history.slice(1).filter(row => row && row.length >= 2 && row[0] === userEmail);
            
            debugLog(`Processed: ${reportData.length} reports, ${submissionHistory.length} submissions`);
            displayReports();
        }

        function displayReports() {
            const reportList = document.getElementById('reportList');
            const loadingReports = document.getElementById('loadingReports');
            
            if (loadingReports) loadingReports.style.display = 'none';
            if (reportList) reportList.style.display = 'block';
            
            if (reportData.length === 0) {
                reportList.innerHTML = '<p style="text-align: center; color: #666;">Không có báo cáo nào đang chờ nộp.</p>';
                return;
            }

            const userEmail = currentUser ? currentUser.getBasicProfile().getEmail() : 'demo@example.com';
            
            reportList.innerHTML = reportData.map((report, index) => {
                const [stt, tenNguoiNhan, donVi, loaiBaoCao, kiHieu] = report;
                
                const isSubmitted = submissionHistory.some(history => history[1] === kiHieu);
                const submissionInfo = submissionHistory.find(history => history[1] === kiHieu);
                
                return `
                    <div class="report-item ${isSubmitted ? 'submitted' : ''}">
                        <input type="radio" 
                               name="selectedReport" 
                               value="${index}" 
                               ${isSubmitted ? 'disabled' : ''}
                               onchange="validateForm()">
                        <div class="report-info">
                            <div class="report-title">
                                ${isSubmitted ? '✅' : ''} ${loaiBaoCao}
                            </div>
                            <div class="report-recipient">
                                ${tenNguoiNhan} - ${donVi}
                            </div>
                            ${isSubmitted ? `<div class="report-status">Đã nộp - ${submissionInfo[2]}</div>` : ''}
                        </div>
                    </div>
                `;
            }).join('');
        }

        function setupFileHandlers() {
            const fileInput = document.getElementById('reportFile');
            const fileInputArea = document.getElementById('fileInputArea');
            const schoolName = document.getElementById('schoolName');

            if (fileInput && !fileInput.hasEventListener) {
                fileInput.addEventListener('change', handleFileSelect);
                fileInput.hasEventListener = true;
            }

            if (schoolName && !schoolName.hasEventListener) {
                schoolName.addEventListener('input', validateForm);
                schoolName.hasEventListener = true;
            }
            
            if (fileInputArea && !fileInputArea.hasEventListener) {
                fileInputArea.addEventListener('dragover', function(e) {
                    e.preventDefault();
                    fileInputArea.classList.add('dragover');
                });
                
                fileInputArea.addEventListener('dragleave', function() {
                    fileInputArea.classList.remove('dragover');
                });
                
                fileInputArea.addEventListener('drop', function(e) {
                    e.preventDefault();
                    fileInputArea.classList.remove('dragover');
                    const files = e.dataTransfer.files;
                    if (files.length > 0) {
                        fileInput.files = files;
                        handleFileSelect();
                    }
                });
                
                fileInputArea.hasEventListener = true;
            }
        }

        function handleFileSelect() {
            const fileInput = document.getElementById('reportFile');
            const selectedFileDiv = document.getElementById('selectedFile');
            
            if (fileInput.files.length > 0) {
                const file = fileInput.files[0];
                const allowedTypes = ['.pdf', '.doc', '.docx', '.xls', '.xlsx', '.mp4'];
                const fileExtension = '.' + file.name.split('.').pop().toLowerCase();
                
                if (!allowedTypes.includes(fileExtension)) {
                    showNotification('File không đúng định dạng! Chỉ nhận: PDF, DOC, DOCX, XLS, XLSX, MP4', 'error');
                    fileInput.value = '';
                    selectedFileDiv.innerHTML = '';
                    return;
                }
                
                selectedFileDiv.innerHTML = `
                    <strong>File đã chọn:</strong> ${file.name}<br>
                    <small>Kích thước: ${formatFileSize(file.size)}</small>
                `;
                
                debugLog(`File selected: ${file.name}`);
            } else {
                selectedFileDiv.innerHTML = '';
            }
            validateForm();
        }

        function validateForm() {
            const schoolName = document.getElementById('schoolName')?.value.trim();
            const fileInput = document.getElementById('reportFile');
            const selectedReport = document.querySelector('input[name="selectedReport"]:checked');
            const submitBtn = document.getElementById('submitBtn');
            
            const isValid = schoolName && fileInput?.files.length > 0 && selectedReport;
            if (submitBtn) {
                submitBtn.disabled = !isValid;
            }
        }

        async function submitReport() {
            const schoolName = document.getElementById('schoolName').value.trim();
            const fileInput = document.getElementById('reportFile');
            const selectedReportIndex = document.querySelector('input[name="selectedReport"]:checked')?.value;
            
            if (!selectedReportIndex || !schoolName || !fileInput.files.length) {
                showNotification('Vui lòng điền đầy đủ thông tin!', 'error');
                return;
            }

            const selectedReport = reportData[selectedReportIndex];
            const file = fileInput.files[0];
            const [stt, tenNguoiNhan, donVi, loaiBaoCao, kiHieu] = selectedReport;
            
            try {
                showProgress(true);
                debugLog('Starting submission process...');
                
                // Simulate or real upload
                if (authInstance && gapi.client) {
                    await performRealUpload(schoolName, file, selectedReport);
                } else {
                    await performDemoUpload(schoolName, file, selectedReport);
                }
                
            } catch (error) {
                showProgress(false);
                debugLog('Submission failed: ' + error.message, 'error');
                showNotification('Lỗi nộp báo cáo: ' + error.message, 'error');
            }
        }

        async function performRealUpload(schoolName, file, selectedReport) {
            const [stt, tenNguoiNhan, donVi, loaiBaoCao, kiHieu] = selectedReport;
            const timestamp = new Date().toISOString().replace(/[-:]/g, '').slice(0, 15);
            const fileExtension = '.' + file.name.split('.').pop();
            const newFileName = `${schoolName}_${kiHieu}_${timestamp}${fileExtension}`;
            
            updateProgress(20);
            debugLog(`Finding/creating folder: ${kiHieu}`);
            const targetFolderId = await findOrCreateSubfolder(kiHieu);
            
            updateProgress(50);
            debugLog('Uploading file...');
            await uploadFileToFolder(file, newFileName, targetFolderId);
            
            updateProgress(80);
            debugLog('Recording submission...');
            await recordSubmission(currentUser.getBasicProfile().getEmail(), kiHieu, newFileName);
            
            updateProgress(100);
            showProgress(false);
            
            showNotification(`Nộp báo cáo thành công!\nFile: ${newFileName}\nNơi nhận: ${tenNguoiNhan}`, 'success');
            
            resetForm();
            loadData();
        }

        async function performDemoUpload(schoolName, file, selectedReport) {
            const [stt, tenNguoiNhan, donVi, loaiBaoCao, kiHieu] = selectedReport;
            const timestamp = new Date().toISOString().replace(/[-:]/g, '').slice(0, 15);
            const fileExtension = '.' + file.name.split('.').pop();
            const newFileName = `${schoolName}_${kiHieu}_${timestamp}${fileExtension}`;
            
            updateProgress(25);
            await sleep(500);
            
            updateProgress(50);
            await sleep(500);
            
            updateProgress(75);
            await sleep(500);
            
            updateProgress(100);
            showProgress(false);
            
            // Add to demo history
            submissionHistory.push([
                'demo@example.com',
                kiHieu,
                new Date().toLocaleString('vi-VN'),
                newFileName
            ]);
            
            showNotification(`Demo: Nộp báo cáo thành công!\nFile: ${newFileName}\nNơi nhận: ${tenNguoiNhan}`, 'success');
            
            resetForm();
            displayReports();
        }

        // Google Drive operations (giữ nguyên từ version gốc)
        async function findOrCreateSubfolder(kiHieu) {
            const response = await gapi.client.drive.files.list({
                q: `name='${kiHieu}' and parents in '${CONFIG.PARENT_FOLDER_ID}' and mimeType='application/vnd.google-apps.folder'`,
                fields: 'files(id, name)'
            });
            
            if (response.result.files.length > 0) {
                return response.result.files[0].id;
            } else {
                const createResponse = await gapi.client.drive.files.create({
                    resource: {
                        name: kiHieu,
                        mimeType: 'application/vnd.google-apps.folder',
                        parents: [CONFIG.PARENT_FOLDER_ID]
                    }
                });
                return createResponse.result.id;
            }
        }

        async function uploadFileToFolder(file, fileName, folderId) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.onload = async function(e) {
                    try {
                        const response = await gapi.client.request({
                            path: 'https://www.googleapis.com/upload/drive/v3/files',
                            method: 'POST',
                            params: { uploadType: 'multipart' },
                            headers: { 'Content-Type': 'multipart/related; boundary="foo_bar_baz"' },
                            body: createMultipartBody(fileName, folderId, file.type, e.target.result)
                        });
                        resolve(response.result);
                    } catch (error) {
                        reject(error);
                    }
                };
                reader.readAsArrayBuffer(file);
            });
        }

        function createMultipartBody(fileName, parentId, mimeType, data) {
            const delimiter = 'foo_bar_baz';
            let body = '--' + delimiter + '\r\n';
            body += 'Content-Type: application/json\r\n\r\n';
            body += JSON.stringify({ name: fileName, parents: [parentId] }) + '\r\n';
            body += '--' + delimiter + '\r\n';
            body += 'Content-Type: ' + mimeType + '\r\n\r\n';
            
            const uint8Array = new Uint8Array(data);
            const binaryString = Array.from(uint8Array).map(byte => String.fromCharCode(byte)).join('');
            body += binaryString + '\r\n--' + delimiter + '--';
            
            return body;
        }

        async function recordSubmission(userEmail, kiHieu, fileName) {
            const now = new Date().toLocaleString('vi-VN');
            await gapi.client.sheets.spreadsheets.values.append({
                spreadsheetId: CONFIG.SPREADSHEET_ID,
                range: 'LichSuNop!A:D',
                valueInputOption: 'RAW',
                resource: {
                    values: [[userEmail, kiHieu, now, fileName]]
                }
            });
        }

        async function createHistorySheet() {
            await gapi.client.sheets.spreadsheets.batchUpdate({
                spreadsheetId: CONFIG.SPREADSHEET_ID,
                resource: {
                    requests: [{
                        addSheet: {
                            properties: { title: 'LichSuNop' }
                        }
                    }]
                }
            });

            await gapi.client.sheets.spreadsheets.values.update({
                spreadsheetId: CONFIG.SPREADSHEET_ID,
                range: 'LichSuNop!A1:D1',
                valueInputOption: 'RAW',
                resource: {
                    values: [['Email người nộp', 'Kí hiệu báo cáo', 'Ngày giờ nộp', 'Tên file']]
                }
            });
        }

        // Utility functions
        function showProgress(show) {
            const progressBar = document.getElementById('progressBar');
            if (progressBar) {
                progressBar.style.display = show ? 'block' : 'none';
                if (!show) updateProgress(0);
            }
        }

        function updateProgress(percent) {
            const progressFill = document.getElementById('progressFill');
            if (progressFill) {
                progressFill.style.width = percent + '%';
            }
        }

        function showNotification(message, type) {
            const notification = document.getElementById('notification');
            if (notification) {
                notification.innerHTML = message.replace(/\n/g, '<br>');
                notification.className = `notification ${type}`;
                notification.style.display = 'block';
                
                setTimeout(() => {
                    notification.style.display = 'none';
                }, type === 'success' ? 6000 : 4000);
            }
        }

        function formatFileSize(bytes) {
            if (bytes === 0) return '0 Bytes';
            const k = 1024;
            const sizes = ['Bytes', 'KB', 'MB', 'GB'];
            const i = Math.floor(Math.log(bytes) / Math.log(k));
            return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
        }

        function resetForm() {
            const elements = ['schoolName', 'reportFile', 'selectedFile'];
            elements.forEach(id => {
                const el = document.getElementById(id);
                if (el) {
                    if (el.type === 'file' || el.type === 'text') {
                        el.value = '';
                    } else {
                        el.innerHTML = '';
                    }
                }
            });
            
            document.querySelectorAll('input[name="selectedReport"]').forEach(radio => radio.checked = false);
            validateForm();
        }

        function sleep(ms) {
            return new Promise(resolve => setTimeout(resolve, ms));
        }
    </script>
</body>
</html>
