<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8" />
  <title>نظام إدارة الموظفين - IndexedDB</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: #f0f4f8;
      padding: 20px;
      direction: rtl;
      color: #333;
    }
    h2 {
      text-align: center;
      color: #2c3e50;
      margin-bottom: 30px;
    }
    .section {
      background: #ffffff;
      padding: 20px 25px;
      margin-bottom: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }
    .form-row {
      display: flex;
      flex-wrap: wrap;
      gap: 15px;
      align-items: center;
      justify-content: flex-start;
    }
    .form-group {
      flex: 1 1 180px;
      min-width: 150px;
      display: flex;
      flex-direction: column;
    }
    label {
      font-weight: 600;
      margin-bottom: 6px;
      color: #34495e;
      user-select: none;
    }
    input[type="text"],
    input[type="date"],
    select {
      padding: 10px 12px;
      border: 1.8px solid #d1dce5;
      border-radius: 8px;
      font-size: 16px;
      transition: border-color 0.3s ease;
    }
    input[type="text"]:focus,
    input[type="date"]:focus,
    select:focus {
      border-color: #3498db;
      outline: none;
      box-shadow: 0 0 6px #a8d0f0;
    }
    button {
      padding: 12px 20px;
      background-color: #3498db;
      border: none;
      border-radius: 8px;
      color: white;
      font-weight: 700;
      cursor: pointer;
      transition: background-color 0.3s ease;
      min-width: 140px;
      user-select: none;
    }
    button:hover {
      background-color: #2c80c9;
    }
    .table-container {
      max-height: 290px; /* ارتفاع الحاوية للتمرير */
      overflow-y: auto;
      border-radius: 8px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      margin-top: 15px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
      font-size: 14px;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
      /* تمت إزالة overflow:hidden من هنا */
      border-radius: 0; /* لا تعطي border-radius هنا */
    }
    thead tr {
      background-color: #2980b9;
      color: white;
      user-select: none;
    }
    thead th {
      position: sticky;
      top: 0;
      background-color: #2980b9;
      z-index: 10;
    }
    th, td {
      padding: 12px 8px;
      text-align: center;
      border-bottom: 1px solid #e1e9f0;
      vertical-align: middle;
    }
    tbody td {
      font-size: 18px;
    }
    tbody tr:hover {
      background-color: #eaf4fb;
    }
    tbody tr:last-child td {
      border-bottom: none;
    }
.btn-small {
  padding: 2px 8px;
  font-size: 12px;
  border-radius: 4px;
  user-select: none;
  border: none;
  cursor: pointer;
  margin: 2px 2px;
  min-width: 75px;
  height: 28px;
  line-height: 28px; /* نفس ارتفاع الزر */
  box-sizing: border-box;
}


<pre><code>.btn-edit {
  background-color: #27ae60;
  color: white;
}
.btn-edit:hover {
  background-color: #1e8449;
}
.btn-delete {
  background-color: #e74c3c;
  color: white;
}
.btn-delete:hover {
  background-color: #c0392b;
}
.btn-details {
  background-color: #f39c12;
  color: white;
}
.btn-details:hover {
  background-color: #d68910;
}
.btn-print {
  background-color: #8e44ad;
  color: white;
}
.btn-print:hover {
  background-color: #732d91;
}
@media (max-width: 720px) {
  .form-row {
    flex-direction: column;
  }
  .form-group {
    width: 100%;
  }
  button {
    width: 100%;
    min-width: auto;
  }
  table, thead, tbody, th, td, tr {
    display: block;
  }
  thead tr {
    position: absolute;
    top: -9999px;
    left: -9999px;
  }
  tr {
    margin-bottom: 15px;
  }
  td {
    border: none;
    position: relative;
    padding-left: 50%;
    text-align: right;
  }
  td:before {
    position: absolute;
    top: 12px;
    left: 10px;
    width: 45%;
    white-space: nowrap;
    font-weight: 600;
    color: #555;
  }
  td:nth-of-type(1):before { content: &quot;الرقم&quot;; }
  td:nth-of-type(2):before { content: &quot;الاسم&quot;; }
  td:nth-of-type(3):before { content: &quot;الجنس&quot;; }
  td:nth-of-type(4):before { content: &quot;المدينة&quot;; }
  td:nth-of-type(5):before { content: &quot;تاريخ الميلاد&quot;; }
  td:nth-of-type(6):before { content: &quot;اليوم&quot;; }
  td:nth-of-type(7):before { content: &quot;الوقت&quot;; }
  td:nth-of-type(8):before { content: &quot;تاريخ التسجيل&quot;; }
  td:nth-of-type(9):before { content: &quot;تحكم&quot;; }
}
/* نافذة تعديل الموظف */
#editModal {
  display: none;
  position: fixed;
  z-index: 1000;
  left: 0; top: 0;
  width: 100%; height: 100%;
  background: rgba(0,0,0,0.5);
}
#editModalContent {
  background: #fff;
  width: 350px;
  margin: 100px auto;
  padding: 20px;
  border-radius: 10px;
  position: relative;
  box-shadow: 0 4px 10px rgba(0,0,0,0.3);
}
#editModalContent span.closeBtn {
  position: absolute;
  top: 10px; left: 15px;
  font-size: 22px;
  cursor: pointer;
}
#editModalContent h3 {
  text-align: center;
  margin-bottom: 20px;
}
#editModalContent form input,
#editModalContent form select {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
  font-size: 15px;
  border-radius: 6px;
  border: 1.5px solid #d1dce5;
  box-sizing: border-box;
}
#editModalContent form button {
  width: 100%;
  padding: 10px;
  background: #27ae60;
  color: #fff;
  font-weight: bold;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
}
#editModalContent form button:hover {
  background: #1e8449;
}
#successMessage {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #27ae60;
  color: white;
  padding: 12px 20px;
  border-radius: 10px;
  font-size: 18px;
  font-weight: bold;
  z-index: 9999;
  box-shadow: 0 4px 10px rgba(0,0,0,0.2);
  transition: opacity 0.4s ease;
  text-align: center;
}
#matchingNames li {
  padding: 8px 12px;
  border-bottom: 1px solid #eee;
}

