<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>即時天氣預報</title>
    <style>
        /* CSS 樣式設定 */
        body {
            font-family: 'PingFang TC', 'Microsoft JhengHei', sans-serif;
            background: linear-gradient(135deg, #6dd5ed, #2193b0);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            color: #333;
        }

        .weather-card {
            background: rgba(255, 255, 255, 0.9);
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.2);
            text-align: center;
            width: 320px;
            backdrop-filter: blur(10px);
        }

        h2 { margin-top: 0; color: #005f7a; }

        .temp-container {
            font-size: 4rem;
            font-weight: bold;
            margin: 10px 0;
            color: #ff8c00;
        }

        .description {
            font-size: 1.2rem;
            font-weight: 500;
            margin-bottom: 20px;
        }

        .details {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            border-top: 1px solid #ddd;
            padding-top: 20px;
        }

        .detail-item {
            font-size: 0.9rem;
            color: #666;
        }

        .detail-item span {
            display: block;
            font-weight: bold;
            color: #333;
            font-size: 1.1rem;
        }

        button {
            background: #2193b0;
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 25px;
            cursor: pointer;
            margin-top: 25px;
            transition: 0.3s;
            font-size: 1rem;
        }

        button:hover {
            background: #005f7a;
            transform: translateY(-2px);
        }

        #loading { font-style: italic; color: #666; }
        .hidden { display: none; }
    </style>
</head>
<body>

    <div class="weather-card">
        <h2 id="location">台北市</h2>
        
        <div id="loading">讀取天氣資料中...</div>

        <div id="weather-info" class="hidden">
            <div class="temp-container">
                <span id="temperature">--</span>°
            </div>
            <div class="description" id="weather-text">偵測中...</div>
            
            <div class="details">
                <div class="detail-item">
                    風速
                    <span id="windspeed">--</span> km/h
                </div>
                <div class="detail-item">
                    座標
                    <span id="coords">25.0, 121.5</span>
                </div>
            </div>
        </div>

        <button onclick="fetchWeather()">更新即時天氣資料</button>
    </div>

    <script>
        // 天氣代碼轉換表 (WMO Weather Interpretation Codes)
        const weatherCodes = {
            0: "晴朗無雲",
            1: "晴間多雲", 2: "多雲", 3: "陰天",
            45: "有霧", 48: "有霧",
            51: "毛毛雨", 53: "毛毛雨", 55: "毛毛雨",
            61: "小雨", 63: "中雨", 65: "大雨",
            71: "小雪", 73: "中雪", 75: "大雪",
            80: "陣雨", 81: "陣雨", 82: "強降雨",
            95: "雷陣雨", 96: "雷陣雨伴有冰雹"
        };

        async function fetchWeather() {
            const loading = document.getElementById('loading');
            const info = document.getElementById('weather-info');
            
            loading.classList.remove('hidden');
            info.classList.add('hidden');

            // 台北座標：25.03, 121.56
            const lat = 25.03;
            const lon = 121.56;
            const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true`;

            try {
                const response = await fetch(url);
                if (!response.ok) throw new Error("網路請求失敗");
                
                const data = await response.json();
                const current = data.current_weather;
                
                // 更新畫面內容
                document.getElementById('temperature').innerText = Math.round(current.temperature);
                document.getElementById('windspeed').innerText = current.windspeed;
                document.getElementById('coords').innerText = `${lat}, ${lon}`;
                
                // 轉換天氣代碼
                const code = current.weathercode;
                document.getElementById('weather-text').innerText = weatherCodes[code] || "未知天氣";

                // 顯示資訊區塊
                loading.classList.add('hidden');
                info.classList.remove('hidden');
                
            } catch (error) {
                console.error(error);
                loading.innerText = "資料載入失敗，請稍後再試。";
            }
        }

        // 初始化
        window.onload = fetchWeather;
    </script>

</body>
</html>
