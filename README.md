<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>وب‌سایت مهربان سلاق</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      background-color: #f4f4f4;
    }

    header {
      background-color: #333;
      color: white;
      padding: 10px 20px;
      text-align: center;
    }

    nav {
      position: fixed;
      top: 0;
      left: 0;
      background-color: #222;
      width: 200px;
      height: 100%;
      padding-top: 20px;
    }

    nav a {
      color: white;
      text-decoration: none;
      display: block;
      padding: 15px;
      margin: 5px 0;
    }

    nav a:hover {
      background-color: #575757;
    }

    .content {
      margin-left: 220px;
      padding: 20px;
    }

    h1 {
      font-size: 36px;
      margin-bottom: 20px;
    }

    .section {
      margin-top: 20px;
    }

    .section img {
      width: 100%;
      max-width: 400px;
      margin: 10px 0;
    }
  </style>
</head>
<body>
  <header>
    <h1>مهربان سلاق</h1>
  </header>

  <nav>
    <a href="#home">خانه</a>
    <a href="#photos">عکس</a>
    <a href="#videos">فیلم</a>
  </nav>

  <div class="content">
    <div id="home" class="section">
      <h2>خانه</h2>
      <p>به سایت مهربان سلاق خوش آمدید!</p>
    </div>

    <div id="photos" class="section">
      <h2>عکس‌ها</h2>
      <img src="https://via.placeholder.com/400" alt="Photo 1">
      <img src="https://via.placeholder.com/400" alt="Photo 2">
    </div>

    <div id="videos" class="section">
      <h2>فیلم‌ها</h2>
      <p>در اینجا فیلم‌ها نمایش داده خواهند شد.</p>
    </div>
  </div>
</body>
</html>