#matchingNames li:hover {
  background-color: #3498db;
  color: white;
}

<p>  </style></p>
</head>
<body>


<!-- نافذة تسجيل الدخول الأنيقة -->
<div id="loginModal" style="position:fixed; z-index:9999; top:0; left:0; width:100%; height:100%; background:linear-gradient(to bottom, #2c3e50, #34495e); display:flex; justify-content:center; align-items:center; font-family:'Arial', sans-serif;">
  <div style="background:#fff; padding:30px; border-radius:20px; box-shadow:0 0 30px rgba(0,0,0,0.4); width:100%; max-width:350px; text-align:center;">
    <h2 style="margin-bottom:25px; color:#2c3e50;">🔐 تسجيل الدخول</h2>
    
    <div style="display:flex; flex-direction:column; align-items:center;">
<input type="text" id="loginUsername" placeholder="👤 اسم المستخدم" 
       style="width:90%; padding:12px; margin-bottom:15px; border-radius:10px; border:1px solid #ccc; font-size:16px;" />

      
      <input type="password" id="loginPassword" placeholder="🔑 كلمة المرور" 
             style="width:90%; padding:12px; margin-bottom:8px; border-radius:10px; border:1px solid #ccc; font-size:16px;" />
      
      <!-- مربع إظهار كلمة المرور -->
      <label style="width:90%; text-align:right; font-size:14px; color:#555; margin-bottom:15px;">
        <input type="checkbox" onclick="togglePasswordCheckbox()" style="margin-left:5px;" />
        إظهار كلمة المرور
      </label>
      
      <button onclick="checkLogin()" 
              style="width:90%; padding:12px; background:#2980b9; color:white; border:none; border-radius:10px; font-size:16px; font-weight:bold; cursor:pointer;">
        دخول
      </button>
      
      <p id="loginError" style="color:#e74c3c; margin-top:15px; display:none; font-weight:bold;">❌ اسم المستخدم أو كلمة المرور غير صحيحة</p>
    </div>
  </div>
</div>

<!-- تركيز تلقائي على خانة اسم المستخدم عند فتح الصفحة -->

<script>
  window.addEventListener("load", () => {
    setTimeout(() => {
      const input = document.getElementById("loginUsername");
      if (input) input.focus();
    }, 300); // تأخير بسيط بعد تحميل كل العناصر
  });
</script>











<!-- نافذة الإحصائيات الأنيقة -->
<div id="statisticsModal" style="display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:#000000aa; justify-content:center; align-items:center; z-index:9999;">
  <div style="background:#fff; padding:30px; border-radius:12px; box-shadow:0 0 15px #000; max-width:700px; width:90%; max-height:90vh; overflow-y:auto; font-family:'Segoe UI'; position:relative; text-align:center;">
    <h2 style="margin-bottom:20px;">📊 إحصائيات الموظفين</h2>
    <p>إجمالي الموظفين: <strong id="stat-total">0</strong></p>
    <p>عدد الذكور: <strong id="stat-male">0</strong></p>
    <p>عدد الإناث: <strong id="stat-female">0</strong></p>

    <div id="cityStats" style="
      margin-top: 20px;
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
      gap: 12px;
      justify-items: center;
    ">
      <!-- أزرار المحافظات ستُضاف هنا -->
    </div>

    <button onclick="document.getElementById('statisticsModal').style.display='none'" 
      style="margin-top:20px; padding:10px 20px; border:none; background:#007bff; color:white; border-radius:8px; cursor:pointer;">
      إغلاق
    </button>
  </div>
</div>





<div id="settingsModal" style="display:none; position: fixed; z-index: 2000; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.5);">
  <div style="background: white; max-width: 400px; margin: 100px auto; padding: 20px; border-radius: 10px; text-align: center; position: relative;">
    <span onclick="closeSettingsModal()" 
          style="position: absolute; top: 10px; left: 15px; font-size: 24px; cursor: pointer;">&times;</span>
    <h3>إعدادات النظام</h3>
    <button onclick="showStatistics()" style="margin:10px; padding: 10px 15px;">📊 إحصائيات</button>
    <button onclick="exportData()" style="margin:10px; padding: 10px 15px;">⬇️ تصدير بيانات</button>
    <button onclick="importData()" style="margin:10px; padding: 10px 15px;">⬆️ استيراد بيانات</button>
    <button onclick="logout()" style="margin:10px; padding: 10px 15px; background-color: #e74c3c; color: white;">🚪 تسجيل خروج</button>
  </div>
