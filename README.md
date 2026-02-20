<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-time tracker • B3xVFg42 • Lagos → Florida</title>
    <!-- Leaflet CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Roboto, system-ui, sans-serif;
        }
        body {
            background: #0b2a1f; /* deep green hint for Africa */
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 16px;
        }
        .tracker-card {
            max-width: 1100px;
            width: 100%;
            background: white;
            border-radius: 36px 36px 24px 24px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.5), 0 0 0 2px #d4a11e inset;
            overflow: hidden;
            transition: all 0.2s;
        }
        .header {
            background: linear-gradient(145deg, #8b5a2b, #b87c3a); /* earthy tones */
            color: white;
            padding: 24px 28px;
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            justify-content: space-between;
            border-bottom: 3px solid #f5c842;
        }
        .header h1 {
            font-weight: 500;
            font-size: 1.9rem;
            letter-spacing: 1px;
            display: flex;
            align-items: center;
            gap: 12px;
        }
        .header h1 span {
            background: #f5c842;
            color: #5e3a1a;
            font-weight: 700;
            padding: 6px 18px;
            border-radius: 40px;
            font-size: 1.3rem;
            box-shadow: 0 4px 0 #a07d2b;
        }
        .tracking-badge {
            background: #3b6e5e;
            padding: 12px 24px;
            border-radius: 60px;
            font-size: 1.5rem;
            font-weight: 600;
            border: 2px solid #f5c842;
            box-shadow: 0 0 0 2px #1f4a3b;
            font-family: monospace;
            letter-spacing: 2px;
        }
        .status-bar {
            background: #fff6e5;
            padding: 20px 28px;
            display: flex;
            flex-wrap: wrap;
            align-items: baseline;
            gap: 24px;
            border-bottom: 1px solid #e0cba0;
            color: #3b2e1e;
        }
        .status-badge {
            background: #b86b2c;
            color: white;
            font-weight: 600;
            padding: 10px 26px;
            border-radius: 50px;
            font-size: 1.4rem;
            display: inline-flex;
            align-items: center;
            gap: 10px;
            box-shadow: 0 5px 0 #6e3f18;
        }
        .status-badge::before {
            content: "⛴️";  /* shipping / awaiting departure */
            font-size: 1.7rem;
            filter: drop-shadow(2px 2px 0 #3f3f1f);
        }
        .destination {
            background: white;
            padding: 12px 20px;
            border-radius: 60px;
            border-left: 6px solid #f5b342;
            font-size: 1.2rem;
            font-weight: 500;
            box-shadow: 0 2px 6px rgba(0,0,0,0.1);
            word-break: break-word;
            flex: 1;
            min-width: 260px;
        }
        .destination strong {
            color: #8b4c1a;
        }
        .live-indicator {
            margin-left: auto;
            display: flex;
            align-items: center;
            gap: 6px;
            background: #1a4235;
            color: #e0ffcc;
            padding: 8px 16px;
            border-radius: 40px;
            font-size: 1.1rem;
            font-weight: 500;
        }
        .pulse {
            width: 16px;
            height: 16px;
            background: #4cd964;
            border-radius: 50%;
            box-shadow: 0 0 0 0 rgba(76, 217, 100, 0.7);
            animation: pulse-green 1.8s infinite;
        }
        @keyframes pulse-green {
            0% { box-shadow: 0 0 0 0 rgba(76, 217, 100, 0.7); }
            70% { box-shadow: 0 0 0 14px rgba(76, 217, 100, 0); }
            100% { box-shadow: 0 0 0 0 rgba(76, 217, 100, 0); }
        }
        .map-container {
            height: 450px;
            width: 100%;
            background: #c2d9f0;
            position: relative;
        }
        #tracker-map {
            height: 100%;
            width: 100%;
            z-index: 1;
        }
        .map-overlay-footer {
            background: #fcf9f0;
            padding: 18px 28px;
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            justify-content: space-between;
            border-top: 2px solid #e0ba7c;
            font-size: 1.15rem;
        }
        .timestamp {
            display: flex;
            align-items: center;
            gap: 12px;
            color: #3b2e1e;
        }
        .timestamp .time-value {
            font-family: monospace;
            background: #e2eaf3;
            padding: 6px 16px;
            border-radius: 28px;
            font-weight: 600;
            font-size: 1.2rem;
            letter-spacing: 1px;
        }
        .refresh-note {
            color: #1d6540;
            display: flex;
            align-items: center;
            gap: 6px;
        }
        .refresh-note i {
            font-size: 1.4rem;
        }
        .footer-note {
            color: #5e3a1a;
            font-weight: 500;
            border-left: 2px solid #b87c3a;
            padding-left: 18px;
        }
        .footer-note strong {
            color: #024e2e;
            background: #deedff;
            padding: 4px 12px;
            border-radius: 30px;
            font-family: monospace;
            font-size: 1.2rem;
        }
    </style>
</head>
<body>
<div class="tracker-card">
    <div class="header">
        <h1>
            📦 LAGOS → FLORIDA
            <span>READY TO SHIP</span>
        </h1>
        <div class="tracking-badge">
            🔑 B3xVFg42
        </div>
    </div>

    <div class="status-bar">
        <div class="status-badge">IN LAGOS · READY FOR DISPATCH</div>
        <div class="destination">
            <strong>🚚 SHIPPING TO:</strong> 1575 A1A St. Augustine, Florida 32177
        </div>
        <div class="live-indicator">
            <span class="pulse"></span> LIVE · item in Lagos
        </div>
    </div>

    <!-- Map container showing current location in Lagos, Nigeria -->
    <div class="map-container">
        <div id="tracker-map"></div>
    </div>

    <div class="map-overlay-footer">
        <div class="timestamp">
            ⏱️ Last location update:
            <span class="time-value" id="live-timestamp">just now</span>
        </div>
        <div class="refresh-note">
            <i>🔄</i> real-time sync (simulated)
        </div>
        <div class="footer-note">
            📍 current: <strong>Lagos, Nigeria · awaiting departure</strong>
        </div>
    </div>
</div>

<!-- Leaflet JS -->
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
    (function() {
        // ----- Coordinates for current location: LAGOS, NIGERIA (warehouse area) -----
        // Approx coordinates for Lagos (Apapa port / industrial area)
        const lagosLat = 6.4531;    // Apapa / Lagos port area
        const lagosLng = 3.3958;    // close to container terminals

        // Destination (client address) – not shown on map but referenced in popup & UI
        const destinationAddr = "1575 A1A St. Augustine, Florida 32177";

        // Initialize map centered on Lagos
        const map = L.map('tracker-map').setView([lagosLat, lagosLng], 14);

        // Use CartoDB Voyager (free, clear)
        L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>, &copy; CartoDB',
            subdomains: 'abcd',
            maxZoom: 19,
            minZoom: 10
        }).addTo(map);

        // Custom icon for package (highlight current location)
        const packageIcon = L.divIcon({
            html: '📦',
            className: 'package-marker',
            iconSize: [40, 40],
            popupAnchor: [0, -20]
        });

        // Marker at Lagos location
        const marker = L.marker([lagosLat, lagosLng], { 
            icon: packageIcon,
            riseOnHover: true,
            zIndexOffset: 1000
        }).addTo(map);

        // Popup with details: current location, tracking, destination
        marker.bindPopup(`
            <div style="text-align: center; font-weight:500; min-width:240px;">
                <div style="font-size:1.7rem; margin-bottom:6px;">📍 CURRENT POSITION</div>
                <div style="background:#8b5a2b; color:white; padding:6px 12px; border-radius:40px; margin:6px 0;">
                    🔑 B3xVFg42
                </div>
                <div style="border-top:1px solid #aaa; margin:8px 0 4px; padding-top:6px;">
                    <strong>LAGOS WAREHOUSE</strong><br>
                    Apapa Port, Lagos, Nigeria
                </div>
                <div style="color:#b86b2c; font-weight:600; margin:8px 0 4px;">
                    ⏳ READY TO SHIP TO:
                </div>
                <div style="background:#fff0d4; padding:6px; border-radius:16px;">
                    ${destinationAddr}
                </div>
                <div style="font-size:0.9rem; color:#3f5f7f; margin-top:8px;">⏱️ live tracking active</div>
            </div>
        `);

        // Open popup automatically
        marker.openPopup();

        // Add a pulsing circle to emphasize location
        const circle = L.circle([lagosLat, lagosLng], {
            color: '#b87c3a',
            weight: 3,
            fillColor: '#f5c842',
            fillOpacity: 0.3,
            radius: 40
        }).addTo(map);

        // ---- live timestamp update every second ----
        const timeElement = document.getElementById('live-timestamp');
        function updateTimestamp() {
            const now = new Date();
            const hours = now.getHours().toString().padStart(2, '0');
            const minutes = now.getMinutes().toString().padStart(2, '0');
            const seconds = now.getSeconds().toString().padStart(2, '0');
            const timeStr = `${hours}:${minutes}:${seconds} WAT`; // West Africa Time
            timeElement.textContent = timeStr;
        }
        updateTimestamp();
        setInterval(updateTimestamp, 1000);

        // Periodic pulse effect on circle
        setInterval(() => {
            circle.setStyle({ radius: 48, fillOpacity: 0.5 });
            setTimeout(() => {
                circle.setStyle({ radius: 40, fillOpacity: 0.3 });
            }, 300);
        }, 4000);

        // Add a small control to show current status on map
        const liveControl = L.control({ position: 'bottomleft' });
        liveControl.onAdd = function(map) {
            const div = L.DomUtil.create('div', 'info leaflet-control');
            div.innerHTML = '<span style="background: #3b6e5e; color: #f5e3c0; padding: 6px 14px; border-radius: 30px; font-weight: 600; border:2px solid #f5c842;">⚡ B3xVFg42 · LAGOS</span>';
            div.style.padding = '5px';
            return div;
        };
        liveControl.addTo(map);
    })();
</script>

<!-- Styles for custom marker and responsiveness -->
<style>
    .package-marker {
        font-size: 3rem;
        line-height: 1;
        text-align: center;
        filter: drop-shadow(4px 8px 6px #1a3350);
        transform: translateY(-10px);
        cursor: pointer;
        transition: transform 0.1s;
    }
    .package-marker:hover {
        transform: translateY(-12px) scale(1.08);
    }
    .leaflet-popup-content-wrapper {
        border-radius: 24px 24px 24px 24px;
        background: #fefefe;
        border-left: 6px solid #f5b342;
        box-shadow: 0 12px 24px rgba(0, 30, 60, 0.5);
    }
    .leaflet-popup-tip {
        background: #f5b342;
    }
    @media (max-width: 720px) {
        .header h1 { font-size: 1.4rem; }
        .tracking-badge { font-size: 1.1rem; padding: 8px 16px; }
        .status-badge { font-size: 1.1rem; }
        .destination { font-size: 1rem; }
    }
</style>
</body>
</html>
