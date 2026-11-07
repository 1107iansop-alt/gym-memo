<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <title>GYM-MEMO</title>
  <style>
    :root {
      --bg-dark: #121214;
      --card-bg: #1c1c1e;
      --accent-red: #ff453a;
      --text-main: #ffffff;
      --text-sub: #8e8e93;
      --border-color: #2c2c2e;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      background-color: var(--bg-dark);
      color: var(--text-main);
      padding: max(24px, env(safe-area-inset-top) + 16px) 16px 80px 16px;
      max-width: 500px;
      margin: 0 auto;
    }
    header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 12px 0 20px 0;
    }
    h1 { font-size: 1.5rem; color: var(--accent-red); font-weight: 700; letter-spacing: 0.5px; }
    .card {
      background-color: var(--card-bg);
      border-radius: 16px;
      padding: 20px;
      margin-bottom: 20px;
      border: 1px solid var(--border-color);
    }
    .summary-title { font-size: 0.85rem; color: var(--text-sub); margin-bottom: 6px; }
    .summary-value { font-size: 2.2rem; font-weight: 800; }
    .summary-unit { font-size: 1rem; color: var(--text-sub); }
    .summary-stats { display: flex; gap: 20px; margin-top: 15px; font-size: 0.85rem; color: var(--text-sub); }
    
    /* カレンダー用スタイル */
    .calendar-container { margin-top: 15px; padding-top: 15px; border-top: 1px solid var(--border-color); }
    .calendar-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 4px; text-align: center; font-size: 0.75rem; }
    .calendar-dow { color: var(--text-sub); padding-bottom: 4px; font-weight: 600; }
    .calendar-day { aspect-ratio: 1; display: flex; align-items: center; justify-content: center; border-radius: 50%; color: var(--text-main); background: #2c2c2e; font-size: 0.75rem; }
    .calendar-day.active { background-color: var(--accent-red); font-weight: bold; }
    .calendar-day.empty { background: transparent; }

    .btn-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 24px; }
    .btn {
      background-color: var(--accent-red);
      color: white;
      border: none;
      border-radius: 14px;
      padding: 16px;
      font-weight: bold;
      font-size: 0.95rem;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 6px;
    }
    .btn-secondary { background-color: #2c2c2e; color: #0a84ff; }
    .history-head { font-size: 1.1rem; font-weight: 600; margin-bottom: 12px; }
    .empty-state {
      background-color: var(--card-bg);
      border-radius: 12px;
      padding: 30px 20px;
      text-align: center;
      color: var(--text-sub);
      font-size: 0.9rem;
    }
  </style>
</head>
<body>
  <header>
    <h1>GYM-MEMO</h1>
    <span>⚙️</span>
  </header>

  <div class="card">
    <div class="summary-title" id="current-month-label">2026年11月 総括</div>
    <div class="summary-value"><span id="total-weight">0.00</span> <span class="summary-unit">t</span></div>
    <div class="summary-stats">
      <span id="total-days">トレ日数: 0 日</span>
      <span id="total-sets">総セット数: 0 セット</span>
    </div>

    <!-- カレンダー表示 -->
    <div class="calendar-container">
      <div class="calendar-grid" id="calendar-grid">
        <!-- 曜日ヘッダー -->
        <div class="calendar-dow">日</div><div class="calendar-dow">月</div><div class="calendar-dow">火</div>
        <div class="calendar-dow">水</div><div class="calendar-dow">木</div><div class="calendar-dow">金</div>
        <div class="calendar-dow">土</div>
        <!-- 日付はJSで動的生成 -->
      </div>
    </div>
  </div>

  <div class="btn-grid">
    <button class="btn" onclick="addSampleRecord()">＋ 今日のトレーニング追加</button>
    <button class="btn btn-secondary" onclick="alert('月間サマリー画像生成機能は準備中です')">📷 月間サマリー画像</button>
  </div>

  <div class="history-head">最近のトレーニング履歴</div>
  <div id="history-area">
    <div class="empty-state">記録はまだありません。</div>
  </div>

  <script>
    // 簡易カレンダー描画ロジック（2026年11月基準）
    function renderCalendar(activeDays = []) {
      const grid = document.getElementById('calendar-grid');
      // 曜日ヘッダー以外を一旦クリア
      const dows = grid.querySelectorAll('.calendar-dow');
      grid.innerHTML = '';
      dows.forEach(el => grid.appendChild(el));

      const year = 2026;
      const month = 10; // 11月 (0始まり)
      const firstDay = new Date(year, month, 1).getDay();
      const lastDate = new Date(year, month + 1, 0).getDate();

      // 空白セル
      for (let i = 0; i < firstDay; i++) {
        const emptyCell = document.createElement('div');
        emptyCell.className = 'calendar-day empty';
        grid.appendChild(emptyCell);
      }

      // 日付セル
      for (let d = 1; d <= lastDate; d++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day';
        cell.textContent = d;
        if (activeDays.includes(d)) {
          cell.classList.add('active');
        }
        grid.appendChild(cell);
      }
    }

    // サンプル記録追加テスト用関数
    function addSampleRecord() {
      document.getElementById('total-weight').textContent = '1.25';
      document.getElementById('total-days').textContent = 'トレ日数: 1 日';
      document.getElementById('total-sets').textContent = '総セット数: 10 セット';
      
      document.getElementById('history-area').innerHTML = `
        <div class="card" style="margin-bottom: 10px; padding: 14px;">
          <div style="font-weight: bold; margin-bottom: 4px; color: var(--accent-red);">11月7日(土) 胸トレ</div>
          <div style="font-size: 0.85rem; color: var(--text-sub);">ベンチプレス 80kg × 10回 × 3セット</div>
        </div>
      `;
      renderCalendar([7]); // 7日をアクティブにする
    }

    renderCalendar();
  </script>
</body>
</html>
