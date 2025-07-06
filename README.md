<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Student Management</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      background: #f0f0f0;
    }
    h1, h2 {
      text-align: center;
    }
    .controls, .add-form {
      display: flex;
      justify-content: center;
      gap: 10px;
      margin: 10px auto;
      flex-wrap: wrap;
    }
    input, select, button {
      padding: 8px;
      font-size: 16px;
    }
    .student-card {
      background: #fff;
      margin: 10px auto;
      padding: 15px;
      max-width: 600px;
      border-radius: 10px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      display: flex;
      align-items: center;
      gap: 15px;
    }
    .student-card img {
      width: 80px;
      height: 80px;
      border-radius: 50%;
      object-fit: cover;
    }
    .student-info {
      flex: 1;
    }
    .fee-status.paid { color: green; }
    .fee-status.due { color: red; }
    button.edit-btn { background: #f39c12; color: white; }
    button.msg-btn { background: #3498db; color: white; }
  </style>
</head>
<body>
  <h1>Student Management System</h1>

  <div class="controls">
    <select id="classSelect">
      <option value="">Select Class</option>
      <option value="Class 1">Class 1</option>
      <option value="Class 2">Class 2</option>
      <option value="Class 3">Class 3</option>
      <option value="Class 4">Class 4</option>
      <option value="Class 5">Class 5</option>
      <option value="Class 6">Class 6</option>
      <option value="Class 7">Class 7</option>
      <option value="Class 8">Class 8</option>
      <option value="Class 9">Class 9</option>
      <option value="Class 10">Class 10</option>
      <option value="Class 11">Class 11</option>
      <option value="Class 12">Class 12</option>
    </select>
    <input type="text" id="searchInput" placeholder="Search by name or roll no">
    <button onclick="renderStudents()">Search</button>
  </div>

  <h2>Add / Edit Student</h2>
  <div class="add-form">
    <input id="name" placeholder="Name" />
    <input id="roll" placeholder="Roll No" />
    <select id="className">
      <option value="">Class</option>
      <option value="Class 1">Class 1</option>
      <option value="Class 2">Class 2</option>
      <option value="Class 3">Class 3</option>
      <option value="Class 4">Class 4</option>
      <option value="Class 5">Class 5</option>
      <option value="Class 6">Class 6</option>
      <option value="Class 7">Class 7</option>
      <option value="Class 8">Class 8</option>
      <option value="Class 9">Class 9</option>
      <option value="Class 10">Class 10</option>
      <option value="Class 11">Class 11</option>
      <option value="Class 12">Class 12</option>
    </select>
    <input type="file" id="photoFile" accept="image/*" onchange="previewPhoto()" />
    <img id="photoPreview" src="" style="width: 80px; height: 80px; border-radius: 50%; object-fit: cover;" />
    <input id="monthsPaid" type="number" placeholder="Months Paid" />
    <input id="totalMonths" type="number" placeholder="Total Months" />
    <button onclick="addOrUpdateStudent()">Save</button>
  </div>

  <div id="studentContainer"></div>

  <script>
    let students = [
      {
        name: "Aarav Mehta",
        roll: "101",
        class: "Class 1",
        photo: "https://randomuser.me/api/portraits/men/10.jpg",
        monthsPaid: 4,
        totalMonths: 12
      }
    ];

    let editIndex = null;

    function renderStudents() {
      const selectedClass = document.getElementById("classSelect").value;
      const query = document.getElementById("searchInput").value.toLowerCase();
      const container = document.getElementById("studentContainer");
      container.innerHTML = "";

      const filtered = students.filter(s => {
        const matchClass = selectedClass ? s.class === selectedClass : true;
        const matchSearch = s.name.toLowerCase().includes(query) || s.roll.includes(query);
        return matchClass && matchSearch;
      });

      filtered.forEach((s, i) => {
        const feeStatus = s.monthsPaid >= s.totalMonths ? "paid" : "due";
        const balance = s.totalMonths - s.monthsPaid;

        const card = document.createElement("div");
        card.className = "student-card";
        card.innerHTML = `
          <img src="${s.photo}" />
          <div class="student-info">
            <h3>${s.name} (Roll: ${s.roll})</h3>
            <p>Class: ${s.class}</p>
            <p class="fee-status ${feeStatus}">Fees: ${s.monthsPaid}/${s.totalMonths} months</p>
            <p class="fee-status ${feeStatus}">${balance > 0 ? balance + " Months Due" : "Fully Paid"}</p>
            <button class="msg-btn" onclick="sendMessage('${s.name}', ${balance})">Send Msg</button>
            <button class="edit-btn" onclick="editStudent(${i})">Edit</button>
          </div>
        `;
        container.appendChild(card);
      });
    }

    function sendMessage(name, due) {
      if (due === 0) {
        alert(`✅ Message: ${name} has paid full fees.`);
      } else {
        alert(`📩 Message sent to ${name}'s guardian: ${due} month(s) fee due.`);
      }
    }

    function previewPhoto() {
      const file = document.getElementById("photoFile").files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(e) {
          document.getElementById("photoPreview").src = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    }

    function addOrUpdateStudent() {
      const name = document.getElementById("name").value;
      const roll = document.getElementById("roll").value;
      const className = document.getElementById("className").value;
      const monthsPaid = parseInt(document.getElementById("monthsPaid").value);
      const totalMonths = parseInt(document.getElementById("totalMonths").value);
      const photoSrc = document.getElementById("photoPreview").src || "https://via.placeholder.com/80";

      const newStudent = {
        name,
        roll,
        class: className,
        photo: photoSrc,
        monthsPaid,
        totalMonths
      };

      if (editIndex !== null) {
        students[editIndex] = newStudent;
        editIndex = null;
      } else {
        students.push(newStudent);
      }

      // Clear form
      document.getElementById("name").value = "";
      document.getElementById("roll").value = "";
      document.getElementById("className").value = "";
      document.getElementById("photoFile").value = "";
      document.getElementById("monthsPaid").value = "";
      document.getElementById("totalMonths").value = "";
      document.getElementById("photoPreview").src = "";

      renderStudents();
    }

    function editStudent(index) {
      const s = students[index];
      document.getElementById("name").value = s.name;
      document.getElementById("roll").value = s.roll;
      document.getElementById("className").value = s.class;
      document.getElementById("monthsPaid").value = s.monthsPaid;
      document.getElementById("totalMonths").value = s.totalMonths;
      document.getElementById("photoPreview").src = s.photo;
      editIndex = index;
    }

    renderStudents();
  </script>
</body>
</html>
