<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mehrban Salagh | قیمت لحظه‌ای ارزها</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      text-align: center;
      background-color: #001f3d;
      color: white;
    }
    header {
      font-size: 24px;
      font-weight: bold;
      margin-bottom: 20px;
    }
    .price {
      font-size: 20px;
      margin: 10px 0;
    }
    .error {
      color: red;
      font-size: 18px;
    }
  </style>
</head>
<body>

  <header>💰 قیمت لحظه‌ای ارزها</header>
  <div id="prices">
    <p>⏳ در حال دریافت قیمت‌ها...</p>
  </div>

  <script>
    async function fetchPrices() {
      const socketUrl = "wss://ws.nobitex.ir/";
      const symbols = [
        { symbol: "USDTIRT", title: "دلار", unit: "تومان", factor: 0.1 },
        { symbol: "BTCUSDT", title: "بیتکوین", unit: "دلار", factor: 1 },
        { symbol: "XAUTUSDT", title: "طلای جهانی", unit: "دلار", factor: 1 }
      ];

      const messages = [];
      const escapeMarkdown = (text) => text.replace(/([*_`\[\]()])/g, '\\$1');

      for (const { symbol, title, unit, factor } of symbols) {
        await new Promise((resolve, reject) => {
          const ws = new WebSocket(socketUrl);

          ws.onopen = () => {
            ws.send(JSON.stringify({
              "type": "subscribe",
              "channels": [{ "name": "ticker", "market": symbol.toLowerCase() }]
            }));

            setInterval(() => {
              ws.send(JSON.stringify({ "type": "ping" }));
            }, 30000);
          };

          ws.onmessage = (event) => {
            try {
              const data = JSON.parse(event.data);
              if (data && data.price) {
                let current_price = parseFloat(data.price) * factor;
                const formattedNumber = new Intl.NumberFormat('en-US').format(current_price);

                messages.push(`📌 **${escapeMarkdown(title)}**: ${formattedNumber} **${escapeMarkdown(unit)}**`);
                ws.close();
                resolve();
              }
            } catch (error) {
              reject();
            }
          };

          ws.onerror = (error) => reject();
          ws.onclose = () => console.log(`Connection closed for ${symbol}`);
        });
      }

      if (messages.length > 0) {
        document.getElementById("prices").innerHTML = messages.join('<br />');
      } else {
        document.getElementById("prices").innerHTML = `<p class="error">⚠️ خطا در دریافت قیمت‌ها.</p>`;
      }
    }

    fetchPrices();
  </script>

</body>
</html>
