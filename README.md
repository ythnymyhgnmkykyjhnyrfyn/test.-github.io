<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>โรงเรียนชุมชนวัดหนองจวง - ระบบจัดการโรงเรียน</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet" />
  <style>
    body { min-height: 100vh; }
    .hidden { display: none; }
    .spin-logo {
      animation: spin 1s linear infinite;
      width: 24px;
      height: 24px;
      vertical-align: middle;
      margin-right: 0.5rem;
      border-radius: 50%;
    }
    @keyframes spin {
      0% { transform: rotate(0deg);}
      100% { transform: rotate(360deg);}
    }
    /* Scrollbar สำหรับ dashboard */
    #student-list {
      max-height: 300px;
      overflow-y: auto;
    }
    #student-list::-webkit-scrollbar {
      width: 8px;
    }
    #student-list::-webkit-scrollbar-thumb {
      background-color: #3b82f6;
      border-radius: 4px;
    }
  </style>
</head>
<body class="bg-gray-100 text-gray-900 flex flex-col">

  <!-- Header -->
  <header class="bg-blue-800 text-white shadow py-4">
    <div class="container mx-auto flex items-center space-x-4 px-4">
      <img src="https://wnc.prachin1.go.th/images/logo.png" alt="โลโก้โรงเรียน" class="w-12 h-12 rounded-full border-2 border-white spin-logo" />
      <h1 class="text-3xl font-bold select-none">โรงเรียนชุมชนวัดหนองจวง</h1>
    </div>
  </header>

  <!-- Main -->
  <main class="container mx-auto flex-grow px-4 py-10 max-w-4xl">
    
    <!-- AUTH Section -->
    <section id="auth-section" class="max-w-md mx-auto">
      <form id="login-form" class="bg-white rounded shadow p-6 mb-8" onsubmit="handleLogin(event)">
        <h2 class="text-2xl font-semibold mb-6 text-center">🔑 เข้าสู่ระบบ</h2>
        <input id="username" type="text" placeholder="ชื่อผู้ใช้" required
               class="w-full mb-4 p-3 border border-gray-300 rounded focus:ring-2 focus:ring-blue-600" autocomplete="username" />
        <input id="password" type="password" placeholder="รหัสผ่าน" required
               class="w-full mb-6 p-3 border border-gray-300 rounded focus:ring-2 focus:ring-blue-600" autocomplete="current-password" />
        <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded font-semibold transition mb-3 flex justify-center items-center">
          <img src="https://wnc.prachin1.go.th/images/logo.png" class="spin-logo" alt="โลโก้โรงเรียนหมุน" />
          <span>เข้าสู่ระบบ</span>
        </button>
        <button type="button" onclick="toggleAuth('register')" class="w-full text-blue-600 hover:underline font-semibold">
          🆕 สมัครสมาชิก
        </button>
      </form>

      <form id="register-form" class="bg-white rounded shadow p-6 hidden" onsubmit="handleRegister(event)">
        <h2 class="text-2xl font-semibold mb-6 text-center">🆕 สมัครสมาชิก</h2>
        <input id="reg-username" type="text" placeholder="ตั้งชื่อผู้ใช้" required
               class="w-full mb-4 p-3 border border-gray-300 rounded focus:ring-2 focus:ring-green-600" autocomplete="username" />
        <input id="reg-password" type="password" placeholder="ตั้งรหัสผ่าน" required
               class="w-full mb-6 p-3 border border-gray-300 rounded focus:ring-2 focus:ring-green-600" autocomplete="new-password" />
        <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-3 rounded font-semibold transition mb-3 flex justify-center items-center">
          <svg class="animate-spin h-5 w-5 mr-2 text-white" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" fill="none"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v4a4 4 0 00-4 4H4z"></path></svg>
          สมัครสมาชิก
        </button>
        <button type="button" onclick="toggleAuth('login')" class="w-full text-green-600 hover:underline font-semibold">
          กลับไปเข้าสู่ระบบ
        </button>
      </form>
    </section>

    <!-- DASHBOARD -->
    <section id="dashboard" class="hidden bg-white rounded shadow p-8">
      <div class="flex justify-between items-center mb-8">
        <h2 class="text-3xl font-bold">🎓 ยินดีต้อนรับ, <span id="display-user"></span></h2>
        <button onclick="logout()" class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded font-semibold transition">
          ออกจากระบบ
        </button>
      </div>

      <!-- Tab Navigation -->
      <nav class="mb-6 border-b border-gray-300">
        <ul class="flex space-x-8 text-lg font-medium text-gray-600">
          <li><button id="tab-students" onclick="showTab('students')" class="border-b-4 border-blue-600 pb-2 text-blue-600">ข้อมูลนักเรียน</button></li>
          <li><button id="tab-news" onclick="showTab('news')" class="pb-2 hover:text-blue-600 transition">ข่าวสาร & กิจกรรม</button></li>
          <li><button id="tab-profile" onclick="showTab('profile')" class="pb-2 hover:text-blue-600 transition">โปรไฟล์ของฉัน</button></li>
        </ul>
      </nav>

      <!-- Tab Contents -->
      <div id="tab-content">

        <!-- Students -->
        <section id="tab-students-content" class="">
          <div class="flex justify-between items-center mb-4">
            <h3 class="text-xl font-semibold">ข้อมูลนักเรียนและคะแนนห้องเรียน</h3>
            <div id="clock" class="text-lg font-mono text-gray-700"></div>
          </div>

          <!-- ฟอร์มเพิ่มนักเรียน พร้อมเลือกห้อง -->
          <form id="student-form" class="mb-6 flex space-x-3 flex-wrap" onsubmit="addStudent(event)">
            <input id="student-name" type="text" placeholder="ชื่อ-สกุล" required class="flex-grow p-3 border border-gray-300 rounded focus:ring-2 focus:ring-blue-600" />
            <select id="student-grade" required class="w-32 p-3 border border-gray-300 rounded focus:ring-2 focus:ring-blue-600">
              <option value="" disabled selected>ชั้นเรียน</option>
              <optgroup label="ประถมศึกษา">
                <option value="ป.1">ป.1</option>
                <option value="ป.2">ป.2</option>
                <option value="ป.3">ป.3</option>
                <option value="ป.4">ป.4</option>
                <option value="ป.5">ป.5</option>
                <option value="ป.6">ป.6</option>
              </optgroup>
              <optgroup label="มัธยมศึกษา">
                <option value="ม.1">ม.1</option>
                <option value="ม.2">ม.2</option>
                <option value="ม.3">ม.3</option>
              </optgroup>
            </select>
            <select id="student-room" required class="w-24 p-3 border border-gray-300 rounded focus:ring-2 focus:ring-blue-600">
              <option value="" disabled selected>ห้อง</option>
              <option value="1">1</option>
              <option value="2">2</option>
              <option value="3">3</option>
              <option value="4">4</option>
              <option value="5">5</option>
               <option value="6">6</option>
            </select>
            <input id="student-score" type="number" placeholder="คะแนน (0-100)" min="0" max="100" required class="w-32 p-3 border border-gray-300 rounded focus:ring-2 focus:ring-blue-600" />
            <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-3 rounded font-semibold transition whitespace-nowrap">เพิ่มนักเรียน</button>
          </form>

          <div id="student-list" class="max-w-full overflow-auto border border-gray-300 rounded p-4 space-y-3" aria-live="polite"></div>

          <hr class="my-8" />

          <h3 class="text-xl font-semibold mb-4">สรุปคะแนนรวมรายชั้นและห้อง</h3>
          <div id="score-summary" class="space-y-3"></div>
        </section>

        <!-- News -->
        <section id="tab-news-content" class="hidden">
          <h3 class="text-xl font-semibold mb-4">ข่าวสารและกิจกรรมล่าสุด</h3>
          <ul id="news-list" class="space-y-4">
            <!-- ตัวอย่างข่าวสาร -->
            <li class="bg-gray-100 rounded p-4 shadow-sm">
              <h4 class="font-bold text-lg">📅 กิจกรรมวันแม่แห่งชาติ</h4>
              <p>โรงเรียนจัดกิจกรรมส่งเสริมความรักในครอบครัว วันที่ 12 สิงหาคม 2568</p>
            </li>
            <li class="bg-gray-100 rounded p-4 shadow-sm">
              <h4 class="font-bold text-lg">🎉 งานกีฬาโรงเรียน</h4>
              <p>พบกับการแข่งขันกีฬาสีประจำปี วันที่ 25 กันยายน 2568</p>
            </li>
          </ul>
          <form id="news-form" class="mt-6 flex flex-col space-y-3" onsubmit="addNews(event)">
            <input id="news-title" type="text" placeholder="หัวข้อข่าว" required class="p-3 border border-gray-300 rounded focus:ring-2 focus:ring-blue-600" />
            <textarea id="news-content" placeholder="รายละเอียดข่าว" required class="p-3 border border-gray-300 rounded focus:ring-2 focus:ring-blue-600 resize-y min-h-[80px]"></textarea>
            <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white py-3 rounded font-semibold transition">เพิ่มข่าวสาร</button>
          </form>
        </section>

        <!-- Profile -->
        <section id="tab-profile-content" class="hidden max-w-md mx-auto text-center">
          <h3 class="text-xl font-semibold mb-6">โปรไฟล์ของฉัน</h3>
          <div class="mb-6">
            <img id="profile-avatar" src="https://wnc.prachin1.go.th/images/logo.png" alt="โปรไฟล์" class="mx-auto rounded-full w-28 h-28 border-4 border-blue-600" />
          </div>
          <p class="text-lg mb-4">ชื่อผู้ใช้: <span id="profile-username"></span></p>
          <button onclick="logout()" class="bg-red-600 hover:bg-red-700 text-white px-6 py-2 rounded font-semibold transition">
            ออกจากระบบ
          </button>
        </section>

      </div>
    </section>

  </main>

  <!-- Footer -->
  <footer class="bg-blue-800 text-white py-4 text-center mt-auto">
    <p>© 2025 โรงเรียนชุมชนวัดหนองจวง - สงวนลิขสิทธิ์</p>
  </footer>

