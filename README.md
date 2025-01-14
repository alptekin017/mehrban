<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mehrban Salagh</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      text-align: center;
      background-color: #001f3d; /* رنگ پس‌زمینه سرمه‌ای */
      color: white; /* رنگ متن سفید برای تضاد با پس‌زمینه */
    }

    header {
      flex: 1;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 36px;
      background-color: #333;
    }

    main {
      flex: 3;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      font-size: 18px;
      padding: 20px;
    }

    .price {
      font-size: 24px;
      font-weight: bold;
    }

    .error {
      color: red;
      font-size: 18px;
    }
  </style>
</head>
<body>

  <header>
    <h1>Mehrban Salagh</h1>
  </header>

  <main>
    <h2>قیمت ارزها آنلاین</h2>
    <div id="prices">
      <p>در حال دریافت قیمت‌ها...</p>
    </div>
  </main>

  <script>
    // URL WebSocket برای نوبیتکس با لینک جدید
    const socketUrl = "wss://wss.nobitex.ir/connection/websocket";

    const socket = new WebSocket(socketUrl);

    socket.onopen = () => {
      console.log('Connected to WebSocket');
      // ارسال درخواست برای قیمت‌ها
      socket.send(JSON.stringify({
        "method": "subscribe",
        "params": {
          "channel": "ticker",
          "symbol": "BTC-IRR" // ارز بیت‌کوین به ریال
        }
      }));
    };

    socket.onmessage = (event) => {
      const data = JSON.parse(event.data);
      if (data.data && data.data.last) {
        const lastPrice = data.data.last; // آخرین قیمت
        document.getElementById("prices").innerHTML = `
          <p class="price">آخرین قیمت بیت‌کوین: ${lastPrice} ریال</p>
        `;
      }
    };

    socket.onerror = (error) => {
      console.error('WebSocket Error: ', error);
      document.getElementById("prices").innerHTML = `<p class="error">خطا در اتصال به WebSocket</p>`;
    };

    socket.onclose = () => {
      console.log('WebSocket connection closed');
    };
  </script>

</body>
</html>
