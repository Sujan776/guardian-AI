<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guardian Band - AI-Powered Wearable Interface</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin=""/>
    <style>
        :root {
            --bg-primary: #060713;
            --bg-secondary: #0c0d21;
            --bg-card: #11132b;
            --accent-cyan: #00f0ff;
            --accent-pink: #ff0055;
            --accent-purple: #7000ff;
            --text-main: #f0f1f7;
            --text-muted: #626b8f;
            --border-color: rgba(255, 255, 255, 0.06);
            --card-glow: 0 12px 40px rgba(0, 0, 0, 0.6);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
        }

        body {
            background-color: var(--bg-primary);
            color: var(--text-main);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
            background-image: radial-gradient(circle at 80% 20%, rgba(112, 0, 255, 0.08) 0%, transparent 50%);
        }

        /* Responsive Dashboard Container Split Layout */
        .dashboard-container {
            width: 100vw;
            height: 100vh;
            max-width: 1920px;
            max-height: 1080px;
            display: grid;
            grid-template-columns: 420px 1fr;
            padding: 25px;
            gap: 25px;
        }

        /* ==================== LEFT CONTROL PANEL ==================== */
        .hardware-panel {
            background-color: var(--bg-secondary);
            border-radius: 20px;
            border: 1px solid var(--border-color);
            box-shadow: var(--card-glow);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: relative;
            overflow: hidden;
        }

        .hardware-panel::before {
            content: '';
            position: absolute;
            width: 100%;
            height: 100%;
            background: linear-gradient(180deg, rgba(0, 240, 255, 0.02) 0%, transparent 100%);
            top: 0;
            left: 0;
            pointer-events: none;
        }

        .brand-title {
            position: absolute;
            top: 30px;
            left: 30px;
            font-size: 24px;
            font-weight: 900;
            letter-spacing: 1px;
            background: linear-gradient(90deg, #fff 0%, var(--accent-pink) 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        /* Wearable Dial Interface Structure */
        .wearable-chassis {
            width: 290px;
            height: 290px;
            border-radius: 50%;
            background: #080916;
            border: 4px solid #1a1c38;
            box-shadow: 0 0 50px rgba(255, 0, 85, 0.15), inset 0 0 30px rgba(0, 240, 255, 0.05);
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
        }

        .outer-pulsing-track {
            width: 250px;
            height: 250px;
            border-radius: 50%;
            border: 2px dashed rgba(0, 240, 255, 0.4);
            display: flex;
            justify-content: center;
            align-items: center;
            animation: rotateTrack 20s linear infinite;
        }

        @keyframes rotateTrack {
            100% { transform: rotate(360deg); }
        }

        .inner-core-display {
            width: 210px;
            height: 210px;
            border-radius: 50%;
            background: radial-gradient(circle, #160818 0%, #08040d 100%);
            border: 3px solid var(--accent-pink);
            box-shadow: 0 0 30px rgba(255, 0, 85, 0.4);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
            position: absolute;
            animation: dynamicCorePulse 2s infinite alternate;
        }

        @keyframes dynamicCorePulse {
            0% { box-shadow: 0 0 20px rgba(255, 0, 85, 0.3); transform: scale(0.98); }
            100% { box-shadow: 0 0 35px rgba(255, 0, 85, 0.5); transform: scale(1.02); }
        }

        .sos-status-label {
            font-size: 11px;
            font-weight: 700;
            color: var(--accent-pink);
            letter-spacing: 3px;
            margin-bottom: 4px;
        }

        .sos-main-state {
            font-size: 32px;
            font-weight: 900;
            color: #ffffff;
            letter-spacing: 1px;
        }

        .sos-timer-clock {
            font-family: monospace;
            font-size: 12px;
            color: var(--text-muted);
            margin-top: 6px;
            background: rgba(0, 0, 0, 0.3);
            padding: 2px 8px;
            border-radius: 4px;
            border: 1px solid var(--border-color);
        }

        /* ==================== RIGHT METRICS WORKSPACE ==================== */
        .metrics-workspace {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .system-badge-container {
            display: flex;
            justify-content: flex-end;
        }

        .architecture-pill {
            background: rgba(0, 240, 255, 0.05);
            border: 1px solid var(--accent-cyan);
            color: var(--accent-cyan);
            padding: 6px 16px;
            border-radius: 20px;
            font-size: 11px;
            font-weight: 700;
            letter-spacing: 1.5px;
            text-transform: uppercase;
        }

        .data-card {
            background-color: var(--bg-secondary);
            border: 1px solid var(--border-color);
            border-radius: 16px;
            padding: 20px;
            box-shadow: var(--card-glow);
        }

        /* Threat Header Module */
        .threat-alert-box {
            border-left: 4px solid var(--accent-pink);
            background: linear-gradient(90deg, rgba(255, 0, 85, 0.04) 0%, var(--bg-secondary) 100%);
        }

        .card-header-label {
            font-size: 11px;
            font-weight: 700;
            color: var(--text-muted);
            text-transform: uppercase;
            letter-spacing: 1.5px;
            margin-bottom: 8px;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        .threat-title-headline {
            font-size: 20px;
            font-weight: 800;
            color: var(--accent-pink);
            margin-bottom: 6px;
        }

        .threat-summary-prose {
            font-size: 13px;
            color: #a4b3d6;
            line-height: 1.5;
        }

        /* Map Window Structure */
        .map-card-wrapper {
            padding: 0;
            overflow: hidden;
            position: relative;
        }

        #map-canvas {
            height: 280px;
            width: 100%;
            background: #090a14;
        }

        /* Dark Vector Inversion Filter for Leaflet Tile Engine */
        .leaflet-tile-container {
            filter: invert(100%) hue-rotate(180deg) sepia(50%) saturate(160%) brightness(80%) contrast(110%);
        }

        .map-telemetry-banner {
            background: #090a15;
            border-top: 1px solid var(--border-color);
            padding: 10px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 12px;
        }

        .gnss-status-indicator {
            display: flex;
            align-items: center;
            gap: 6px;
            color: var(--accent-cyan);
            font-weight: bold;
        }

        .gnss-pulse-dot {
            width: 6px;
            height: 6px;
            background-color: var(--accent-cyan);
            border-radius: 50%;
            animation: cyanGlow 1.5s infinite ease-in-out;
        }

        @keyframes cyanGlow {
            0%, 100% { opacity: 0.4; }
            50% { opacity: 1; box-shadow: 0 0 8px var(--accent-cyan); }
        }

        /* Communications Mesh Framework Network Status Layout */
        .mesh-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
        }

        .mesh-node-item {
            background-color: var(--bg-card);
            border: 1px solid var(--border-color);
            border-radius: 10px;
            padding: 15px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            min-height: 85px;
            transition: all 0.3s ease;
        }

        .mesh-node-item.active-node {
            border-color: rgba(0, 240, 255, 0.2);
            box-shadow: 0 0 15px rgba(0, 240, 255, 0.05);
        }

        .mesh-node-item.alert-node {
            border-color: rgba(255, 0, 85, 0.25);
            box-shadow: 0 0 15px rgba(255, 0, 85, 0.05);
        }

        .mesh-node-tag {
            font-size: 10px;
            font-weight: 700;
            color: var(--text-muted);
            text-transform: uppercase;
            letter-spacing: 0.8px;
        }

        .mesh-node-value {
            font-size: 13px;
            font-weight: 800;
            margin-top: 5px;
        }

        .status-cyan { color: var(--accent-cyan); }
        .status-pink { color: var(--accent-pink); font-weight: 900; }

        /* Footer Telemetry Coordinates Row Layout */
        .bottom-data-bar {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 20px;
            text-align: left;
        }

        .metric-field-label {
            font-size: 10px;
            font-weight: 700;
            color: var(--text-muted);
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 4px;
        }

        .metric-field-value {
            font-size: 16px;
            font-weight: 700;
            color: #ffffff;
            font-family: monospace;
        }

        /* Dynamic Map Pulse Marker Asset style sheet overrides */
        .leaflet-radar-marker {
            background: var(--accent-pink);
            border: 2px solid #fff;
            border-radius: 50%;
            box-shadow: 0 0 20px var(--accent-pink);
        }
        
        .leaflet-radar-marker::after {
            content: '';
            display: block;
            width: 400%;
            height: 400%;
            margin-left: -150%;
            margin-top: -150%;
            border-radius: 50%;
            border: 2px solid var(--accent-pink);
            animation: radarRingOut 1.6s infinite linear;
        }

        @keyframes radarRingOut {
            0% { transform: scale(0.1); opacity: 1; }
            100% { transform: scale(1); opacity: 0; }
        }

        @media (max-width: 1024px) {
            .dashboard-container { grid-template-columns: 1fr; height: auto; max-height: none; }
            .hardware-panel { height: 400px; }
            .mesh-grid { grid-template-columns: 1fr 1fr; }
        }
    </style>
</head>
<body>

    <div class="dashboard-container">
        <div class="hardware-panel">
            <div class="brand-title">GUARDIAN BAND</div>
            
            <div class="wearable-chassis">
                <div class="outer-pulsing-track"></div>
                <div class="inner-core-display">
                    <span class="sos-status-label">SOS LINK</span>
                    <span class="sos-main-state">ACTIVE</span>
                    <span class="sos-timer-clock" id="timer-output">REC: 00:00s</span>
                </div>
            </div>
        </div>

        <div class="metrics-workspace">
            <div class="system-badge-container">
                <div class="architecture-pill">AI WEARABLE SYSTEM V2.4 ARCHITECTURE</div>
            </div>

            <div class="data-card threat-alert-box">
                <div class="card-header-label">
                    <span>AI THREAT DETECTION</span>
                    <span style="width:8px; height:8px; background-color:var(--accent-pink); border-radius:50%;"></span>
                </div>
                <div class="threat-title-headline">CRITICAL CRASH & SCREAM DETECTED</div>
                <div class="threat-summary-prose">
                    Hardware sensory suite registered sharp acoustic spike (104dB) corresponding to vocal distress profiles matched with sudden 1.8m vertical vector drop.
                </div>
            </div>

            <div class="data-card map-card-wrapper">
                <div id="map-canvas"></div>
                <div class="map-telemetry-banner">
                    <div class="gnss-status-indicator">
                        <div class="gnss-pulse-dot"></div>
                        <span id="mesh-region-lbl">BROADCAST REGION: INITIALIZING</span>
                    </div>
                    <div style="color:var(--text-muted); font-size:11px;">CONNECTED VIA HARDWARE GNSS RECEIVER</div>
                </div>
            </div>

            <div class="data-card">
                <div class="card-header-label">MESH COMMUNICATIONS ARCHITECTURE</div>
                <div class="mesh-grid">
                    <div class="mesh-node-item active-node">
                        <span class="mesh-node-tag">Smartphone Link</span>
                        <span class="mesh-node-value status-cyan">SECURE MESH</span>
                    </div>
                    <div class="mesh-node-item active-node">
                        <span class="mesh-node-tag">Cloud Gateway</span>
                        <span class="mesh-node-value status-cyan">STABLE UPLINK</span>
                    </div>
                    <div class="mesh-node-item alert-node">
                        <span class="mesh-node-tag">Police Control</span>
                        <span class="mesh-node-value status-pink">DISPATCHING</span>
                    </div>
                    <div class="mesh-node-item active-node">
                        <span class="mesh-node-tag">Kin Contacts</span>
                        <span class="mesh-node-value status-cyan">SMS ROUTED</span>
                    </div>
                    <div class="mesh-node-item active-node">
                        <span class="mesh-node-tag">Civic Responders</span>
                        <span class="mesh-node-value status-cyan" id="mesh-volunteers-count">3 IN RANGE</span>
                    </div>
                    <div class="mesh-node-item active-node">
                        <span class="mesh-node-tag">Audio Blackbox</span>
                        <span class="mesh-node-value status-cyan">STREAMING</span>
                    </div>
                </div>
            </div>

            <div class="data-card bottom-data-bar">
                <div>
                    <div class="metric-field-label">LATITUDE</div>
                    <div class="metric-field-value" id="val-lat">12.92476° N</div>
                </div>
                <div>
                    <div class="metric-field-label">LONGITUDE</div>
                    <div class="metric-field-value" id="val-lng">80.20737° E</div>
                </div>
                <div>
                    <div class="metric-field-label">SATELLITES</div>
                    <div class="metric-field-value" id="val-sats" style="color:var(--accent-cyan);">9 / 12 Active</div>
                </div>
                <div>
                    <div class="metric-field-label">NFT INTEGRITY</div>
                    <div class="metric-field-value" style="color:#00ff66;">99.8% (Encrypted)</div>
                </div>
            </div>
        </div>
    </div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    <script>
        // Set coordinates to match the Jalladianpet / Velachery region from your prototype screenshots
        const primaryTargetCoordinates = { lat: 12.92476, lng: 80.20737, regionName: "LIVE LOCAL INTERCEPT" };
        
        let dashboardMap, coreRadarMarker;
        let cumulativeRuntimeSeconds = 20; // Starts from 20s timestamp matching sample logs

        function buildMapInterface(lat, lng) {
            dashboardMap = L.map('map-canvas', {
                zoomControl: false,
                attributionControl: false
            }).setView([lat, lng], 15);

            // Fetch dark mode map assets
            L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
                maxZoom: 18
            }).addTo(dashboardMap);

            // Setup dynamic radar tracking asset
            const trackingIcon = L.divIcon({
                className: 'leaflet-radar-marker',
                iconSize: [10, 10],
                iconAnchor: [5, 5]
            });

            coreRadarMarker = L.marker([lat, lng], { icon: trackingIcon }).addTo(dashboardMap);
            
            document.getElementById('mesh-region-lbl').innerText = `BROADCAST REGION: ${primaryTargetCoordinates.regionName}`;
            updateInterfaceValues(lat, lng);
            initiateDataFuzzing();
        }

        function updateInterfaceValues(lat, lng) {
            document.getElementById('val-lat').innerText = lat.toFixed(5) + "° N";
            document.getElementById('val-lng').innerText = lng.toFixed(5) + "° E";
        }

        // Simulates real-time sensor variances to replicate an active hardware data stream
        function initiateDataFuzzing() {
            setInterval(() => {
                // Micro-variations in GNSS location tracking metrics
                const microLatDelta = (Math.random() - 0.5) * 0.00015;
                const microLngDelta = (Math.random() - 0.5) * 0.00015;
                
                const modifiedLat = primaryTargetCoordinates.lat + microLatDelta;
                const modifiedLng = primaryTargetCoordinates.lng + microLngDelta;
                
                const updatedCoordinateInstance = new L.LatLng(modifiedLat, modifiedLng);
                coreRadarMarker.setLatLng(updatedCoordinateInstance);
                
                updateInterfaceValues(modifiedLat, modifiedLng);

                // Fluctuate active satellite counts dynamically (between 8 and 11)
                const calculatedSatellites = Math.floor(Math.random() * 4) + 8;
                document.getElementById('val-sats').innerText = `${calculatedSatellites} / 12 Active`;

                // Fluctuate active volunteer mesh count (between 2 and 5)
                const simulatedVolunteers = Math.floor(Math.random() * 4) + 2;
                document.getElementById('mesh-volunteers-count').innerText = `${simulatedVolunteers} IN RANGE`;

            }, 4000);
        }

        // System clock configuration hook
        window.addEventListener('DOMContentLoaded', () => {
            setInterval(() => {
                cumulativeRuntimeSeconds++;
                const computedMinutes = Math.floor(cumulativeRuntimeSeconds / 60).toString().padStart(2, '0');
                const computedSeconds = (cumulativeRuntimeSeconds % 60).toString().padStart(2, '0');
                document.getElementById('timer-output').innerText = `REC: ${computedMinutes}:${computedSeconds}s`;
            }, 1000);

            // Attempt to fetch live location via browser Geolocation API
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    (hwPosition) => {
                        primaryTargetCoordinates.lat = hwPosition.coords.latitude;
                        primaryTargetCoordinates.lng = hwPosition.coords.longitude;
                        primaryTargetCoordinates.regionName = "HARDWARE GNSS SYSTEM CONNECTED";
                        buildMapInterface(primaryTargetCoordinates.lat, primaryTargetCoordinates.lng);
                    },
                    (hwError) => {
                        console.warn("Hardware GNSS denied. Launching target development sandbox layout.");
                        buildMapInterface(primaryTargetCoordinates.lat, primaryTargetCoordinates.lng);
                    }
                );
            } else {
                buildMapInterface(primaryTargetCoordinates.lat, primaryTargetCoordinates.lng);
            }
        });
    </script>
</body>
</html>
