<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mục Tiêu Chill & Cổ Vũ</title>
  <link href="https://fonts.googleapis.com/css2?family=Quicksand:wght@500;600;700&display=swap" rel="stylesheet">
  <style>
    body {
      background-color: #FAF6F0;
      font-family: 'Quicksand', sans-serif;
      color: #4A5568;
      margin: 0;
      padding: 40px 20px;
      display: flex;
      justify-content: center;
    }
    .app-card {
      background: #FFFFFF;
      border-radius: 24px;
      padding: 28px;
      max-width: 600px;
      width: 100%;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.04);
      border: 1px solid #F0EAE1;
    }
    .header {
      text-align: center;
      margin-bottom: 24px;
    }
    .header h1 {
      color: #3D405B;
      font-size: 1.6rem;
      margin: 6px 0;
    }
    .header p {
      color: #8D99AE;
      font-size: 0.9rem;
      margin: 0;
    }
    .badge {
      background: #E2F0CB;
      color: #4A6B32;
      padding: 4px 12px;
      border-radius: 20px;
      font-size: 0.8rem;
      font-weight: 700;
    }
    .form-group {
      background: #FAF9F6;
      padding: 18px;
      border-radius: 16px;
      margin-bottom: 24px;
    }
    .input-field {
      width: 100%;
      padding: 10px 14px;
      border: 1.5px solid #E9ECEF;
      border-radius: 10px;
      margin-top: 6px;
      margin-bottom: 12px;
      box-sizing: border-box;
      font-family: inherit;
    }
    .btn-add {
      width: 100%;
      background: #FFDAC1;
      color: #6D4C41;
      border: none;
      padding: 12px;
      border-radius: 10px;
      font-weight: 700;
      cursor: pointer;
      font-family: inherit;
    }
    .goal-item {
      background: #FFFFFF;
      border: 1px solid #F3F0E6;
      border-radius: 16px;
      padding: 18px;
      margin-bottom: 16px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.02);
    }
    .message-box {
      padding: 12px 16px;
      border-radius: 10px;
      font-size: 0.9rem;
      margin-top: 10px;
      line-height: 1.4;
    }
  </style>
</head>
<body>

  <div class="app-card">
    <div class="header">
      <span class="badge">Chill Goal Tracker</span>
      <h1>Góc Mục Tiêu Nhẹ Nhàng 🌿</h1>
      <p>Đặt mục tiêu không áp lực, vừa làm vừa tự chăm sóc bản thân.</p>
    </div>

    <div class="form-group">
      <label><strong>Mục tiêu của cậu:&strong></label>
      <input type="text" id="goalTitle" class="input-field" placeholder="Ví dụ: Đọc 5 trang sách...">
      
      <div style="display: flex; gap: 10px;">
        <div style="flex:1;">
          <label><small>Hạn chót:&small></label>
          <input type="date" id="goalDeadline" class="input-field">
        </div>
        <div style="flex:1;">
          <label><small>Giờ nhắc nhở:&small></label>
          <input type="time" id="goalTime" class="input-field">
        </div>
      </div>

      <button id="addBtn" class="btn-add">+ Thêm mục tiêu mới</button>
    </div>

    <div id="goalList"></div>
  </div>

  <script>
    let goals = JSON.parse(localStorage.getItem('my_chill_goals')) || [];

    function saveAndRender() {
      localStorage.setItem('my_chill_goals', JSON.stringify(goals));
      render();
    }

    function getFeedback(progress) {
      const p = Number(progress);
      if (p === 100) return { msg: "🎉 Tuyệt vời quá! Cậu đã hoàn thành xuất sắc mục tiêu này rồi. Cậu có muốn đặt thêm một mục tiêu mới không?", bg: "#E2F0CB", color: "#2D5A27" };
      if (p >= 70) return { msg: "🌟 Chỉ còn một chút xíu nữa thôi là cán đích rồi! Cố lên nhé, cậu làm rất tốt!", bg: "#C7CEEA", color: "#3B4B70" };
      if (p >= 50) return { msg: "☕ Hoan hô! Cậu đã đi được một nửa chặng đường rồi đấy. Nghỉ xíu rồi tiếp nha!", bg: "#FFDAC1", color: "#7A4B2A" };
      return { msg: "🍃 Không sao đâu nè. Có lẽ mục tiêu hơi quá sức hoặc thời gian chưa đủ. Cậu cứ thong thả chia nhỏ ra nhé!", bg: "#FFB7B2", color: "#7A2E28" };
    }

    function render() {
      const listContainer = document.getElementById('goalList');
      listContainer.innerHTML = '';

      goals.forEach((goal, index) => {
        const fb = getFeedback(goal.progress);
        const item = document.createElement('div');
        item.className = 'goal-item';
        item.innerHTML = `
          <div style="display:flex; justify-size: space-between; align-items: center;">
            <h3 style="margin:0; color:#3D405B;">${goal.title}</h3>
            <small style="color:#9A8C98;">⏰ ${goal.time || 'Tự do'}</small>
          </div>
          <div style="margin: 12px 0;">
            <div style="display:flex; justify-content: space-between; font-size: 0.85rem;">
              <span>Tiến độ</span>
              <strong>${goal.progress}%</strong>
            </div>
            <input type="range" min="0" max="100" value="${goal.progress}" onchange="updateProgress(${index}, this.value)" style="width:100%; accent-color:#B5EAD7;">
          </div>
          <div class="message-box" style="background:${fb.bg}30; border-left:4px solid ${fb.bg}; color:${fb.color};">
            ${fb.msg}
          </div>
        `;
        listContainer.appendChild(item);
      });
    }

    function updateProgress(index, val) {
      goals[index].progress = Number(val);
      saveAndRender();
    }

    document.getElementById('addBtn').addEventListener('click', () => {
      const title = document.getElementById('goalTitle').value.trim();
      if (!title) return alert('Hãy nhập tên mục tiêu nhé!');
      goals.unshift({
        title,
        deadline: document.getElementById('goalDeadline').value,
        time: document.getElementById('goalTime').value,
        progress: 0
      });
      document.getElementById('goalTitle').value = '';
      saveAndRender();
    });

    render();
  </script>
</body>
</html>
