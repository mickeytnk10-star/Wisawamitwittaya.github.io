<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบลงทะเบียนหนังสือส่ง - Wisawamitwittaya</title>
    <link rel="icon" type="image/x-icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><text y='.9em' font-size='90'>📚</text></svg>">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.min.js"></script>
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-auth.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 50%, #1e40af 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.3);
            padding: 40px;
            position: relative;
            overflow: hidden;
        }

        .container::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 8px;
            background: linear-gradient(90deg, #fbbf24, #f59e0b, #d97706);
        }

        .header-section {
            text-align: center;
            margin-bottom: 40px;
            padding-bottom: 30px;
            border-bottom: 2px solid #e5e7eb;
        }

        .school-logo {
            font-size: 4em;
            margin-bottom: 15px;
            display: block;
        }

        .school-name {
            font-size: 2.2em;
            font-weight: 800;
            color: #1e3a8a;
            margin-bottom: 10px;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }

        .system-title {
            font-size: 1.4em;
            color: #6b7280;
            font-weight: 600;
            margin-bottom: 5px;
        }

        .subtitle {
            font-size: 1em;
            color: #9ca3af;
            font-style: italic;
        }

        .form-section {
            background: #f8f9fa;
            padding: 30px;
            border-radius: 12px;
            margin-bottom: 30px;
            border-left: 6px solid #1e3a8a;
        }

        .form-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }

        .form-group {
            display: flex;
            flex-direction: column;
        }

        label {
            font-weight: 600;
            color: #333;
            margin-bottom: 8px;
            font-size: 0.95em;
        }

        input[type="text"],
        input[type="date"],
        textarea {
            padding: 12px;
            border: 2px solid #e0e0e0;
            border-radius: 5px;
            font-size: 0.95em;
            font-family: inherit;
            transition: all 0.3s ease;
        }

        input[type="text"]:focus,
        input[type="date"]:focus,
        textarea:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 10px rgba(102, 126, 234, 0.1);
        }

        textarea {
            resize: vertical;
            min-height: 60px;
            grid-column: span 2;
        }

        .button-group {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        button {
            padding: 12px 24px;
            border: none;
            border-radius: 5px;
            font-size: 0.95em;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .btn-submit {
            background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
            color: white;
        }

        .btn-submit:hover {
            background: linear-gradient(135deg, #1e40af 0%, #2563eb 100%);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(30, 58, 138, 0.4);
        }

        .btn-export-pdf {
            background: linear-gradient(135deg, #dc2626 0%, #ef4444 100%);
            color: white;
        }

        .btn-export-pdf:hover {
            background: linear-gradient(135deg, #b91c1c 0%, #dc2626 100%);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(220, 38, 38, 0.4);
        }

        .btn-export-excel {
            background: linear-gradient(135deg, #16a34a 0%, #22c55e 100%);
            color: white;
        }

        .btn-export-excel:hover {
            background: linear-gradient(135deg, #15803d 0%, #16a34a 100%);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(22, 163, 74, 0.4);
        }

        .btn-print {
            background: linear-gradient(135deg, #0891b2 0%, #06b6d4 100%);
            color: white;
        }

        .btn-print:hover {
            background: linear-gradient(135deg, #0e7490 0%, #0891b2 100%);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(8, 145, 178, 0.4);
        }

        .btn-delete {
            background: #ef5350;
            color: white;
            padding: 6px 12px;
            font-size: 0.85em;
        }

        .btn-delete:hover {
            background: #e53935;
        }

        .table-section {
            margin-top: 30px;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
            background: white;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
            border-radius: 8px;
            overflow: hidden;
        }

        thead {
            background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
            color: white;
        }

        th {
            padding: 18px;
            text-align: left;
            font-weight: 700;
            font-size: 1.05em;
        }

        td {
            padding: 12px 15px;
            border-bottom: 1px solid #e0e0e0;
        }

        tbody tr:nth-child(even) {
            background: #f5f5f5;
        }

        tbody tr:hover {
            background: #efefff;
            transition: background 0.2s ease;
        }

        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: #999;
            font-size: 1.1em;
        }

        .success-message {
            background: #c8e6c9;
            color: #2e7d32;
            padding: 15px;
            border-radius: 5px;
            margin-bottom: 15px;
            display: none;
            animation: slideIn 0.3s ease;
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateY(-10px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .stats {
            display: flex;
            gap: 20px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .stat-box {
            background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
            color: white;
            padding: 25px;
            border-radius: 12px;
            text-align: center;
            flex: 1;
            min-width: 180px;
            box-shadow: 0 5px 20px rgba(30, 58, 138, 0.3);
        }

        .stat-number {
            font-size: 2em;
            font-weight: 700;
        }

        .stat-label {
            font-size: 0.9em;
            opacity: 0.9;
            margin-top: 5px;
        }

        .auth-section {
            background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
            color: white;
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 20px;
        }

        .auth-info p {
            margin: 5px 0;
            font-size: 0.95em;
        }

        .status-badge {
            padding: 8px 15px;
            border-radius: 20px;
            font-weight: 600;
            font-size: 0.9em;
        }

        .status-online {
            background: #4caf50;
            color: white;
        }

        .status-offline {
            background: #ff9800;
            color: white;
        }

        .user-indicator {
            position: fixed;
            top: 10px;
            right: 10px;
            background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
            color: white;
            padding: 10px 15px;
            border-radius: 5px;
            font-size: 0.85em;
            z-index: 1000;
            box-shadow: 0 3px 10px rgba(30, 58, 138, 0.3);
        }

        .privacy-notice {
            background: #c8e6c9;
            color: #2e7d32;
            padding: 15px;
            border-radius: 5px;
            margin-bottom: 20px;
            font-size: 0.9em;
            border-left: 4px solid #4caf50;
        }

        .share-section {
            background: #eff6ff;
            color: #1e3a8a;
            padding: 18px;
            border-radius: 10px;
            margin-bottom: 24px;
            border-left: 6px solid #3b82f6;
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            gap: 10px;
        }

        .share-text {
            flex: 1 1 320px;
            font-size: 0.95em;
        }

        .share-button {
            background: #3b82f6;
            color: white;
            border-radius: 8px;
            padding: 12px 20px;
            border: none;
            font-weight: 700;
            cursor: pointer;
            transition: background 0.3s ease, transform 0.3s ease;
        }

        .share-button:hover {
            background: #1d4ed8;
            transform: translateY(-1px);
        }

        @media print {
            body {
                background: white;
            }
            .form-section,
            .button-group,
            button {
                display: none;
            }
            table {
                box-shadow: none;
                border: 1px solid #ddd;
            }
        }

        @media (max-width: 768px) {
            .container {
                padding: 15px;
            }

            h1 {
                font-size: 1.5em;
            }

            .form-grid {
                grid-template-columns: 1fr;
            }

            textarea {
                grid-column: span 1;
            }

            table {
                font-size: 0.85em;
            }

            th, td {
                padding: 8px 10px;
            }
        }
    </style>
</head>
<body>
    <div id="mainContent" class="main-content visible">
        <div class="user-indicator" id="userIndicator">✓ ระบบพร้อม</div>

        <div class="container">
            <div class="header-section">
                <div class="school-logo">🎓</div>
                <div class="school-name">Wisawamitwittaya</div>
                <div class="system-title">ระบบลงทะเบียนหนังสือส่ง</div>
                <div class="subtitle">ระบบจัดการเอกสารออนไลน์สำหรับนักเรียนและบุคลากร</div>
            </div>

            <div class="auth-section">
                <div class="auth-info">
                    <p><strong>🌐 สถานะ:</strong> <span id="connectionStatus" class="status-badge status-offline">ออฟไลน์</span></p>
                    <p style="font-size: 0.85em; margin-top: 8px;">
                        <strong>💾 ข้อมูลจะซิงค์ถูกต้องอัตโนมัติเมื่อเชื่อมต่ออินเทอร์เน็ต</strong>
                    </p>
                </div>
            </div>

            <div class="privacy-notice">
                ✅ <strong>ระบบพร้อมใช้:</strong> ข้อมูลการลงทะเบียนทั้งหมดจะถูกบันทึกและซิงค์อัตโนมัติให้กับผู้ใช้ทั้งหมด
                <br>
                ⚠️ <strong>โปรดแทนที่</strong> Firebase config กับค่าจาก Firebase Console เพื่อให้คนอื่นใช้งานร่วมกันได้
            </div>

            <div class="share-section">
                <div class="share-text">
                    📤 แชร์ลิงก์นี้ให้คนอื่นใช้งานร่วมกันได้ทันที เมื่อระบบถูกโฮสต์บนเว็บหรือแชร์ผ่านเซิร์ฟเวอร์
                </div>
                <button class="share-button" onclick="copyShareLink()">คัดลอกลิงก์แชร์</button>
            </div>

            <div class="success-message" id="successMessage">
                ✓ บันทึกข้อมูลสำเร็จ
            </div>

            <div class="form-section">
                <form id="registrationForm">
                    <div class="form-grid">
                        <div class="form-group">
                            <label for="docNumber">เลขทะเบียนส่ง *</label>
                            <input type="text" id="docNumber" placeholder="เช่น ส.1/2568" required>
                        </div>
                        <div class="form-group">
                            <label for="location">ที่ *</label>
                            <input type="text" id="location" placeholder="เช่น สำนักอธิการบดี" required>
                        </div>
                        <div class="form-group">
                            <label for="date">วันที่ลง *</label>
                            <input type="date" id="date" required>
                        </div>
                        <div class="form-group">
                            <label for="fromPerson">จาก *</label>
                            <input type="text" id="fromPerson" placeholder="เช่น นาย ก. ล้อม" required>
                        </div>
                        <div class="form-group">
                            <label for="toPerson">ถึง *</label>
                            <input type="text" id="toPerson" placeholder="เช่น นาย ส. วิทย์" required>
                        </div>
                        <div class="form-group">
                            <label for="subject">เรื่อง *</label>
                            <input type="text" id="subject" placeholder="เรื่องหนังสือที่ส่ง" required>
                        </div>
                        <div class="form-group">
                            <label for="action">การปฏิบัติ</label>
                            <input type="text" id="action" placeholder="เช่น อนุมัติแล้ว">
                        </div>
                        <div class="form-group">
                            <label for="remarks">หมายเหตุ</label>
                            <textarea id="remarks" placeholder="หมายเหตุเพิ่มเติม (ถ้ามี)"></textarea>
                        </div>
                    </div>

                    <div class="button-group">
                        <button type="submit" class="btn-submit">✓ บันทึกข้อมูล</button>
                        <button type="reset" class="btn-submit" style="background: #999;">🔄 ล้างแบบฟอร์ม</button>
                    </div>
                </form>
            </div>

            <div class="stats">
                <div class="stat-box">
                    <div class="stat-number" id="totalRecords">0</div>
                    <div class="stat-label">จำนวนการลงทะเบียน</div>
                </div>
            </div>

            <div class="button-group">
                <button class="btn-export-pdf" onclick="exportToPDF()">📄 ส่งออก PDF</button>
                <button class="btn-export-excel" onclick="exportToExcel()">📊 ส่งออก Excel</button>
                <button class="btn-print" onclick="window.print()">🖨️ พิมพ์</button>
            </div>

            <div class="table-section">
                <h2 style="margin-bottom: 15px; color: #333;">รายการลงทะเบียน</h2>
                <table id="dataTable">
                    <thead>
                        <tr>
                            <th>เลขทะเบียนส่ง</th>
                            <th>ที่</th>
                            <th>วันที่ลง</th>
                            <th>จาก</th>
                            <th>ถึง</th>
                            <th>เรื่อง</th>
                            <th>การปฏิบัติ</th>
                            <th>หมายเหตุ</th>
                            <th>การจัดการ</th>
                        </tr>
                    </thead>
                    <tbody id="tableBody">
                    </tbody>
                </table>
                <div id="emptyState" class="empty-state">ยังไม่มีข้อมูลการลงทะเบียน กรุณากรอกแบบฟอร์มและบันทึกข้อมูล</div>
            </div>
        </div>
    </div>

    <script>
        // =============== Firebase Configuration ===============
        // โปรดแทนที่ค่าต่อไปนี้ด้วย config ของ Firebase ที่ได้จาก Firebase Console
        // ตัวอย่าง: https://console.firebase.google.com/
        // หากยังไม่ใส่ จะใช้งานเป็นแบบ LocalStorage เท่านั้น
        const firebaseConfig = {
            apiKey: "AIzaSyBE7x_9z_qwR-D5vV5zZZZ9qZ9Z9c9z9zZ9z9",
            authDomain: "registration-system-xxxx.firebaseapp.com",
            projectId: "registration-system-xxxx",
            storageBucket: "registration-system-xxxx.appspot.com",
            messagingSenderId: "123456789012",
            appId: "1:123456789012:web:abcdef1234567890abcdef"
        };

        let db, auth;
        let isOnline = navigator.onLine;
        let localDataCache = [];
        const SHARED_DB_PATH = 'shared_registrations';

        // Initialize Firebase
        function initializeFirebase() {
            try {
                firebase.initializeApp(firebaseConfig);
                db = firebase.database();
                auth = firebase.auth();

                // Sign in anonymously
                auth.signInAnonymously().catch(error => {
                    console.log('Anonymous auth error:', error);
                    setupFallback();
                });

                setupNetworkListener();
                setupDataListener();
            } catch (error) {
                console.log('Firebase error - Running in fallback mode');
                document.querySelector('.privacy-notice').innerHTML =
                    '⚠️ <strong>โหมด LocalStorage:</strong> ใช้ LocalStorage เพื่อบันทึกข้อมูลแทน';
                setupFallback();
            }
        }

        function setupDataListener() {
            if (!db) {
                setupFallback();
                return;
            }

            const dataRef = db.ref(SHARED_DB_PATH);
            dataRef.on('value', snapshot => {
                const data = [];
                snapshot.forEach(childSnapshot => {
                    data.push({id: childSnapshot.key, ...childSnapshot.val()});
                });
                localDataCache = data;
                displayTable(data);
                updateStats(data);
            }, error => {
                console.log('Firebase listener error:', error);
                setupFallback();
            });
        }

        // =============== Network Status ===============
        function setupNetworkListener() {
            window.addEventListener('online', () => {
                isOnline = true;
                updateConnectionStatus();
                syncLocalDataToFirebase();
            });

            window.addEventListener('offline', () => {
                isOnline = false;
                updateConnectionStatus();
            });

            updateConnectionStatus();
            if (isOnline) {
                syncLocalDataToFirebase();
            }
        }

        function updateConnectionStatus() {
            const statusEl = document.getElementById('connectionStatus');
            if (isOnline) {
                statusEl.textContent = '🟢 ออนไลน์';
                statusEl.className = 'status-badge status-online';
            } else {
                statusEl.textContent = '🔴 ออฟไลน์';
                statusEl.className = 'status-badge status-offline';
            }
        }

        function copyShareLink() {
            const shareUrl = window.location.href;
            navigator.clipboard.writeText(shareUrl).then(() => {
                showSuccessMessage('คัดลอกลิงก์เรียบร้อยแล้ว');
            }).catch(() => {
                alert('ไม่สามารถคัดลอกลิงก์ได้ กรุณาคัดลอกด้วยตนเอง: ' + shareUrl);
            });
        }

        // =============== Fallback Mode ===============
        function setupFallback() {
            loadDataFromLocalStorage();
        }

        function loadDataFromLocalStorage() {
            const data = JSON.parse(localStorage.getItem('registrationData')) || [];
            localDataCache = data;
            displayTable(data);
            updateStats(data);
        }

        // =============== Data Operations ===============
        function saveData(record) {
            if (db && isOnline) {
                const dataRef = db.ref(SHARED_DB_PATH);
                dataRef.push({
                    ...record,
                    createdAt: new Date().toISOString(),
                    updatedAt: new Date().toISOString()
                }).catch(error => {
                    console.log('Firebase save error:', error);
                    saveDataLocally(record);
                });
            } else {
                saveDataLocally(record);
            }
        }

        function saveDataLocally(record) {
            const data = JSON.parse(localStorage.getItem('registrationData')) || [];
            data.push({
                id: 'local_' + Date.now(),
                ...record,
                createdAt: new Date().toISOString(),
                updatedAt: new Date().toISOString()
            });
            localStorage.setItem('registrationData', JSON.stringify(data));
        }

        function syncLocalDataToFirebase() {
            if (!db || !isOnline) return;

            const localData = JSON.parse(localStorage.getItem('registrationData')) || [];
            if (localData.length === 0) return;

            const dataRef = db.ref(SHARED_DB_PATH);
            const syncPromises = localData.map(record => {
                const newRef = dataRef.push();
                return newRef.set(record).catch(error => {
                    console.log('Sync local record failed:', error);
                });
            });

            Promise.all(syncPromises).then(() => {
                localStorage.removeItem('registrationData');
                loadDataFromLocalStorage();
                console.log('Local data synced to Firebase.');
            }).catch(error => {
                console.log('Error syncing local data:', error);
            });
        }

        function deleteRecord(recordId) {
            if (confirm('ยืนยันการลบข้อมูลนี้?')) {
                if (db && !recordId.startsWith('local_')) {
                    db.ref(SHARED_DB_PATH + '/' + recordId).remove();
                } else {
                    const data = JSON.parse(localStorage.getItem('registrationData')) || [];
                    const index = data.findIndex(item => item.id === recordId);
                    if (index > -1) {
                        data.splice(index, 1);
                        localStorage.setItem('registrationData', JSON.stringify(data));
                        loadDataFromLocalStorage();
                    }
                }
                showSuccessMessage('ลบข้อมูลสำเร็จ');
            }
        }

        // =============== UI Operations ===============
        function displayTable(data) {
            const tableBody = document.getElementById('tableBody');
            const emptyState = document.getElementById('emptyState');

            if (data.length === 0) {
                tableBody.innerHTML = '';
                emptyState.style.display = 'block';
                return;
            }

            emptyState.style.display = 'none';
            tableBody.innerHTML = data.map((item) => `
                <tr>
                    <td>${escapeHtml(item.docNumber)}</td>
                    <td>${escapeHtml(item.location)}</td>
                    <td>${formatDate(item.date)}</td>
                    <td>${escapeHtml(item.fromPerson)}</td>
                    <td>${escapeHtml(item.toPerson)}</td>
                    <td>${escapeHtml(item.subject)}</td>
                    <td>${escapeHtml(item.action)}</td>
                    <td>${escapeHtml(item.remarks)}</td>
                    <td>
                        <button class="btn-delete" onclick="deleteRecord('${item.id}')">ลบ</button>
                    </td>
                </tr>
            `).join('');
        }

        function updateStats(data) {
            document.getElementById('totalRecords').textContent = data.length;
        }

        function showSuccessMessage(message) {
            const msg = document.getElementById('successMessage');
            msg.textContent = '✓ ' + message;
            msg.style.display = 'block';
            setTimeout(() => {
                msg.style.display = 'none';
            }, 3000);
        }

        // =============== Export Functions ===============
        function exportToPDF() {
            const element = document.querySelector('.table-section');
            const opt = {
                margin: 10,
                filename: 'registration_' + new Date().toISOString().slice(0, 10) + '.pdf',
                image: { type: 'jpeg', quality: 0.98 },
                html2canvas: { scale: 2 },
                jsPDF: { orientation: 'landscape', unit: 'mm', format: 'a4' }
            };
            html2pdf().set(opt).from(element).save();
        }

        function exportToExcel() {
            const data = localDataCache;
            if (data.length === 0) {
                alert('ไม่มีข้อมูลที่จะส่งออก');
                return;
            }

            const wsData = [
                ['เลขทะเบียนส่ง', 'ที่', 'วันที่ลง', 'จาก', 'ถึง', 'เรื่อง', 'การปฏิบัติ', 'หมายเหตุ'],
                ...data.map(item => [
                    item.docNumber,
                    item.location,
                    item.date,
                    item.fromPerson,
                    item.toPerson,
                    item.subject,
                    item.action,
                    item.remarks
                ])
            ];

            const ws = XLSX.utils.aoa_to_sheet(wsData);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, 'Registration');
            XLSX.writeFile(wb, 'registration_' + new Date().toISOString().slice(0, 10) + '.xlsx');
        }

        // =============== Utility Functions ===============
        function escapeHtml(text) {
            const div = document.createElement('div');
            div.textContent = text || '';
            return div.innerHTML;
        }

        function formatDate(dateString) {
            const date = new Date(dateString + 'T00:00:00');
            return date.toLocaleDateString('th-TH', {
                year: 'numeric',
                month: 'long',
                day: 'numeric'
            });
        }

        // =============== Form Submission ===============
        document.getElementById('registrationForm').addEventListener('submit', function (e) {
            e.preventDefault();

            const newRecord = {
                docNumber: document.getElementById('docNumber').value,
                location: document.getElementById('location').value,
                date: document.getElementById('date').value,
                fromPerson: document.getElementById('fromPerson').value,
                toPerson: document.getElementById('toPerson').value,
                subject: document.getElementById('subject').value,
                action: document.getElementById('action').value,
                remarks: document.getElementById('remarks').value
            };

            saveData(newRecord);
            showSuccessMessage('บันทึกข้อมูลสำเร็จ');
            this.reset();
            document.getElementById('date').valueAsDate = new Date();
        });

        // =============== Initialize ===============
        document.getElementById('date').valueAsDate = new Date();
        initializeFirebase();
    </script>
</body>
</html>