</div>









<div id="successMessage" style="display:none;"></div>

  <div class="section">
    <div class="form-row">
      <div class="form-group">
        <label for="name"></label>
        <input type="text" id="name" placeholder="ادخل الاسم" />
      </div>
      <div class="form-group">
        <label for="gender"></label>
        <select id="gender">
          <option value="">جنس</option>
          <option value="ذكر">ذكر</option>
          <option value="أنثى">أنثى</option>
        </select>
      </div>
 <div class="form-group">
  <label for="city"></label>
  <select id="city">
<option value="">اختر المدينة</option>
  <option value="بغداد">بغداد</option>
  <option value="البصرة">البصرة</option>
  <option value="النجف">النجف</option>
  <option value="كربلاء">كربلاء</option>
  <option value="الناصرية">الناصرية</option>
  <option value="السليمانية">السليمانية</option>
  <option value="أربيل">أربيل</option>
  <option value="دهوك">دهوك</option>
  <option value="صلاح الدين">صلاح الدين</option>
  <option value="الأنبار">الأنبار</option>
  <option value="كركوك">كركوك</option>
  <option value="ديالى">ديالى</option>
  <option value="ميسان">ميسان</option>
  <option value="واسط">واسط</option>
  <option value="ذي قار">ذي قار</option>
  <option value="حلبجة">حلبجة</option>
  <option value="نينوى">نينوى</option>
  <option value="بابل">بابل</option>
  <option value="المثنى">المثنى</option>
</select>
</div>

      <div class="form-group">
        <label for="birth"></label>
        <input type="date" id="birth" />
      </div>
      <div class="form-group" style="align-self: flex-end;">
      <button id="addEmployee" onclick="addEmployee()">إضافة الموظف</button>

      </div>
    </div>
  </div>

  <div class="section">
    <h3></h3>
    <div class="form-row">
<div style="position: relative; display: inline-block;">
<input
  type="text"
  id="searchName"
  oninput="onSearchInput()"
  placeholder="اكتب بداية الاسم للبحث"
  style="padding-left: 0px; height: 44px; box-sizing: border-box; width: 250px; margin-top: 4px;"
/>
  <span
    id="clearSearch"
    onclick="clearSearchField()"
    style="
      position: absolute;
      left: 15px;
      top: 40%;
      transform: translateY(-50%);
      cursor: pointer;
      font-weight: bold;
      font-size: 18px;
      color: #888;
      user-select: none;
      display: none;
    "
    title="مسح"
  >
   X
  </span>
</div>


      <div class="form-group">
        <label for="filterGender"></label>
        <select id="filterGender" onchange="filterAndShow()">
          <option value=""> الجنس المسجلة </option>
          <option value="ذكر">ذكر</option>
          <option value="أنثى">أنثى</option>
        </select>
      </div>

      <div class="form-group">
        <label for="filterCity"></label>
        <select id="filterCity" onchange="filterAndShow()">
          <option value="">المدن المسجلة</option>
          <option value="أربيل">أربيل</option>
          <option value="دهوك">دهوك</option>
        </select>
      </div>

      <div class="form-group">
        <label for="filterBirthSelect"></label>
        <select id="filterBirthSelect" onchange="filterAndShow()">
          <option value="">اختر تاريخ الميلاد</option>
        </select>
      </div>
    </div>
  </div>

<div id="matchingNamesList" style="position: relative;">
  <ul id="matchingNames" style="
    list-style: none;
    margin: 0; padding: 0;
    background: white;
    border: 1px solid #ccc;
    max-height: 150px;
    overflow-y: auto;
    width: 100%;
    box-sizing: border-box;
    cursor: pointer;
    display: none;
    position: absolute;
    z-index: 1000;
  ">
  </ul>
</div>




<div style="display: flex; align-items: center; justify-content: center; margin-top: 10px; gap: 20px;">

  <!-- زر على اليمين -->
  <button id="openSettingsBtn" style="padding: 10px 20px; font-weight: bold;">
    ⚙️ إعدادات النظام
  </button>


  <!-- النص في الوسط مع دفعه يمين شوي -->
  <div id="countDisplay" style="font-weight: bold; color: #2980b9; text-align: center; margin-left: 40px; min-width: 150px;">
    مجموع الموظفين: 0 <br/>
    من بعث: 0
  </div>

</div>








</div>


<div id="countDisplay" style="margin-top: 10px; font-weight: bold;"></div>

</div>
  <div class="table-container">
    <table id="employeeTable">
      <thead>
        <tr>
          <th>الرقم</th>
          <th>الاسم</th>
          <th>الجنس</th>
          <th>المدينة</th>
          <th>تاريخ الميلاد</th>
          <th>اليوم</th>
          <th>الوقت</th>
          <th>تاريخ التسجيل</th>
          <th>تحكم</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </div>

