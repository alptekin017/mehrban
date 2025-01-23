<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mehrban Salagh | قیمت ارزها</title>
  <style>
    body { font-family: Arial, sans-serif; background-color: #001f3d; color: white; text-align: center; }
    header { font-size: 24px; margin-bottom: 20px; }
    .price { font-size: 20px; margin: 10px 0; }
    .error { color: red; font-size: 18px; }
  </style>
</head>
<body>

  <header>💰 قیمت لحظه‌ای ارزها</header>
  <div id="prices">
    <p>⏳ در حال دریافت قیمت‌ها...</p>
  </div>

  <script>
    const cloudflareProxy = "(https://siteprice.alptekin017.workers.dev/)"; // آدرس Worker خودت را جایگزین کن

    async function fetchPrices() {
      try {
        const response = await fetch(cloudflareProxy);
        const data = await response.json();

        const symbols = {
          "USDTIRT": { title: "دلار", unit: "تومان", factor: 0.1 },
          "BTCUSDT": { title: "بیتکوین", unit: "دلار", factor: 1 },
          "XAUTUSDT": { title: "طلای جهانی", unit: "دلار", factor: 1 }
        };

        const messages = [];
        Object.keys(symbols).forEach(symbol => {
          if (data.stats[symbol]) {
            let current_price = parseFloat(data.stats[symbol].last) * symbols[symbol].factor;
            const formattedNumber = new Intl.NumberFormat('en-US').format(current_price);
            messages.push(`📌 **${symbols[symbol].title}**: ${formattedNumber} **${symbols[symbol].unit}**`);
          }
        });

        document.getElementById("prices").innerHTML = messages.join('<br />');
      } catch (error) {
        document.getElementById("prices").innerHTML = `<p class="error">⚠️ خطا در دریافت قیمت‌ها.</p>`;
      }
    }

    fetchPrices();
    setInterval(fetchPrices, 60000); // هر 60 ثانیه یک‌بار بروزرسانی
  </script>

</body>
</html>
