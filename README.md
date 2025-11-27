# DATA-COLLETION-USING-OF-TABLE-FORM-IN-USING-OF-WEBPAGE-
THIS IS WORKING OF COLLET TO DATA IN TABLE FORM AND AFTER CREATE A DATA TO DOWLOAD A PDF FORM TO EASY TO USE AND PRINT EASILY.
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Professional Data Table App</title>

<!-- TensorFlow.js -->
<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@4.12.0/dist/tf.min.js"></script>

<!-- PDF Library -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>

<style>
/* ===== GLOBAL BODY WITH BACKGROUND IMAGE ===== */
body {
  margin:0;
  padding:0;
  font-family: Arial, Helvetica, sans-serif;
  background: url("VISULIZATION.png") no-repeat center center fixed;
  background-size: cover;
  position: relative;
}

/* DARK OVERLAY */
body::before {
  content:"";
  position:fixed;
  top:0; left:0;
  width:100%; height:100%;
  background: rgba(0,0,20,0.7);
  z-index:-1;
}

/* HEADINGS */
h1, h2 {
  text-align:center;
  color:#00d4ff;
  text-shadow:0 0 20px #00d4ff;
}

/* FORM CONTAINERS */
.container {
  max-width:420px;
  margin:60px auto;
  background: rgba(255,255,255,0.08);
  padding:30px;
  border-radius:15px;
  backdrop-filter: blur(8px);
  box-shadow:0 0 25px rgba(0,212,255,0.4);
  color:white;
}

/* INPUTS */
input, select {
  width:100%;
  padding:12px;
  margin:10px 0;
  border:none;
  border-radius:6px;
  background:rgba(255,255,255,0.12);
  color:white;
}

/* FIXED PAPER DROPDOWN VISIBILITY */
select option {
  background:#222;
  color:white;
  padding:10px;
}

/* BUTTONS */
button {
  width:100%;
  padding:12px;
  border:none;
  border-radius:8px;
  background:#00d4ff;
  font-weight:bold;
  cursor:pointer;
  color:black;
  margin-top:10px;
}
button:hover {
  transform:scale(1.05);
  box-shadow:0 0 20px #00d4ff;
}

/* HIDE PAGES */
.hidden { display:none; }

/* MAIN APP PAGE */
#mainContainer {
  display:flex;
  justify-content:space-between;
  gap:20px;
  max-width:95%;
  margin:20px auto;
}

/* LEFT TABLE SECTION */
#tableSection {
  flex:3;
  background: rgba(255,255,255,0.1);
  padding:20px;
  border-radius:12px;
  backdrop-filter: blur(6px);
  box-shadow:0 0 20px #00d4ff;
}

/* RIGHT CONTROL PANEL */
#controls {
  flex:1;
  background: rgba(255,255,255,0.1);
  padding:20px;
  border-radius:12px;
  backdrop-filter: blur(6px);
  color:white;
  box-shadow:0 0 20px #00d4ff;
}
#controls label {
  font-weight:bold;
  margin-top:15px;
}

/* TABLE */
table {
  width:100%;
  border-collapse: collapse;
  margin-top:20px;
  background:white;
  color:black;
}
th, td {
  border:1px solid black;
  padding:8px;
  text-align:center;
}

/* FIX — TABLE INPUT VISIBILITY */
td input, th input {
  width: 100%;
  border: 1px solid #555;
  padding: 6px;
  background: #f2f2f2;
  color: #000;
  border-radius: 4px;
  font-weight: bold;
}
td input:focus, th input:focus {
  background:#ffffff;
  outline:2px solid #00d4ff;
}
</style>
</head>
<body>