<!-- مودال عرض تفاصيل الموظف -->
<div id="detailsModal" style="display:none; position: fixed; z-index: 1000; left: 0; top: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5);">
  <div style="background: white; width: 350px; margin: 100px auto; padding: 20px; border-radius: 10px; position: relative; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;">
    <span style="position: absolute; top: 10px; left: 15px; font-size: 22px; cursor: pointer;" onclick="closeDetailsModal()">&times;</span>
    <div id="detailsContent"></div>
    <button onclick="printCurrentDetails()" style="margin-top: 15px; width: 100%; padding: 10px; font-weight: bold; background-color: #3498db; color: white; border: none; border-radius: 6px; cursor: pointer;">🖨️ طباعة</button>
  </div>
</div>





  <!-- نافذة تعديل الموظف -->
  <div id="editModal">
    <div id="editModalContent">
      <span class="closeBtn" onclick="closeEditModal()">&times;</span>
      <h3>تعديل بيانات الموظف</h3>
      <form id="editForm" onsubmit="saveEdit(event)">
        <input type="text" id="editName" required placeholder="الاسم"/>
        <select id="editGender" required>
          <option value="ذكر">ذكر</option>
          <option value="أنثى">أنثى</option>
        </select>
<select id="editCity" required>
  <option value="">اختر المدينة</option>
  <option value="بغداد">بغداد</option>
  <option value="البصرة">البصرة</option>
  <option value="النجف">النجف</option>
  <option value="كربلاء">كربلاء</option>
  <option value="الناصرية">الناصرية</option>
  <option value="السليمانية">السليمانية</option>
  <option value="أربيل">أربيل</option>
  <option value="دهوك">دهوك</option>
  <option value="صلاح الدين">صلاح الدين</option>
  <option value="الأنبار">الأنبار</option>
  <option value="كركوك">كركوك</option>
  <option value="ديالى">ديالى</option>
  <option value="ميسان">ميسان</option>
  <option value="واسط">واسط</option>
  <option value="ذي قار">ذي قار</option>
  <option value="حلبجة">حلبجة</option>
  <option value="نينوى">نينوى</option>
  <option value="بابل">بابل</option>
  <option value="المثنى">المثنى</option>
</select>
        <input type="date" id="editBirth" required/>
        <button type="submit">حفظ التعديل</button>
      </form>
    </div>
  </div>

<script>
  let db;
  const request = indexedDB.open("EmployeeDB", 1);

  let editingId = null;

  request.onupgradeneeded = function(e) {
    db = e.target.result;
    if (!db.objectStoreNames.contains("employees")) {
      db.createObjectStore("employees", { keyPath: "id", autoIncrement: true });
    }
  };

  request.onsuccess = function(e) {
    db = e.target.result;
    updateBirthDatesDropdown();
    filterAndShow();
  };


