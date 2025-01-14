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

    const symbols = [
      { symbol: "USDTIRT", title: "دلار", unit: "تومان", factor: 0.1 },
      { symbol: "BTCUSDT", title: "بیتکوین", unit: "دلار", factor: 1 },
      { symbol: "XAUTUSDT", title: "طلای جهانی", unit: "دلار", factor: 1 },
    ];

    const escapeMarkdown = (text) => {
      return text.replace(/([*_`\[\]()])/g, '\\$1');
    };

    const messages = [];

    const savePriceToKV = async (key, price) => {
      // ذخیره قیمت در Storage محلی (در اینجا برای مثال از localStorage استفاده کرده‌ایم)
      localStorage.setItem(key, price.toString());
    };

    const getLastPriceFromKV = async (key) => {
      const lastPrice = localStorage.getItem(key);
      return lastPrice ? parseFloat(lastPrice) : null;
    };

    for (const { symbol, title, unit, factor } of symbols) {
      await new Promise((resolve, reject) => {
        const ws = new WebSocket(socketUrl);

        ws.onopen = () => {
          console.log(`Connected to WebSocket for ${symbol}.`);

          ws.send(JSON.stringify({
            connect: { name: 'js' },
            id: 3,
          }));

          ws.send(JSON.stringify({
            subscribe: {
              channel: `public:orderbook-${symbol}`,
              recover: true,
              offset: 0,
              epoch: '0',
              delta: 'fossil',
            },
            id: 4,
          }));
        };

        ws.onmessage = async (event) => {
          try {
            const message = JSON.parse(event.data);
            if (message.id === 4 && message.subscribe && message.subscribe.publications) {
              const publication = message.subscribe.publications[0];
              if (publication && publication.data) {
                const parsedData = JSON.parse(publication.data);
                if (parsedData.asks && parsedData.asks.length > 0) {
                  let current_price = parsedData.asks[0][0];

                  current_price *= factor;

                  const lastPrice = await getLastPriceFromKV(symbol);
                  let trend = '';

                  if (lastPrice !== null) {
                    trend = current_price > lastPrice ? '🟩' : current_price < lastPrice ? '🟥' : '⬜️';
                  }

                  await savePriceToKV(symbol, current_price);

                  const formattedNumber = new Intl.NumberFormat('en-US').format(current_price);

                  // ساخت پیام با Markdown (تایتل و واحد با حروف درشت)
                  messages.push(`${trend} **${escapeMarkdown(title)}**: ${formattedNumber} **${escapeMarkdown(unit)}**`);

                  ws.close();
                  resolve();
                }
              }
            }
          } catch (error) {
            console.error(`Error parsing message for ${symbol}:`, error);
            ws.close();
            reject();
          }
        };

        ws.onerror = (error) => {
          console.error(`WebSocket error for ${symbol}:`, error);
          reject();
        };

        ws.onclose = () => {
          console.log(`WebSocket connection closed for ${symbol}.`);
        };
      });
    }

    if (messages.length > 0) {
      // نمایش پیام‌ها در سایت
      document.getElementById("prices").innerHTML = messages.join('<br />');
    } else {
      document.getElementById("prices").innerHTML = `<p class="error">خطا در دریافت قیمت‌ها.</p>`;
    }

  </script>

</body>
</html>