<!-- ========== LOGIN / REGISTER / FORGOT ========== -->
<div id="authPage">

  <!-- LOGIN -->
  <div id="loginBox" class="container">
    <h1>Login</h1>
    <input id="loginUser" type="text" placeholder="Username">
    <input id="loginPass" type="password" placeholder="Password">
    <button id="loginBtn">Login</button>
    <p>No account? <a id="goRegister">Register</a></p>
    <p><a id="goForgot">Forgot Password?</a></p>
  </div>

  <!-- REGISTER -->
  <div id="registerBox" class="container hidden">
    <h1>Register</h1>
    <input id="regUser" type="text" placeholder="Create Username">
    <input id="regPass" type="password" placeholder="Create Password">
    <button id="regBtn">Create Account</button>
    <p><a id="goLogin1">Back to Login</a></p>
  </div>

  <!-- FORGOT PASSWORD -->
  <div id="forgotBox" class="container hidden">
    <h1>Reset Password</h1>
    <input id="fpUser" type="text" placeholder="Username">
    <input id="fpNewPass" type="password" placeholder="New Password">
    <button id="fpBtn">Reset Password</button>
    <p><a id="goLogin2">Back to Login</a></p>
  </div>

</div>

<!-- ========= MAIN APP PAGE ========= -->
<div id="appPage" class="hidden">
  <h1 id="welcomeUser"></h1>

  <div id="mainContainer">

    <!-- TABLE OUTPUT -->
    <div id="tableSection">
      <h2 id="tableHeading">Table Output</h2>

      <table id="dataTable">
        <thead></thead>
        <tbody></tbody>
      </table>

      <button id="addRow">Add Row</button>
      <button id="predictNext">Predict Next Value</button>
      <button id="predictRow">Auto-Fill Predicted Row</button>
      <button id="downloadPDF">Download PDF</button>
    </div>

    <!-- RIGHT SIDE CONTROLS -->
    <div id="controls">
      <label>Table Title</label>
      <input type="text" id="titleInput">

      <label>Rows</label>
      <input type="number" id="rowsInput" value="5">

      <label>Columns</label>
      <input type="number" id="colsInput" value="5">

      <label>Paper Size</label>
      <select id="paperSize">
        <option>A4</option>
        <option>A3</option>
        <option>A5</option>
        <option>Letter</option>
        <option>Legal</option>
      </select>

      <label>History</label>
      <select id="historySelect">
        <option value="">--Select Previous Table--</option>
      </select>
      <button id="loadHistory">Load Table</button>

      <button id="makeTable">Create Table</button>
      <button id="logout">Logout</button>
    </div>

  </div>
</div>

<script>
/* ====== IndexedDB Setup ====== */
let db;
const request = indexedDB.open("UserDB", 1);

request.onupgradeneeded = (e) => {
  db = e.target.result;
  if (!db.objectStoreNames.contains("users")) {
    db.createObjectStore("users", { keyPath: "username" });
  }
};

request.onsuccess = (e) => {
  db = e.target.result;
  const user = localStorage.getItem("loggedIn");
  if (user) loadApp(user);
};

/* ====== Login / Register / Reset ====== */
function loadApp(user) {
  authPage.classList.add("hidden");
  appPage.classList.remove("hidden");
  welcomeUser.textContent = "Welcome, " + user;
  populateHistory(user);
}

loginBtn.onclick = () => {
  let user = loginUser.value.trim();
  let pass = loginPass.value.trim();
  let tx = db.transaction("users", "readonly");
  let store = tx.objectStore("users");
  let r = store.get(user);
  r.onsuccess = () => {
    if (r.result && r.result.password === pass) {
      localStorage.setItem("loggedIn", user);
      loadApp(user);
    } else alert("Invalid Login");
  };
};

regBtn.onclick = () => {
  let user = regUser.value.trim();
  let pass = regPass.value.trim();
  let tx = db.transaction("users", "readwrite");
  let store = tx.objectStore("users");
  store.add({ username:user, password:pass, history: [] });
  alert("Registered");
};

fpBtn.onclick = () => {
  let user = fpUser.value.trim();
  let pass = fpNewPass.value.trim();
  let tx = db.transaction("users", "readwrite");
  let store = tx.objectStore("users");
  let r = store.get(user);
  r.onsuccess = () => {
    if (r.result) {
      store.put({ username:user, password:pass, history: r.result.history || [] });
      alert("Password Updated");
    }
  };
};