function addEmployee() {
  const name = document.getElementById("name").value.trim();
  const gender = document.getElementById("gender").value;
  const city = document.getElementById("city").value;
  const birth = document.getElementById("birth").value;

  if (!name || !birth) {
    alert("يرجى ملء جميع الحقول.");
    return;
  }

  const now = new Date();
  const weekday = now.toLocaleDateString("ar-EG", { weekday: "long" });
  const time = now.toLocaleTimeString("ar-EG");
const year = now.getFullYear();
const month = (now.getMonth() + 1).toString().padStart(2, '0');
const day = now.getDate().toString().padStart(2, '0');
const regDate = `${year}-${month}-${day}`; // التاريخ بصيغة محلية صحيحة


  const tx = db.transaction("employees", "readwrite");
  const store = tx.objectStore("employees");

  const employee = {
    name,
    gender,
    city,
    birth,
    weekday,
    time,
    regDate
  };

  const requestAdd = store.add(employee);

  requestAdd.onsuccess = (event) => {
    const newId = event.target.result;
    showSuccessMessage("تم تسجيل الموظف " + name + " بنجاح.");
    updateBirthDatesDropdown();
    filterAndShow();
    clearForm();
    printA5(newId); // ← طباعة بعد التسجيل
  };

  requestAdd.onerror = (e) => {
    alert("حدث خطأ أثناء إضافة الموظف. ربما الاسم مكرر أو هناك مشكلة.");
    console.error("Add Error:", e.target.error);
  };
}



 


  function clearForm() {
    document.getElementById("name").value = "";
    document.getElementById("gender").value = "";
    document.getElementById("city").value = "";
    document.getElementById("birth").value = "";
  }

  function renderRow(emp) {
    const tbody = document.querySelector("#employeeTable tbody");
    const row = document.createElement("tr");

    row.innerHTML = `
      <td>${emp.id}</td>
      <td>${emp.name}</td>
      <td>${emp.gender}</td>
      <td>${emp.city}</td>
      <td>${emp.birth}</td>
      <td>${emp.weekday}</td>
      <td>${emp.time}</td>
      <td>${emp.regDate}</td>
      <td>
        <button class="btn-small btn-edit" onclick="editEmployee(${emp.id})">تعديل</button>
        <button class="btn-small btn-delete" onclick="deleteEmployee(${emp.id})">حذف</button>
        <button class="btn-small btn-details" onclick="showDetails(${emp.id})">معلومات</button>
        <button class="btn-small btn-print" onclick="printA5(${emp.id})">طباعة</button>
      </td>
    `;
    tbody.appendChild(row);
  }

 function deleteEmployee(id) {
  const tx = db.transaction("employees", "readonly");
  const store = tx.objectStore("employees");
  const request = store.get(id);
  request.onsuccess = () => {
    const emp = request.result;
    if (!emp) {
      alert("الموظف غير موجود!");
      return;
    }
    const confirmMsg = `هل تريد حذف الموظف: ${emp.name} ؟`;
    if (confirm(confirmMsg)) {
      const txDelete = db.transaction("employees", "readwrite");
      const storeDelete = txDelete.objectStore("employees");
      storeDelete.delete(id);
      txDelete.oncomplete = () => {
        updateBirthDatesDropdown();
        filterAndShow();
      };
    }
  };
  request.onerror = () => {
    alert("خطأ في جلب بيانات الموظف للحذف.");
  };
}


  function editEmployee(id) {
    const tx = db.transaction("employees", "readonly");
    const store = tx.objectStore("employees");
    const request = store.get(id);
    request.onsuccess = () => {
      const emp = request.result;
      document.getElementById("editName").value = emp.name;
      document.getElementById("editGender").value = emp.gender;
      document.getElementById("editCity").value = emp.city;
      document.getElementById("editBirth").value = emp.birth;
      editingId = emp.id;
      document.getElementById("editModal").style.display = "block";
    };
  }

  function closeEditModal() {
    document.getElementById("editModal").style.display = "none";
    editingId = null;
  }







  function showDetails(id) {
    const tx = db.transaction("employees", "readonly");
    const store = tx.objectStore("employees");
    const request = store.get(id);
    request.onsuccess = () => {
      const emp = request.result;
      alert(
        `معلومات الموظف:\n\n` +
        `الاسم: ${emp.name}\n` +
        `الجنس: ${emp.gender}\n` +
        `المدينة: ${emp.city}\n` +
        `تاريخ الميلاد: ${emp.birth}\n` +
        `اليوم: ${emp.weekday}\n` +
        `الوقت: ${emp.time}\n` +
        `تاريخ التسجيل: ${emp.regDate}`
      );
    };
  }

 function printA5(id) {
  const tx = db.transaction("employees", "readonly");
  const store = tx.objectStore("employees");
  const request = store.get(id);

  request.onsuccess = () => {
    const emp = request.result;

    const printWindow = window.open('', '', 'width=600,height=800'); // حجم أكبر قليلاً من A5

    printWindow.document.write(`
      <html dir="rtl">
        <head>
          <title>طباعة بيانات الموظف</title>
          <style>
            body {
              font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
              padding: 40px;
              font-size: 16px;
              direction: rtl;
              text-align: right;
            }
            h2 {
              text-align: center;
              margin-bottom: 30px;
            }
            p {
              margin: 10px 0;
              border-bottom: 1px dashed #ccc;
              padding-bottom: 5px;
            }
            strong {
              display: inline-block;
              width: 150px;
            }
          </style>
        </head>
        <body>
          <h2>بيانات الموظف</h2>
          <p><strong>الاسم:</strong> ${emp.name}</p>
          <p><strong>الجنس:</strong> ${emp.gender}</p>
          <p><strong>المدينة:</strong> ${emp.city}</p>
          <p><strong>تاريخ الميلاد:</strong> ${emp.birth}</p>
          <p><strong>اليوم:</strong> ${emp.weekday}</p>
          <p><strong>الوقت:</strong> ${emp.time}</p>
          <p><strong>تاريخ التسجيل:</strong> ${emp.regDate}</p>
        </body>
      </html>
    `);

    printWindow.document.close();
    printWindow.focus();
    printWindow.print();
    printWindow.close();
  };
}


  // تحديث قائمة تواريخ الميلاد من قاعدة البيانات (بشكل فريد)
  function updateBirthDatesDropdown() {
    const tx = db.transaction("employees", "readonly");
    const store = tx.objectStore("employees");
    store.getAll().onsuccess = (e) => {
      const employees = e.target.result;
      const births = new Set(employees.map(emp => emp.birth).filter(b => b));
      const select = document.getElementById("filterBirthSelect");
      const currentVal = select.value;

      // نظف القوائم السابقة
      while (select.options.length > 1) {
        select.remove(1);
      }

      Array.from(births).sort().forEach(b => {
        const opt = document.createElement("option");
        opt.value = b;
        opt.textContent = b;
        select.appendChild(opt);
      });

      // حافظ على الاختيار إذا مازال موجود
      if (births.has(currentVal)) {
        select.value = currentVal;
      } else {
        select.value = "";
      }
    };
  }

  // دالة التصفية الرئيسية (بحث + فلترة + تحديث قوائم)


  function updateDropdownsBasedOn(employees) {
    const genders = new Set();
    const cities = new Set();
    const births = new Set();

    employees.forEach(emp => {
      if(emp.gender) genders.add(emp.gender);
      if(emp.city) cities.add(emp.city);
      if(emp.birth) births.add(emp.birth);
    });

    fillSelect("filterGender", genders);
    fillSelect("filterCity", cities);
    fillSelect("filterBirthSelect", births);
  }

  function fillSelect(id, items) {
    const select = document.getElementById(id);
    const currentValue = select.value;
    while (select.options.length > 1) {
      select.remove(1);
    }
    Array.from(items).sort().forEach(item => {
      const opt = document.createElement("option");
      opt.value = item;
      opt.textContent = item;
      select.appendChild(opt);
    });
    if (items.has(currentValue)) {
      select.value = currentValue;
    } else {
      select.value = "";
    }
  }
