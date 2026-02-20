<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-time item tracker • B3xVFg42</title>
    <!-- Leaflet CSS (for map) -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Roboto, system-ui, sans-serif;
        }
        body {
            background: #0b1a2f;
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
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.5), 0 0 0 2px #2a4b7c inset;
            overflow: hidden;
            transition: all 0.2s;
        }
        .header {
            background: linear-gradient(145deg, #002856, #0a3b6e);
            color: white;
            padding: 24px 28px;
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            justify-content: space-between;
            border-bottom: 3px solid #7ab7e0;
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
            color: #002856;
            font-weight: 700;
            padding: 6px 18px;
            border-radius: 40px;
            font-size: 1.3rem;
            box-shadow: 0 4px 0 #a07d2b;
        }
        .tracking-badge {
            background: #163a5e;
            padding: 12px 24px;
            border-radius: 60px;
            font-size: 1.5rem;
            font-weight: 600;
            border: 2px solid #8fc9ff;
            box-shadow: 0 0 0 2px #0e2d49;
            font-family: monospace;
            letter-spacing: 2px;
        }
        .status-bar {
            background: #e4f0fa;
            padding: 20px 28px;
            display: flex;
            flex-wrap: wrap;
            align-items: baseline;
            gap: 24px;
            border-bottom: 1px solid #b9d6f0;
            color: #003366;
        }
        .status-badge {
            background: #2e7d32;
            color: white;
            font-weight: 600;
            padding: 10px 26px;
            border-radius: 50px;
            font-size: 1.4rem;
            display: inline-flex;
            align-items: center;
            gap: 10px;
            box-shadow: 0 5px 0 #1b4f1b;
        }
        .status-badge::before {
            content: "⛟";
            font-size: 1.7rem;
            filter: drop-shadow(2px 2px 0 #1f3f1f);
        }
        .address {
            background: white;
            padding: 12px 20px;
            border-radius: 60px;
            border-left: 6px solid #f5b342;
            font-size: 1.3rem;
            font-weight: 500;
            box-shadow: 0 2px 6px rgba(0,0,0,0.1);
            word-break: break-word;
            flex: 1;
            min-width: 260px;
        }
        .address strong {
            color: #003b77;
        }
        .live-indicator {
            margin-left: auto;
            display: flex;
            align-items: center;
            gap: 6px;
            background: #1a2c42;
            color: #b7e0ff;
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
            border-top: 2px solid #b9d3f0;
            font-size: 1.15rem;
        }
        .timestamp {
            display: flex;
            align-items: center;
            gap: 12px;
            color: #00355e;
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
            color: #0e4a6b;
            display: flex;
            align-items: center;
            gap: 6px;
        }
        .refresh-note i {
            font-size: 1.4rem;
        }
        .footer-note {
            color: #1d4560;
            font-weight: 500;
            border-left: 2px solid #789fcb;
            padding-left: 18px;
        }
        .footer-note strong {
            color: #022d52;
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
            📦 TRACK & TRACE
            <span>READY TO SHIP</span>
        </h1>
        <div class="tracking-badge">
            🔑 B3xVFg42
        </div>
    </div>

    <div class="status-bar">
        <div class="status-badge">READY FOR DISPATCH</div>
        <div class="address">
            <strong>📍 Current location:</strong> 1575 A1A St. Augustine, Florida 32177
        </div>
        <div class="live-indicator">
            <span class="pulse"></span> LIVE · updating
        </div>
    </div>

    <!-- Map container -->
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
            🚚 item status: <strong>at warehouse · ready to ship</strong>
        </div>
    </div>
</div>

<!-- Leaflet JS -->
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
    (function() {
        // ----- Coordinates for 1575 A1A, St. Augustine, FL 32177 -----
        // Precise geocoding: (lat, lng) approx based on A1A / St. Augustine beach area.
        // This location represents the shipping warehouse / current item position.
        const targetLat = 29.9013;    // ~ near A1A and Pope Rd
        const targetLng = -81.3120;   // adjusted for St. Augustine mainland / coastal region
        
        // Initialize map, set zoom for street precision
        const map = L.map('tracker-map').setView([targetLat, targetLng], 16);
        
        // Use CartoDB Voyager (no API key, free & clean) — or OpenStreetMap standard
        L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>, &copy; CartoDB',
            subdomains: 'abcd',
            maxZoom: 19,
            minZoom: 12
        }).addTo(map);
        
        // Custom icon for package (to make it pop)
        const packageIcon = L.divIcon({
            html: '📦',
            className: 'package-marker',
            iconSize: [40, 40],
            popupAnchor: [0, -20]
        });
        
        // Marker with the package location (real-time position)
        const marker = L.marker([targetLat, targetLng], { 
            icon: packageIcon,
            riseOnHover: true,
            zIndexOffset: 1000
        }).addTo(map);
        
        // Bind a detailed popup (shows tracking and address)
        marker.bindPopup(`
            <div style="text-align: center; font-weight:500; min-width:200px;">
                <div style="font-size:1.7rem; margin-bottom:6px;">📍 YOUR ITEM</div>
                <div style="background:#002856; color:white; padding:6px 12px; border-radius:40px; margin:6px 0;">
                    🔑 B3xVFg42
                </div>
                <div style="border-top:1px solid #aaa; margin:8px 0 4px; padding-top:6px;">
                    1575 A1A<br>St. Augustine, FL 32177
                </div>
                <div style="color:#2e7d32; font-weight:600; margin-top:8px;">✅ READY TO SHIP OUT</div>
                <div style="font-size:0.9rem; color:#3f5f7f;">⏱️ real-time position active</div>
            </div>
        `);
        
        // Open popup automatically (to show details)
        marker.openPopup();
        
        // Add a pulsing circle to emphasize location (semi-transparent)
        const circle = L.circle([targetLat, targetLng], {
            color: '#ffab00',
            weight: 3,
            fillColor: '#ffd966',
            fillOpacity: 0.3,
            radius: 25
        }).addTo(map);
        
        // ---- simulate "real‑time" timestamp updates (freshness indicator) ----
        const timeElement = document.getElementById('live-timestamp');
        
        function updateTimestamp() {
            const now = new Date();
            const hours = now.getHours().toString().padStart(2, '0');
            const minutes = now.getMinutes().toString().padStart(2, '0');
            const seconds = now.getSeconds().toString().padStart(2, '0');
            const timeStr = `${hours}:${minutes}:${seconds}`;
            
            // Optionally add seconds to feel more real-time
            timeElement.textContent = `${timeStr} ET`;
            
            // Also subtly change the marker popup content? Not needed, but could refresh.
            // We can update the popup content's timestamp if we want — but it's fine.
        }
        
        // initial call
        updateTimestamp();
        
        // Update every second to show "live" feel (though location fixed, the timestamp shows constant updates)
        setInterval(updateTimestamp, 1000);
        
        // Optional: slight map rotate or marker bounce? Not necessary. But we can add a small 
        // "refreshing location" effect by toggling circle radius? That might be overkill.
        // To reinforce the idea that it's being tracked live, we can add a periodic "pulse" on the marker.
        let pulseTimer = 0;
        setInterval(() => {
            // briefly increase circle radius to simulate a 'ping'
            circle.setStyle({ radius: 32, fillOpacity: 0.5 });
            setTimeout(() => {
                circle.setStyle({ radius: 25, fillOpacity: 0.3 });
            }, 300);
        }, 4000);
        
        // Also re-center the map if user scrolls away? Not necessary, user can pan.
        // But we can optionally have a button to reset view – not needed for this demo.
        
        // Add a small "live" note as an attribution on map corner (optional)
        const liveControl = L.control({ position: 'bottomleft' });
        liveControl.onAdd = function(map) {
            const div = L.DomUtil.create('div', 'info leaflet-control');
            div.innerHTML = '<span style="background: #002856; color: #ffd966; padding: 6px 12px; border-radius: 24px; font-weight: 600; border:2px solid #ffb347;">⚡ LIVE TRACKING: B3xVFg42</span>';
            div.style.padding = '5px';
            return div;
        };
        liveControl.addTo(map);
        
        // For better UX, add a small line showing that the item is at this exact facility
        const addressLine = "1575 A1A, St. Augustine, FL 32177";
        // done.
    })();
</script>

<!-- Add a little style for the custom marker (since divIcon might need some baseline) -->
<style>
    .package-marker {
        font-size: 2.8rem;
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
    /* Responsive adjustments */
    @media (max-width: 720px) {
        .header h1 { font-size: 1.4rem; }
        .tracking-badge { font-size: 1.1rem; padding: 8px 16px; }
        .status-badge { font-size: 1.1rem; }
        .address { font-size: 1rem; }
    }
</style>

</body>
</html>