/* PAGE SWITCH */
goRegister.onclick = () => { loginBox.classList.add("hidden"); registerBox.classList.remove("hidden"); };
goLogin1.onclick   = () => { registerBox.classList.add("hidden"); loginBox.classList.remove("hidden"); };
goForgot.onclick   = () => { loginBox.classList.add("hidden"); forgotBox.classList.remove("hidden"); };
goLogin2.onclick   = () => { forgotBox.classList.add("hidden"); loginBox.classList.remove("hidden"); };
logout.onclick = () => { localStorage.removeItem("loggedIn"); location.reload(); };

/* ====== Table Creation ====== */
function createTable() {
  let r = rowsInput.value;
  let c = colsInput.value;
  tableHeading.textContent = titleInput.value;
  let thead = dataTable.querySelector("thead");
  let tbody = dataTable.querySelector("tbody");
  thead.innerHTML = ""; tbody.innerHTML = "";

  // Header
  let hr = document.createElement("tr");
  for (let i=0;i<c;i++){
    let th=document.createElement("th");
    let inp=document.createElement("input");
    th.appendChild(inp);
    hr.appendChild(th);
  }
  thead.appendChild(hr);

  // Body
  for (let i=0;i<r;i++){
    let tr=document.createElement("tr");
    for (let j=0;j<c;j++){
      let td=document.createElement("td");
      let inp=document.createElement("input");
      td.appendChild(inp);
      tr.appendChild(td);
    }
    tbody.appendChild(tr);
  }
}

/* ====== Save & Load History ====== */
function saveTableToHistory(user){
  let rows=[];
  dataTable.querySelectorAll("tbody tr").forEach(tr=>{
    let row=[];
    tr.querySelectorAll("td input").forEach(td=>{row.push(td.value);});
    rows.push(row);
  });
  let header=[];
  dataTable.querySelectorAll("thead th input").forEach(th=>{header.push(th.value);});
  let tx=db.transaction("users","readwrite");
  let store=tx.objectStore("users");
  let req=store.get(user);
  req.onsuccess=()=>{ 
    let data=req.result;
    if(!data.history)data.history=[];
    data.history.push({title:tableHeading.textContent, header:header, rows:rows, date:new Date().toLocaleString()});
    store.put(data);
    populateHistory(user);
  };
}

function populateHistory(user){
  let tx=db.transaction("users","readonly");
  let store=tx.objectStore("users");
  let req=store.get(user);
  req.onsuccess=()=>{
    let data=req.result;
    let historySelect=document.getElementById("historySelect");
    historySelect.innerHTML='<option value="">--Select Previous Table--</option>';
    if(data.history){
      data.history.forEach((item,index)=>{
        let opt=document.createElement("option");
        opt.value=index;
        opt.textContent=`${item.title} (${item.date})`;
        historySelect.appendChild(opt);
      });
    }
  };
}

loadHistory.onclick=()=>{
  let user=localStorage.getItem("loggedIn");
  let index=historySelect.value;
  if(index==="")return;
  let tx=db.transaction("users","readonly");
  let store=tx.objectStore("users");
  let req=store.get(user);
  req.onsuccess=()=>{
    let data=req.result;
    let item=data.history[index];
    tableHeading.textContent=item.title;
    let thead=dataTable.querySelector("thead");
    let tbody=dataTable.querySelector("tbody");
    thead.innerHTML="";tbody.innerHTML="";
    let hr=document.createElement("tr");
    item.header.forEach(h=>{
      let th=document.createElement("th");
      let inp=document.createElement("input");
      inp.value=h;
      th.appendChild(inp);
      hr.appendChild(th);
    });
    thead.appendChild(hr);
    item.rows.forEach(r=>{
      let tr=document.createElement("tr");
      r.forEach(cell=>{
        let td=document.createElement("td");
        let inp=document.createElement("input");
        inp.value=cell;
        td.appendChild(inp);
        tr.appendChild(td);
      });
      tbody.appendChild(tr);
    });
  };
};