function showSuccessMessage(msg) {
  const messageDiv = document.getElementById("successMessage");
  messageDiv.textContent = msg;
  messageDiv.style.display = "block";
  messageDiv.style.opacity = "1";
  
  setTimeout(() => {
    messageDiv.style.opacity = "0";
    setTimeout(() => {
      messageDiv.style.display = "none";
    }, 400);
  }, 3000); // تختفي بعد 3 ثوانٍ
}


function saveEdit(event) {
  event.preventDefault();
  const name = document.getElementById("editName").value.trim();
  const gender = document.getElementById("editGender").value;
  const city = document.getElementById("editCity").value;
  const birth = document.getElementById("editBirth").value;

  if (!name || !birth) {
    alert("يرجى ملء جميع الحقول.");
    return;
  }

const now = new Date();
const weekday = now.toLocaleDateString("ar-EG", { weekday: "long" }); // اسم اليوم بالعربي، صحيح
const time = now.toLocaleTimeString("ar-EG");

const year = now.getFullYear();
const month = (now.getMonth() + 1).toString().padStart(2, '0');
const day = now.getDate().toString().padStart(2, '0');
const regDate = `${year}-${month}-${day}`; // تاريخ محلي صحيح





  const tx = db.transaction("employees", "readwrite");
  const store = tx.objectStore("employees");
  const employee = {
    id: editingId,
    name,
    gender,
    city,
    birth,
    weekday,
    time,
    regDate
  };

  store.put(employee);
  tx.oncomplete = () => {
    showSuccessMessage("تم تعديل بيانات الموظف " + name + " بنجاح.");
    updateBirthDatesDropdown();
    closeEditModal();
    filterAndShow();
  };
}





function showDetails(id) {
  const tx = db.transaction("employees", "readonly");
  const store = tx.objectStore("employees");
  const request = store.get(id);
  request.onsuccess = () => {
    const emp = request.result;
    const detailsContent = document.getElementById("detailsContent");
    detailsContent.innerHTML = `
      <h3 style="text-align:center; margin-bottom: 15px;">معلومات الموظف</h3>
      <p><strong>الاسم:</strong> ${emp.name}</p>
      <p><strong>الجنس:</strong> ${emp.gender}</p>
      <p><strong>المدينة:</strong> ${emp.city}</p>
      <p><strong>تاريخ الميلاد:</strong> ${emp.birth}</p>
      <p><strong>اليوم:</strong> ${emp.weekday}</p>
      <p><strong>الوقت:</strong> ${emp.time}</p>
      <p><strong>تاريخ التسجيل:</strong> ${emp.regDate}</p>
    `;
    document.getElementById("detailsModal").style.display = "block";
  };
}

function closeDetailsModal() {
  document.getElementById("detailsModal").style.display = "none";
}

function printCurrentDetails() {
  const detailsContent = document.getElementById("detailsContent").innerHTML;
  const printWindow = window.open('', '', 'width=800,height=600');

  printWindow.document.write(`
    <html dir="rtl">
      <head>
        <title>طباعة بيانات الموظف</title>
        <style>
          body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; padding: 20px; }
          h3 { text-align: center; }
          p { font-size: 16px; }
        </style>
      </head>
      <body>${detailsContent}</body>
    </html>
  `);
  printWindow.document.close();
  printWindow.focus();
  printWindow.print();
  printWindow.close();
}

// زر فتح النافذة
document.getElementById('openSettingsBtn').addEventListener('click', () => {
  document.getElementById('settingsModal').style.display = 'block';
});

// إغلاق النافذة
function closeSettingsModal() {
  document.getElementById('settingsModal').style.display = 'none';
}

// دالة عرض الإحصائيات

function filterAndShow() {
  const nameInput = document.getElementById("searchName").value.trim().toLowerCase();
  const firstChar = nameInput ? nameInput[0] : "";
  const genderFilter = document.getElementById("filterGender").value;
  const cityFilter = document.getElementById("filterCity").value;
  const birthFilter = document.getElementById("filterBirthSelect").value;

  const tx = db.transaction("employees", "readonly");
  const store = tx.objectStore("employees");
  store.getAll().onsuccess = (e) => {
    let employees = e.target.result;

    // فلترة حسب الإدخالات
    if (firstChar) {
      employees = employees.filter(emp => emp.name.toLowerCase().startsWith(firstChar));
    }
    if (genderFilter) {
      employees = employees.filter(emp => emp.gender === genderFilter);
    }
    if (cityFilter) {
      employees = employees.filter(emp => emp.city === cityFilter);
    }
    if (birthFilter) {
      employees = employees.filter(emp => emp.birth === birthFilter);
    }

    updateDropdownsBasedOn(employees);

    const tbody = document.querySelector("#employeeTable tbody");
    tbody.innerHTML = "";

    // ✅ عرض فقط أول 200 نتيجة
    let limitedEmployees = employees.slice(0, 200);
    limitedEmployees.forEach(emp => renderRow(emp));

    // تحديث قائمة الأسماء المطابقة
    updateMatchingNamesList(employees, nameInput);

    // عرض إحصائيات
    const allEmployees = e.target.result;
    let displayParts = [];

    if (!firstChar && !cityFilter && !genderFilter && !birthFilter) {
      displayParts.push(`مجموع المسجلين: ${allEmployees.length}`);
    } else {
      if (firstChar) {
        const countName = allEmployees.filter(emp => emp.name.toLowerCase().startsWith(firstChar)).length;
        displayParts.push(`نتائج الاسم: ${countName}`);
      }
      if (cityFilter) {
        const countCity = allEmployees.filter(emp => emp.city === cityFilter).length;
        displayParts.push(`المدينة: ${countCity}`);
      }
      if (genderFilter) {
        const countGender = allEmployees.filter(emp => emp.gender === genderFilter).length;
        displayParts.push(`الجنس: ${countGender}`);
      }
      if (birthFilter) {
        const countBirth = allEmployees.filter(emp => emp.birth === birthFilter).length;
        displayParts.push(`تاريخ الميلاد: ${countBirth}`);
      }
    }

    document.getElementById("countDisplay").textContent = displayParts.join(" | ");
  };
}

