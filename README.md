<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Live Item Tracking Demo</title>
    <!-- Leaflet CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            box-sizing: border-box;
            font-family: Arial, sans-serif;
        }
        body {
            margin: 0;
            padding: 20px;
            background: #f4f4f4;
            display: flex;
            justify-content: center;
        }
        .container {
            max-width: 800px;
            width: 100%;
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            padding: 20px;
        }
        h1 {
            text-align: center;
            color: #333;
        }
        .tracking-input {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
        }
        #trackingCode {
            flex: 1;
            padding: 10px;
            font-size: 16px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }
        button {
            padding: 10px 20px;
            background: #007bff;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background: #0056b3;
        }
        #map {
            height: 400px;
            border-radius: 8px;
            border: 1px solid #ddd;
        }
        .info {
            margin-top: 15px;
            padding: 10px;
            background: #e9ecef;
            border-radius: 4px;
            text-align: center;
            color: #555;
        }
        .footer {
            margin-top: 20px;
            font-size: 12px;
            text-align: center;
            color: #888;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>📦 Track Your Item</h1>
        <div class="tracking-input">
            <input type="text" id="trackingCode" placeholder="Enter tracking code (e.g., ABC123)" value="">
            <button id="trackBtn">Track</button>
        </div>
        <div id="map"></div>
        <div class="info" id="status">Enter a tracking code to begin</div>
        <div class="footer">
            Demo: Marker moves along a fixed route to simulate live location.
            <br>Real integration would replace simulation with actual GPS data.
        </div>
    </div>

    <!-- Leaflet JavaScript -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        (function() {
            // ---------- Configuration ----------
            // Predefined route (polyline) for simulation: from San Francisco to Los Angeles
            const routePoints = [
                [37.7749, -122.4194], // SF
                [37.2545, -121.8764], // near San Jose
                [36.7783, -119.4179], // Fresno area
                [35.3733, -119.0187], // Bakersfield
                [34.0522, -118.2437]  // LA
            ];

            // How often to move the marker (milliseconds)
            const UPDATE_INTERVAL = 3000; // 3 seconds

            // ---------- Global variables ----------
            let map;
            let marker;
            let polyline;
            let currentIndex = 0;          // index of current route point
            let intervalId = null;
            let trackingCode = '';

            // ---------- Helper: get URL parameter ----------
            function getTrackingCodeFromUrl() {
                const urlParams = new URLSearchParams(window.location.search);
                return urlParams.get('code') || '';
            }

            // ---------- Helper: update URL without reload ----------
            function setUrlParameter(code) {
                const url = new URL(window.location);
                if (code) {
                    url.searchParams.set('code', code);
                } else {
                    url.searchParams.delete('code');
                }
                window.history.pushState({}, '', url);
            }

            // ---------- Initialize map (once) ----------
            function initMap() {
                if (map) return; // already initialized
                map = L.map('map').setView([37.7749, -122.4194], 6); // center between SF and LA
                L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
                }).addTo(map);

                // Draw the full route as a polyline
                polyline = L.polyline(routePoints, { color: 'blue', weight: 3 }).addTo(map);
            }

            // ---------- Start simulation for a given tracking code ----------
            function startTracking(code) {
                if (!code) {
                    stopTracking();
                    document.getElementById('status').innerText = 'Enter a tracking code to begin';
                    return;
                }

                // If tracking same code and already running, do nothing
                if (trackingCode === code && intervalId) {
                    document.getElementById('status').innerText = `Tracking ${code} (already active)`;
                    return;
                }

                // Stop any previous simulation
                stopTracking();

                trackingCode = code;
                document.getElementById('status').innerHTML = `🔍 Tracking code: <strong>${code}</strong> — moving marker every ${UPDATE_INTERVAL/1000} seconds`;

                // Reset to start of route
                currentIndex = 0;

                // Remove old marker if exists
                if (marker) map.removeLayer(marker);

                // Create new marker at first point
                marker = L.marker(routePoints[0]).addTo(map);
                map.setView(routePoints[0], 8); // zoom in a bit

                // Start moving the marker along the route
                intervalId = setInterval(() => {
                    // Move to next point, loop back to start after finishing
                    currentIndex = (currentIndex + 1) % routePoints.length;
                    const newPos = routePoints[currentIndex];
                    marker.setLatLng(newPos);
                    map.panTo(newPos); // smooth pan

                    // Update status with current location (simulated)
                    const locationName = getLocationName(currentIndex);
                    document.getElementById('status').innerHTML = `🔍 Tracking code: <strong>${code}</strong> — Current location: ${locationName}`;
                }, UPDATE_INTERVAL);
            }

            // Simple helper to give names to route points (for demo)
            function getLocationName(index) {
                const names = ['San Francisco', 'Near San Jose', 'Fresno Area', 'Bakersfield', 'Los Angeles'];
                return names[index] || 'Unknown';
            }

            // ---------- Stop simulation ----------
            function stopTracking() {
                if (intervalId) {
                    clearInterval(intervalId);
                    intervalId = null;
                }
                trackingCode = '';
            }

            // ---------- Load from URL on page load ----------
            function loadFromUrl() {
                const code = getTrackingCodeFromUrl();
                if (code) {
                    document.getElementById('trackingCode').value = code;
                    startTracking(code);
                } else {
                    document.getElementById('status').innerText = 'Enter a tracking code to begin';
                }
            }

            // ---------- Handle track button click ----------
            function handleTrackClick() {
                const input = document.getElementById('trackingCode');
                const code = input.value.trim();

                if (code === '') {
                    alert('Please enter a tracking code');
                    return;
                }

                // Update URL
                setUrlParameter(code);

                // Start tracking
                startTracking(code);
            }

            // ---------- Initialize everything ----------
            window.addEventListener('load', () => {
                initMap();
                loadFromUrl();

                document.getElementById('trackBtn').addEventListener('click', handleTrackClick);

                // Also allow Enter key in input field
                document.getElementById('trackingCode').addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') {
                        handleTrackClick();
                    }
                });
            });

            // Handle browser back/forward (popstate)
            window.addEventListener('popstate', () => {
                const code = getTrackingCodeFromUrl();
                document.getElementById('trackingCode').value = code;
                if (code) {
                    startTracking(code);
                } else {
                    stopTracking();
                    document.getElementById('status').innerText = 'Enter a tracking code to begin';
                    if (marker) map.removeLayer(marker);
                    marker = null;
                    map.setView([37.7749, -122.4194], 6); // reset view
                }
            });

        })();
    </script>
</body>
</html>
