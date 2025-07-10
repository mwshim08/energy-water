<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>나의 에너지 절약지수는 얼마일까?</title>
  <style>
    body {
      background-color: #e0e0e0;
      font-family: Arial, sans-serif;
      padding: 0;
      margin: 0;
    }
    .header {
      background-color: #444;
      color: white;
      text-align: center;
      padding: 20px 0;
    }
    .container {
      max-width: 800px;
      margin: 30px auto;
      background-color: #f9f9f9;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    h2 {
      color: #333;
    }
    label {
      display: block;
      margin-top: 15px;
    }
    input[type=number] {
      width: 100%;
      padding: 10px;
      margin-top: 5px;
      border: 1px solid #ccc;
      border-radius: 5px;
      appearance: textfield;
    }
    input::-webkit-outer-spin-button,
    input::-webkit-inner-spin-button {
      -webkit-appearance: none;
      margin: 0;
    }
    button {
      margin-top: 20px;
      padding: 12px;
      width: 100%;
      background-color: #666;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    button:hover {
      background-color: #555;
    }
    .result-box {
      background-color: #eee;
      margin-top: 20px;
      padding: 15px;
      border-radius: 5px;
    }
    img.histogram {
      width: 100%;
      margin-top: 30px;
      border: 1px solid #ccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <div class="header">
    <h1>나의 에너지 절약지수는 얼마일까?</h1>
  </div>

  <div class="container">
    <h2>에너지 사용 관련 정보를 입력해주세요!</h2>

    <label for="summerTemp">여름철 평균 실내 온도 (°C) - 에어컨 설정 온도</label>
    <input type="number" id="summerTemp" placeholder="예: 26">

    <label for="winterTemp">겨울철 평균 실내 온도 (°C) - 난방 설정 온도</label>
    <input type="number" id="winterTemp" placeholder="예: 20">

    <label for="summerTime">여름철 냉방 사용 시간 (시간)</label>
    <input type="number" id="summerTime" placeholder="예: 4">

    <label for="winterTime">겨울철 난방 사용 시간 (시간)</label>
    <input type="number" id="winterTime" placeholder="예: 5">

    <label for="chargeTime">충전(노트북/폰) 시간 (시간)</label>
    <input type="number" id="chargeTime" placeholder="예: 3">

    <button onclick="calculateScore()">절약지수 계산</button>

    <div id="result" class="result-box"></div>

    <img class="histogram" src="https://ifh.cc/g/ZKjxZ9.png" alt="절약지수 분포 히스토그램">
  </div>

  <script>
    const peerData = [
      -5.4, -3.2, -1.8, 0.1, 0.5, 1.0, 2.2, 3.3, 4.4, 5.1, 6.2, 7.0, 8.5, 9.0, 10.3,
      11.5, 12.1, 13.4, 14.6, 15.8, 16.9, 17.5, 18.2, 19.3, 20.5, 0.2, -2.3, 5.7, 7.1, 8.9,
      6.3, 3.7, 2.6, -1.1, 1.8, 3.2, 4.5, 7.8, 6.9, 2.1, -4.0, -2.5, 0.0, 2.4, 3.9, 5.2, 6.6, 9.9
    ];

    const avg = {
      summerTemp: 25.3,
      winterTemp: 21.2,
      summerTime: 3.6,
      winterTime: 4.1,
      chargeTime: 2.8
    };

    function calculateScore() {
      const summerTemp = parseFloat(document.getElementById('summerTemp').value);
      const winterTemp = parseFloat(document.getElementById('winterTemp').value);
      const summerTime = parseFloat(document.getElementById('summerTime').value);
      const winterTime = parseFloat(document.getElementById('winterTime').value);
      const chargeTime = parseFloat(document.getElementById('chargeTime').value);

      const totalTime = summerTime + winterTime + chargeTime;

      // 절약지수 계산 개선
      const score = 
        1.5 * (summerTemp - 20) +
        0.8 * (winterTemp - 18) +
        -1.0 * (summerTime - 2.5) +
        -1.0 * (winterTime - 2.5) +
        -0.7 * (chargeTime - 2);

      const percentile = peerData.filter(p => p <= score).length / peerData.length * 100;

      let feedback = `<strong>당신의 절약지수:</strong> ${score.toFixed(2)}<br>`;
      feedback += `<strong>상위 백분위:</strong> ${percentile.toFixed(1)}%<br><br>`;

      // 여름 온도 피드백
      const dSummer = summerTemp - avg.summerTemp;
      if (dSummer < -2) {
        feedback += "<strong>[여름 온도]</strong> 너무 낮아요. 에너지 낭비가 클 수 있습니다.<br>";
      } else if (dSummer < -1) {
        feedback += "<strong>[여름 온도]</strong> 다소 낮아요. 조금 높여보세요.<br>";
      } else if (dSummer < 1) {
        feedback += "<strong>[여름 온도]</strong> 적절한 온도입니다.<br>";
      } else if (dSummer < 2) {
        feedback += "<strong>[여름 온도]</strong> 좋은 설정입니다.<br>";
      } else {
        feedback += "<strong>[여름 온도]</strong> 훌륭해요! 매우 절약적입니다.<br>";
      }

      // 충전 시간 피드백
      const dCharge = chargeTime - avg.chargeTime;
      if (dCharge > 2) {
        feedback += "<strong>[충전 시간]</strong> 너무 깁니다. 절약을 고려해보세요.<br>";
      } else if (dCharge > 1) {
        feedback += "<strong>[충전 시간]</strong> 다소 깁니다.<br>";
      } else if (dCharge > -1) {
        feedback += "<strong>[충전 시간]</strong> 평균적입니다.<br>";
      } else {
        feedback += "<strong>[충전 시간]</strong> 충전을 잘 관리하고 있어요.<br>";
      }

      // 전체 시간 피드백
      if (totalTime > 12) {
        feedback += "<strong>[충전용 전기 사용]</strong> 사용량이 매우 많아요. 줄일 여지가 큽니다.<br>";
      } else if (totalTime > 9) {
        feedback += "<strong>[충전용 전기 사용]</strong> 평균보다 높은 편입니다.<br>";
      } else if (totalTime > 6) {
        feedback += "<strong>[충전용 전기 사용]</strong> 적정 수준입니다.<br>";
      } else {
        feedback += "<strong>[충전용 전기 사용]</strong> 매우 절약적입니다!<br>";
      }

      document.getElementById('result').innerHTML = feedback;
    }
  </script>
</body>
</html>