function adjustMatchingNamesWidth() {
  const matchingNames = document.getElementById("matchingNames");
  if (window.innerWidth <= 1366) { // شاشة لابتوب أو أصغر
    matchingNames.style.width = "200px";
  } else {
    matchingNames.style.width = "100%";
  }
}

window.addEventListener('resize', adjustMatchingNamesWidth);
window.addEventListener('load', adjustMatchingNamesWidth);


// تصدير البيانات إلى ملف JSON
function exportData() {
  const request = indexedDB.open('EmployeeDB', 1);
  request.onsuccess = function (event) {
    const db = event.target.result;
    const transaction = db.transaction(['employees'], 'readonly');
    const store = transaction.objectStore('employees');
    const getAll = store.getAll();

    getAll.onsuccess = function () {
      const data = JSON.stringify(getAll.result, null, 2);
      const blob = new Blob([data], { type: 'application/json' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = 'employees_backup.json';
      a.click();
      URL.revokeObjectURL(url);
    };
  };
}

// استيراد البيانات من ملف JSON
function importData() {
  const input = document.createElement('input');
  input.type = 'file';
  input.accept = 'application/json';
  input.onchange = function (event) {
    const file = event.target.files[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = function (e) {
      try {
        const data = JSON.parse(e.target.result);
        const request = indexedDB.open('EmployeeDB', 1);
        request.onsuccess = function (event) {
          const db = event.target.result;
          const transaction = db.transaction(['employees'], 'readwrite');
          const store = transaction.objectStore('employees');

          data.forEach(emp => {
            store.put(emp); // store.add(emp) إذا تريد منع التكرار
          });

          transaction.oncomplete = function () {
            alert('✅ تم استيراد البيانات بنجاح!');
            // location.reload(); ❌
            filterAndShow(); // ✅ بديل صحيح وموجود

          };
        };
      } catch (err) {
        alert('❌ فشل في قراءة الملف. تأكد من صحة تنسيقه.');
        console.error(err);
      }
    };
    reader.readAsText(file);
  };
  input.click();
}

// دالة تسجيل الخروج

  function logout() {
  closeSettingsModal();
  location.reload();
}




function checkLogin() {
  const username = document.getElementById("loginUsername").value.trim();
  const password = document.getElementById("loginPassword").value;

  const correctUsername = "احمد";
  const correctPassword = "1234";

  if (username === correctUsername && password === correctPassword) {
    document.getElementById("loginModal").style.display = "none";
  } else {
    const errorMsg = document.getElementById("loginError");
    errorMsg.style.display = "block";
    setTimeout(() => {
      errorMsg.style.display = "none";
    }, 3000);
  }
}



function closeSettingsModal() {
  const settings = document.getElementById("settingsModal");
  if (settings) settings.style.display = "none";
}





// عند تحميل الصفحة نخفي المحتوى الرئيسي (حتى تسجيل الدخول)
window.onload = function() {
  document.getElementById("mainContent").style.display = "none";
  document.getElementById("loginModal").style.display = "flex";
}

let nameSelected = false;

function updateMatchingNamesList(employees, searchText) {
  const list = document.getElementById("matchingNames");
  list.innerHTML = "";

  if (!searchText || nameSelected) {
    list.style.display = "none";
    return;
  }

  const matchedNamesSet = new Set();

  employees.forEach(emp => {
    if (emp.name.toLowerCase().startsWith(searchText)) {
      matchedNamesSet.add(emp.name);
    }
  });

  if (matchedNamesSet.size === 0) {
    list.style.display = "none";
    return;
  }

  matchedNamesSet.forEach(name => {
    const li = document.createElement("li");
    li.textContent = name;

    li.onclick = () => {
      nameSelected = true;  // منع إعادة الظهور
      document.getElementById("searchName").value = name;
      list.style.display = "none";

      filterAndShow();

      setTimeout(() => {
        nameSelected = false; // إعادة السماح لاحقًا
      }, 300);
    };

    list.appendChild(li);
  });

  list.style.display = "block";
}


function fillBaathDataAndHide(name, gender, city, birth) {
  document.getElementById("baathName").value = name;
  document.getElementById("baathGender").value = gender;
  document.getElementById("baathCity").value = city;
  document.getElementById("baathBirth").value = birth;

  // إخفاء القائمة بعد الاختيار
  document.getElementById("resultsList").innerHTML = "";
}

function showStatistics() {
  const iraqiCities = [
    "أربيل", "دهوك", "السليمانية", "بغداد", "البصرة", "نينوى",
    "كركوك", "النجف", "كربلاء", "ديالى", "الأنبار", "ذي قار",
    "صلاح الدين", "ميسان", "بابل", "واسط", "المثنى", "الناصرية", "حلبجة"
  ];
  let total = 0, male = 0, female = 0;
  const cityCounts = {};

  iraqiCities.forEach(city => cityCounts[city] = 0);

  const tx = db.transaction("employees", "readonly");
  const store = tx.objectStore("employees");
  const request = store.getAll();

  request.onsuccess = () => {
    const employees = request.result;
    total = employees.length;

    employees.forEach(emp => {
      if (emp.gender === "ذكر") male++;
      if (emp.gender === "أنثى") female++;
      if (iraqiCities.includes(emp.city)) {
        cityCounts[emp.city]++;
      }
    });

    // تحديث العدادات الرئيسية
    document.getElementById("stat-total").textContent = total;
    document.getElementById("stat-male").textContent = male;
    document.getElementById("stat-female").textContent = female;

    // توليد أزرار المحافظات التي لديها موظفين
    const cityStatsDiv = document.getElementById("cityStats");
    cityStatsDiv.innerHTML = ""; // تنظيف المحتوى

    iraqiCities.forEach(city => {
      const count = cityCounts[city];
      if (count > 0) {
        const btn = document.createElement("button");
        btn.textContent = `${city} (${count})`;
        btn.style.cssText = `
          padding: 8px 14px;
          background: #007bff;
          color: white;
          border: none;
          border-radius: 8px;
          cursor: default;
          font-weight: bold;
          transition: background-color 0.3s;
          width: 100%;
          box-sizing: border-box;
        `;
        btn.onmouseover = () => btn.style.backgroundColor = "#0056b3";
        btn.onmouseout = () => btn.style.backgroundColor = "#007bff";

        cityStatsDiv.appendChild(btn);
      }
    });

    // إظهار النافذة
    document.getElementById("statisticsModal").style.display = "flex";
  };
}



function onSearchInput() {
  const input = document.getElementById("searchName");
  const clearBtn = document.getElementById("clearSearch");
  if (input.value.length > 0) {
    clearBtn.style.display = "block";
  } else {
    clearBtn.style.display = "none";
  }
  filterAndShow(); // إذا عندك دالة فلترة
}

function clearSearchField() {
  const input = document.getElementById("searchName");
  input.value = "";
  document.getElementById("clearSearch").style.display = "none";
  input.focus();
  filterAndShow(); // حدث الفلترة بعد المسح
}

// عند تحميل الصفحة، ضع المؤشر على خانة اسم المستخدم
window.onload = () => {
  document.getElementById("loginUsername").focus();
};

function togglePassword() {
  const passwordInput = document.getElementById("loginPassword");
  const eyeIcon = event.target;

  if (passwordInput.type === "password") {
    passwordInput.type = "text";
    eyeIcon.textContent = "🙈"; // عند العرض
  } else {
    passwordInput.type = "password";
    eyeIcon.textContent = "👁"; // عند الإخفاء
  }
}





// إخفاء كل شيء حتى تسجيل الدخول
window.onload = function() {
  document.body.style.display = "none";
  setTimeout(() => {
    document.body.style.display = "block";
  }, 50); // تأخير بسيط لإظهار loginModal
};



window.onload = function () {
  const nameInput = document.getElementById("name");
  const genderInput = document.getElementById("gender");
  const cityInput = document.getElementById("city");
  const birthInput = document.getElementById("birth");
  const saveBtn = document.getElementById("addEmployee");

  // ضع التركيز تلقائيًا على الاسم
  nameInput.focus();

  // انتقال تلقائي بين الحقول بالضغط على Enter
  nameInput.addEventListener("keydown", function (e) {
    if (e.key === "Enter") {
      e.preventDefault();
      genderInput.focus();
    }
  });

  genderInput.addEventListener("keydown", function (e) {
    if (e.key === "Enter") {
      e.preventDefault();
      cityInput.focus();
    }
  });

  cityInput.addEventListener("keydown", function (e) {
    if (e.key === "Enter") {
      e.preventDefault();
      birthInput.focus();
    }
  });

  birthInput.addEventListener("keydown", function (e) {
    if (e.key === "Enter") {
      e.preventDefault();
      saveBtn.click(); // حفظ تلقائي
    }
  });
};


// تفعيل زر "دخول" عند الضغط على Enter داخل خانة كلمة المرور
document.getElementById("loginPassword").addEventListener("keypress", function(e) {
  if (e.key === "Enter") {
    checkLogin(); // نفس الدالة التي تضغط عليها في الزر
  }
});

function togglePasswordCheckbox() {
  const passwordInput = document.getElementById("loginPassword");
  passwordInput.type = passwordInput.type === "password" ? "text" : "password";
}

document.getElementById('loginUsername').addEventListener('keydown', function(e) {
  if (e.key === 'Enter') {
    e.preventDefault();
    document.getElementById('loginPassword').focus();
  }
});




</script>

</body>
</html>
