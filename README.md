<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Delivery Tracker</title>
    <!-- Leaflet CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            box-sizing: border-box;
            font-family: Arial, sans-serif;
        }
        body {
            background: #f4f4f4;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .container {
            max-width: 800px;
            width: 100%;
            background: white;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            padding: 25px;
        }
        h1 {
            margin-top: 0;
            color: #333;
            text-align: center;
        }
        .track-box {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }
        #trackingCode {
            flex: 1;
            padding: 12px 15px;
            font-size: 16px;
            border: 2px solid #ddd;
            border-radius: 8px;
            outline: none;
            transition: border 0.2s;
        }
        #trackingCode:focus {
            border-color: #007bff;
        }
        button {
            padding: 12px 25px;
            background: #007bff;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            cursor: pointer;
            transition: background 0.2s;
        }
        button:hover {
            background: #0056b3;
        }
        #map {
            height: 400px;
            width: 100%;
            border-radius: 8px;
            margin-bottom: 20px;
            border: 1px solid #ccc;
        }
        #statusMessage {
            padding: 15px;
            background: #e9f7fe;
            border-left: 4px solid #007bff;
            border-radius: 4px;
            font-size: 18px;
            color: #333;
        }
        .invalid {
            background: #f8d7da;
            border-left-color: #dc3545;
            color: #721c24;
        }
        footer {
            text-align: center;
            margin-top: 20px;
            color: #777;
            font-size: 14px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>📦 Track Your Delivery</h1>

        <div class="track-box">
            <input type="text" id="trackingCode" placeholder="Enter tracking code (e.g., B3xFtQz4)" value="B3xFtQz4">
            <button onclick="track()">Track</button>
        </div>

        <!-- Map will be inserted here -->
        <div id="map"></div>

        <!-- Status message -->
        <div id="statusMessage">Enter a code to see the location.</div>

        <footer>
            Demo tracker – uses Leaflet & OpenStreetMap
        </footer>
    </div>

    <!-- Leaflet JavaScript -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

    <script>
        // ---------- DATA ----------
        // You can easily add more tracking codes here
        const trackingData = {
            "B3xFtQz4": {
                lat: 28.5383,        // Orlando, Florida
                lng: -81.3792,
                message: "The item is ready to be shipped out to the United States, Florida."
            }
            // Add more codes as needed:
            // "ABC123": { lat: 25.7617, lng: -80.1918, message: "Out for delivery in Miami." }
        };

        // ---------- MAP SETUP ----------
        // Initialize the map with a default world view (will be updated when a code is found)
        const map = L.map('map').setView([20, 0], 2);  // zoomed out world view

        // Add OpenStreetMap tiles
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);

        // Variable to hold the current marker (so we can remove it when a new code is entered)
        let currentMarker = null;

        // ---------- TRACKING FUNCTION ----------
        function track() {
            const codeInput = document.getElementById('trackingCode').value.trim();
            const statusDiv = document.getElementById('statusMessage');

            // Remove any existing marker from previous search
            if (currentMarker) {
                map.removeLayer(currentMarker);
                currentMarker = null;
            }

            // Check if the code exists in our data
            if (trackingData.hasOwnProperty(codeInput)) {
                const data = trackingData[codeInput];

                // Update map: center on the location and set zoom level
                map.setView([data.lat, data.lng], 13);

                // Add a marker
                currentMarker = L.marker([data.lat, data.lng]).addTo(map);
                // Optional: bind a popup with the code
                currentMarker.bindPopup(`<b>Code:</b> ${codeInput}`).openPopup();

                // Update status message
                statusDiv.textContent = data.message;
                statusDiv.className = ''; // remove any error class
            } else {
                // Invalid code
                statusDiv.textContent = '❌ Invalid tracking code. Please try again.';
                statusDiv.className = 'invalid';

                // Optionally reset map to world view
                map.setView([20, 0], 2);
            }
        }

        // Automatically track on page load if the input already has the code (it does)
        window.onload = function() {
            track();
        };
    </script>
</body>
</html>