/* ====== Table Buttons ====== */
makeTable.onclick=()=>{ createTable(); saveTableToHistory(localStorage.getItem("loggedIn")); };
addRow.onclick=()=>{
  let cols=dataTable.querySelectorAll("thead th").length;
  let tr=document.createElement("tr");
  for(let i=0;i<cols;i++){
    let td=document.createElement("td");
    let inp=document.createElement("input");
    td.appendChild(inp);
    tr.appendChild(td);
  }
  dataTable.querySelector("tbody").appendChild(tr);
};

/* ====== Download PDF ====== */
downloadPDF.onclick=()=>{
  let cloned=dataTable.cloneNode(true);
  cloned.querySelectorAll("input").forEach(i=>{i.parentElement.textContent=i.value;});
  let wrapper=document.createElement("div");
  let t=document.createElement("h2");
  t.textContent=tableHeading.textContent;
  t.style.textAlign="center";
  wrapper.appendChild(t);
  wrapper.appendChild(cloned);
  html2pdf().set({margin:0.5, filename:"Table.pdf", html2canvas:{scale:2}, jsPDF:{unit:"in", format:paperSize.value, orientation:"portrait"}}).from(wrapper).save();
  saveTableToHistory(localStorage.getItem("loggedIn"));
};

/* ====== Predict Next Value (Single) ====== */
predictNext.onclick=async()=>{
  let data=[];
  dataTable.querySelectorAll("tbody tr").forEach(tr=>{
    let row=[];
    tr.querySelectorAll("td input").forEach(td=>{
      let val=parseFloat(td.value);
      if(!isNaN(val))row.push(val);
    });
    if(row.length>0)data.push(row);
  });
  if(data.length===0){ alert("Enter numeric data to predict!"); return; }
  let flatData=data.flat();
  const xs=tf.tensor1d(flatData.map((v,i)=>i));
  const ys=tf.tensor1d(flatData);
  const model=tf.sequential();
  model.add(tf.layers.dense({units:1, inputShape:[1]}));
  model.compile({loss:'meanSquaredError', optimizer:'sgd'});
  await model.fit(xs,ys,{epochs:200});
  const nextIndex=flatData.length;
  const predTensor=model.predict(tf.tensor2d([nextIndex],[1,1]));
  const predValue=(await predTensor.data())[0];
  alert("Predicted next value: "+predValue.toFixed(2));
};

/* ====== Auto-Fill Predicted Row ====== */
predictRow.onclick=async()=>{
  let tbody=dataTable.querySelector("tbody");
  let thead=dataTable.querySelectorAll("thead th");
  let cols=thead.length;
  if(cols===0){ alert("Create table first!"); return; }

  let colData=[];
  for(let c=0;c<cols;c++) colData[c]=[];
  tbody.querySelectorAll("tr").forEach(tr=>{
    tr.querySelectorAll("td input").forEach((td,idx)=>{
      let val=parseFloat(td.value);
      if(!isNaN(val)) colData[idx].push(val);
    });
  });

  let predictedRow=[];
  for(let c=0;c<cols;c++){
    let data=colData[c];
    if(data.length===0){ predictedRow[c]=0; continue; }
    const xs=tf.tensor1d(data.map((v,i)=>i));
    const ys=tf.tensor1d(data);
    const model=tf.sequential();
    model.add(tf.layers.dense({units:1,inputShape:[1]}));
    model.compile({loss:'meanSquaredError', optimizer:'sgd'});
    await model.fit(xs,ys,{epochs:200});
    const nextIndex=data.length;
    const predTensor=model.predict(tf.tensor2d([nextIndex],[1,1]));
    const predValue=(await predTensor.data())[0];
    predictedRow[c]=predValue.toFixed(2);
  }

  // Add predicted row
  let tr=document.createElement("tr");
  predictedRow.forEach(val=>{
    let td=document.createElement("td");
    let inp=document.createElement("input");
    inp.value=val;
    td.appendChild(inp);
    tr.appendChild(td);
  });
  tbody.appendChild(tr);
  alert("Predicted row added!");
};
</script>
</body>
</html>
