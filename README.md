<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>에너지 & 물 절약 프로그램</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            background-color: #f4f4f4;
        }
        header {
            background-color: #4CAF50;
            color: white;
            padding: 20px;
            text-align: center;
        }
        .container {
            display: flex;
            flex-wrap: wrap;
            margin: 20px;
        }
        .section {
            flex: 1;
            margin: 10px;
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        .left, .right {
            flex-basis: 45%;
        }
        .bottom {
            width: 100%;
            margin-top: 20px;
        }
        h2 {
            color: #333;
            border-bottom: 1px solid #ddd;
            padding-bottom: 10px;
        }
        label, input, button {
            display: block;
            width: 100%;
            margin-bottom: 10px;
        }
        input {
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        .temp-buttons, .season-buttons {
            display: flex;
            gap: 10px;
        }
        .temp-buttons button, .season-buttons button {
            flex: 1;
            padding: 10px;
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
        }
        .temp-buttons button:hover, .season-buttons button:hover {
            background-color: #45a049;
        }
        .result {
            margin-top: 20px;
            padding: 10px;
            background-color: #f9f9f9;
            border: 1px solid #ddd;
            border-radius: 5px;
        }
        .download-btn {
            margin-top: 30px;
            background-color: #f0ad4e;
            color: white;
            padding: 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
        }
        .download-btn:hover {
            background-color: #ec971f;
        }
    </style>
    <!-- SheetJS 라이브러리 추가 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.0/xlsx.full.min.js"></script>
</head>
<body>

<header>
    <h1>에너지 & 물 절약 프로그램</h1>
</header>

<div class="container">
    <!-- 가전제품 사용 분석 -->
    <div class="section left">
        <h2>가전제품 사용 분석</h2>
        <label for="aircon">에어컨 사용 시간 (시간):</label>
        <input type="number" id="aircon" placeholder="에어컨 사용 시간 입력">

        <label for="fan">선풍기 사용 시간 (시간):</label>
        <input type="number" id="fan" placeholder="선풍기 사용 시간 입력">

        <label for="laptop">노트북 충전 시간 (시간):</label>
        <input type="number" id="laptop" placeholder="노트북 충전 시간 입력">

        <label for="phone">핸드폰 충전 시간 (시간):</label>
        <input type="number" id="phone" placeholder="핸드폰 충전 시간 입력">

        <label for="coffeePot">커피포트 사용 시간 (분):</label>
        <input type="number" id="coffeePot" placeholder="커피포트 사용 시간 입력">

        <label for="hairDryer">헤어드라이어 사용 시간 (분):</label>
        <input type="number" id="hairDryer" placeholder="헤어드라이어 사용 시간 입력">

        <button onclick="analyzeUsage()">가전제품 사용 분석</button>
        <div id="usageResult" class="result"></div>
    </div>

    <!-- 에어컨/히터 사용 제안 -->
    <div class="section right">
        <h2>에어컨/히터 사용 제안</h2>
        <label for="temperature">실내 온도 입력:</label>
        <input type="number" id="temperature" placeholder="온도 입력">

        <label>온도 단위 선택:</label>
        <div class="temp-buttons">
            <button onclick="setUnit('C')">섭씨 (°C)</button>
            <button onclick="setUnit('F')">화씨 (°F)</button>
            <button onclick="setUnit('K')">켈빈 (K)</button>
        </div>

        <label>현재 계절 선택:</label>
        <div class="season-buttons">
            <button onclick="setSeason('봄')">봄</button>
            <button onclick="setSeason('여름')">여름</button>
            <button onclick="setSeason('가을')">가을</button>
            <button onclick="setSeason('겨울')">겨울</button>
        </div>

        <button onclick="suggestTemperatureControl()">온도 제안</button>
        <div id="tempSuggestion" class="result"></div>
    </div>
</div>

<div class="container">
    <!-- 물 사용 분석 -->
    <div class="section bottom">
        <h2>물 사용 분석</h2>
        <label for="shower">샤워 시간 (분):</label>
        <input type="number" id="shower" placeholder="샤워 시간 입력">

        <label for="wash">설거지 시간 (분):</label>
        <input type="number" id="wash" placeholder="설거지 시간 입력">

        <button onclick="analyzeWaterUsage()">물 사용 분석</button>
        <div id="waterResult" class="result"></div>
    </div>
</div>

<!-- 엑셀 파일로 저장 버튼 -->
<div class="container">
    <button class="download-btn" onclick="downloadReport()">엑셀로 보고서 저장</button>
</div>

<script>
    let temperatureUnit = 'C'; // 기본값
    let selectedSeason = ''; // 선택된 계절

    function setUnit(unit) {
        temperatureUnit = unit;
        alert(`온도 단위가 ${unit}로 설정되었습니다.`);
    }

    function setSeason(season) {
        selectedSeason = season;
        alert(`현재 계절이 ${season}로 설정되었습니다.`);
    }

    function analyzeUsage() {
        const airconTime = parseFloat(document.getElementById('aircon').value) || 0;
        const fanTime = parseFloat(document.getElementById('fan').value) || 0;
        const laptopTime = parseFloat(document.getElementById('laptop').value) || 0;
        const phoneTime = parseFloat(document.getElementById('phone').value) || 0;
        const coffeePotTime = parseFloat(document.getElementById('coffeePot').value) || 0;
        const hairDryerTime = parseFloat(document.getElementById('hairDryer').value) || 0;

        const usageResult = document.getElementById('usageResult');
        usageResult.innerHTML = '';

        if (airconTime > 3) {
            usageResult.innerHTML += '에어컨을 많이 사용합니다. 온도를 조금 높여 에너지 절약을 고려하세요.<br>';
        }
        if (fanTime > 5) {
            usageResult.innerHTML += '선풍기를 오래 사용 중입니다. 필요할 때만 사용해보세요.<br>';
        }
        if (laptopTime > 4) {
            usageResult.innerHTML += '노트북 충전 시간이 길어요. 배터리 충전 후 충전기를 빼는 것이 좋습니다.<br>';
        }
        if (phoneTime > 2) {
            usageResult.innerHTML += '핸드폰 충전 시간이 길어요. 완충 후 충전기를 빼세요.<br>';
        }
        if (coffeePotTime > 15) {
            usageResult.innerHTML += '커피포트 사용 시간이 길어요. 적정량만 끓이세요.<br>';
        }
        if (hairDryerTime > 10) {
            usageResult.innerHTML += '헤어드라이어 사용 시간이 길어요. 짧게 사용해 전기를 절약하세요.<br>';
        }
    }

    function convertTemperature(temp, unit) {
        if (unit === 'F') {
            return (temp - 32) * 5 / 9; // 화씨 -> 섭씨
        } else if (unit === 'K') {
            return temp - 273.15; // 켈빈 -> 섭씨
        }
        return temp; // 섭씨
    }

    function suggestTemperatureControl() {
        const celsiusTemp = convertTemperature(parseFloat(document.getElementById('temperature').value), temperatureUnit);
        const tempSuggestion = document.getElementById('tempSuggestion');

        if (selectedSeason === '여름' && celsiusTemp < 24) {
            tempSuggestion.innerHTML = '온도가 너무 낮습니다. 에어컨 설정 온도를 높여 에너지를 절약하세요.<br>';
        } else if (selectedSeason === '겨울' && celsiusTemp > 20) {
            tempSuggestion.innerHTML = '온도가 너무 높습니다. 히터 설정 온도를 낮춰보세요.<br>';
        } else {
            tempSuggestion.innerHTML = '현재 온도는 적절합니다.<br>';
        }
    }

    const avgWaterUsage = {
        shower: 10,
        wash: 15
    };

    function analyzeWaterUsage() {
        const showerTime = parseFloat(document.getElementById('shower').value) || 0;
        const washTime = parseFloat(document.getElementById('wash').value) || 0;

        const waterResult = document.getElementById('waterResult');
        waterResult.innerHTML = '';

        if (showerTime > avgWaterUsage.shower) {
            waterResult.innerHTML += '샤워 시간이 길어요. 물 절약을 위해 시간을 줄여보세요.<br>';
        } else {
            waterResult.innerHTML += '샤워 시간이 적정합니다.<br>';
        }

        if (washTime > avgWaterUsage.wash) {
            waterResult.innerHTML += '설거지 시간이 길어요. 물을 절약할 방법을 고려하세요.<br>';
        } else {
            waterResult.innerHTML += '설거지 시간이 적정합니다.<br>';
        }
    }

    function downloadReport() {
        const airconTime = document.getElementById('aircon').value || 0;
        const fanTime = document.getElementById('fan').value || 0;
        const laptopTime = document.getElementById('laptop').value || 0;
        const phoneTime = document.getElementById('phone').value || 0;
        const coffeePotTime = document.getElementById('coffeePot').value || 0;
        const hairDryerTime = document.getElementById('hairDryer').value || 0;
        const showerTime = document.getElementById('shower').value || 0;
        const washTime = document.getElementById('wash').value || 0;

        const reportData = [
            ['항목', '시간'],
            ['에어컨 사용 시간', airconTime],
            ['선풍기 사용 시간', fanTime],
            ['노트북 충전 시간', laptopTime],
            ['핸드폰 충전 시간', phoneTime],
            ['커피포트 사용 시간', coffeePotTime],
            ['헤어드라이어 사용 시간', hairDryerTime],
            ['샤워 시간', showerTime],
            ['설거지 시간', washTime],
        ];

        const worksheet = XLSX.utils.aoa_to_sheet(reportData);
        const workbook = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(workbook, worksheet, '보고서');

        XLSX.writeFile(workbook, 'energy_water_report.xlsx');
    }
</script>

</body>
</html>