<script>
  // สลับฟอร์ม Login/Register
  function toggleAuth(mode) {
    document.getElementById('login-form').classList.toggle('hidden', mode !== 'login');
    document.getElementById('register-form').classList.toggle('hidden', mode !== 'register');
  }

  // แสดง loading บนปุ่ม พร้อมโลโก้หมุน
  function toggleLoading(button, loading, label, withSuccess = false) {
    if (loading) {
      button.disabled = true;
      button.innerHTML = `<img src="https://wnc.prachin1.go.th/images/logo.png" class="spin-logo" alt="โลโก้โรงเรียนหมุน"> ${label}`;
    } else {
      button.disabled = false;
      button.innerHTML = label;
      if (withSuccess) alert('✅ คุณได้แล้ว!');
    }
  }

  // ดึงข้อมูลผู้ใช้จาก localStorage
  function getUsers() {
    const data = localStorage.getItem('users');
    return data ? JSON.parse(data) : [];
  }

  // บันทึกผู้ใช้ลง localStorage
  function saveUsers(users) {
    localStorage.setItem('users', JSON.stringify(users));
  }

  // ฟังก์ชันเข้าสู่ระบบ
  function handleLogin(e) {
    e.preventDefault();
    const btn = e.target.querySelector('button[type="submit"]');
    toggleLoading(btn, true, 'กำลังเข้าสู่ระบบ...');
    const username = document.getElementById('username').value.trim();
    const password = document.getElementById('password').value;

    setTimeout(() => {
      const users = getUsers();
      const user = users.find(u => u.username === username && u.password === password);
      toggleLoading(btn, false, 'เข้าสู่ระบบ', user !== undefined);
      if (user) {
        localStorage.setItem('currentUser', username);
        showDashboard(username);
      } else {
        alert('ชื่อผู้ใช้หรือรหัสผ่านไม่ถูกต้อง');
      }
    }, 1000);
  }

  // ฟังก์ชันสมัครสมาชิก
  function handleRegister(e) {
    e.preventDefault();
    const btn = e.target.querySelector('button[type="submit"]');
    toggleLoading(btn, true, 'กำลังสมัครสมาชิก...');
    const username = document.getElementById('reg-username').value.trim();
    const password = document.getElementById('reg-password').value;

    setTimeout(() => {
      let users = getUsers();
      if (users.some(u => u.username === username)) {
        toggleLoading(btn, false, 'สมัครสมาชิก');
        alert('ชื่อผู้ใช้นี้ถูกใช้ไปแล้ว กรุณาใช้ชื่ออื่น');
        return;
      }
      users.push({ username, password });
      saveUsers(users);
      toggleLoading(btn, false, 'สมัครสมาชิก', true);
      alert('สมัครสมาชิกสำเร็จ! คุณสามารถเข้าสู่ระบบได้ทันที');
      e.target.reset();
      toggleAuth('login');
    }, 1000);
  }

  // แสดงแดชบอร์ดหลังล็อกอิน
  function showDashboard(username) {
    document.getElementById('auth-section').classList.add('hidden');
    const dash = document.getElementById('dashboard');
    dash.classList.remove('hidden');
    document.getElementById('display-user').innerText = username;
    // โหลดข้อมูลโปรไฟล์
    document.getElementById('profile-username').innerText = username;
  }

  // ฟังก์ชันออกจากระบบ
  function logout() {
    localStorage.removeItem('currentUser');
    document.getElementById('dashboard').classList.add('hidden');
    document.getElementById('auth-section').classList.remove('hidden');
    toggleAuth('login');
  }

  // Tab navigation
  function showTab(tab) {
    const tabs = ['students', 'news', 'profile'];
    tabs.forEach(t => {
      document.getElementById(`tab-${t}-content`).classList.toggle('hidden', t !== tab);
      document.getElementById(`tab-${t}`).classList.toggle('border-blue-600', t === tab);
      document.getElementById(`tab-${t}`).classList.toggle('text-blue-600', t === tab);
      document.getElementById(`tab-${t}`).classList.toggle('hover:text-blue-600', t !== tab);
    });
  }

  // เก็บข้อมูลนักเรียนใน localStorage
  function getStudents() {
    const data = localStorage.getItem('students');
    return data ? JSON.parse(data) : [];
  }

  function saveStudents(students) {
    localStorage.setItem('students', JSON.stringify(students));
  }

  // อัพเดต renderStudents ให้แสดงข้อมูลคะแนน, ห้อง และชั้นเรียน
  function renderStudents() {
    const students = getStudents();
    const container = document.getElementById('student-list');
    if (students.length === 0) {
      container.innerHTML = '<p class="text-gray-500 text-center">ยังไม่มีข้อมูลนักเรียน</p>';
      document.getElementById('score-summary').innerHTML = '';
      return;
    }
    container.innerHTML = '';
    students.forEach((s, i) => {
      const div = document.createElement('div');
      div.className = 'flex justify-between items-center p-3 bg-gray-50 rounded shadow-sm flex-wrap';
      div.innerHTML = `
        <div class="flex-grow min-w-[220px]"><strong>${s.name}</strong> | ชั้น: ${s.grade} | ห้อง: ${s.room} | คะแนน: ${s.score}</div>
        <button class="text-red-600 font-semibold hover:underline whitespace-nowrap" onclick="removeStudent(${i})">ลบ</button>
      `;
      container.appendChild(div);
    });
    renderScoreSummary();
  }

  // ลบนักเรียน
  function removeStudent(index) {
    let students = getStudents();
    if (index >= 0 && index < students.length) {
      if (confirm(`ลบนักเรียน: ${students[index].name} ?`)) {
        students.splice(index, 1);
        saveStudents(students);
        renderStudents();
      }
    }
  }

  // ฟังก์ชันสรุปคะแนนรวมแยกตามชั้นและห้อง
  function renderScoreSummary() {
    const students = getStudents();
    if (students.length === 0) {
      document.getElementById('score-summary').innerHTML = '<p class="text-gray-500 text-center">ยังไม่มีข้อมูลคะแนน</p>';
      return;
    }

    // รวมคะแนนรายชั้นและห้อง
    // โครงสร้าง { 'ป.1': { '1': [คะแนน], '2': [...], ... }, 'ม.3': { '1': [...], ... } }
    const summary = {};

    students.forEach(s => {
      if (!summary[s.grade]) summary[s.grade] = {};
      if (!summary[s.grade][s.room]) summary[s.grade][s.room] = [];
      summary[s.grade][s.room].push(s.score);
    });

    // สร้าง HTML สรุปคะแนน
    let html = '';
    for (const grade in summary) {
      html += `<div class="border border-gray-300 rounded p-4 bg-white shadow">`;
      html += `<h4 class="text-lg font-semibold mb-2">ชั้น ${grade}</h4>`;
      for (const room in summary[grade]) {
        const scores = summary[grade][room];
        const avg = (scores.reduce((a,b) => a + b, 0) / scores.length).toFixed(2);
        html += `<p>ห้อง ${room} &nbsp;&nbsp; คะแนนเฉลี่ย: <span class="font-bold text-blue-600">${avg}</span> (นักเรียน ${scores.length} คน)</p>`;
      }
      html += `</div>`;
    }
    document.getElementById('score-summary').innerHTML = html;
  }

  // ปุ่มเพิ่มนักเรียน ให้เก็บข้อมูลห้องและคะแนนด้วย
  function addStudent(e) {
    e.preventDefault();
    const name = document.getElementById('student-name').value.trim();
    const grade = document.getElementById('student-grade').value;
    const room = document.getElementById('student-room').value;
    const score = parseFloat(document.getElementById('student-score').value);
    if (!name || !grade || !room || isNaN(score) || score < 0 || score > 100) {
      alert('กรุณากรอกข้อมูลให้ถูกต้องครบถ้วน (คะแนน 0-100)');
      return;
    }
    let students = getStudents();
    students.push({ name, grade, room, score });
    saveStudents(students);
    e.target.reset();
    renderStudents();
  }

  // ฟังก์ชันเพิ่มข่าวสารใหม่
  function getNews() {
    const data = localStorage.getItem('news');
    return data ? JSON.parse(data) : [];
  }
  function saveNews(news) {
    localStorage.setItem('news', JSON.stringify(news));
  }
  function renderNews() {
    const news = getNews();
    const container = document.getElementById('news-list');
    if (news.length === 0) {
      container.innerHTML = '<p class="text-gray-500 text-center">ยังไม่มีข่าวสาร</p>';
      return;
    }
    container.innerHTML = '';
    news.forEach(item => {
      const li = document.createElement('li');
      li.className = 'bg-gray-100 rounded p-4 shadow-sm';
      li.innerHTML = `<h4 class="font-bold text-lg">${item.title}</h4><p>${item.content}</p>`;
      container.appendChild(li);
    });
  }
  function addNews(e) {
    e.preventDefault();
    const title = document.getElementById('news-title').value.trim();
    const content = document.getElementById('news-content').value.trim();
    if (!title || !content) {
      alert('กรุณากรอกหัวข้อและรายละเอียดข่าวสารให้ครบถ้วน');
      return;
    }
    const news = getNews();
    news.unshift({ title, content });
    saveNews(news);
    e.target.reset();
    renderNews();
    alert('เพิ่มข่าวสารสำเร็จ');
  }

  // แสดงเวลาเรียลไทม์
  function updateClock() {
    const clock = document.getElementById('clock');
    if (!clock) return;
    const now = new Date();
    const options = { hour12: false, hour: '2-digit', minute: '2-digit', second: '2-digit' };
    clock.textContent = now.toLocaleTimeString('th-TH', options);
  }
  setInterval(updateClock, 1000);
  updateClock();

  // เช็คสถานะล็อกอินอัตโนมัติเมื่อโหลดหน้าเว็บ
  window.onload = () => {
    const currentUser = localStorage.getItem('currentUser');
    if (currentUser) {
      showDashboard(currentUser);
      showTab('students');
      renderStudents();
      renderNews();
    } else {
      toggleAuth('login');
    }
  };
</script>

</body>
</html>
