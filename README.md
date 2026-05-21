# ai-smart-quiz-hub
An interactive AI-powered quiz web app with mock tests, custom questions, score tracking, and responsive UI built using HTML, CSS, and JavaScript.
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Smart Quiz Hub</title>

<style>

*{
margin:0;
padding:0;
box-sizing:border-box;
font-family:Arial,sans-serif;
}

body{
background:linear-gradient(135deg,#020617,#0f172a,#1e293b);
min-height:100vh;
display:flex;
justify-content:center;
align-items:center;
padding:20px;
color:white;
}

.container{
width:100%;
max-width:950px;
background:rgba(255,255,255,0.07);
backdrop-filter:blur(12px);
border-radius:25px;
padding:30px;
box-shadow:0 0 30px rgba(0,255,255,0.2);
}

h1,h2,h3{
text-align:center;
margin-bottom:15px;
color:#00ffff;
}

input,select{
width:100%;
padding:14px;
margin:10px 0;
border:none;
border-radius:12px;
background:#1e293b;
color:white;
font-size:16px;
}

button{
width:100%;
padding:14px;
margin-top:10px;
border:none;
border-radius:12px;
background:#00ffff;
color:black;
font-size:17px;
cursor:pointer;
font-weight:bold;
transition:0.3s;
}

button:hover{
transform:scale(1.02);
}

.auth-box,
.home,
.quiz-box,
.result,
.flashcards,
.admin-panel{
display:none;
}

.subjects{
display:grid;
grid-template-columns:repeat(auto-fit,minmax(180px,1fr));
gap:20px;
margin-top:20px;
}

.subject-btn{
background:#111827;
color:white;
padding:20px;
border-radius:18px;
}

.subject-btn:hover{
background:#00ffff;
color:black;
}

.info{
display:flex;
justify-content:space-between;
margin-bottom:20px;
font-size:18px;
}

.progress{
width:100%;
height:12px;
background:#334155;
border-radius:10px;
overflow:hidden;
margin-bottom:20px;
}

.progress-bar{
width:0%;
height:100%;
background:#00ffff;
}

.question{
font-size:24px;
margin-bottom:20px;
}

.options button{
background:#1e293b;
color:white;
margin:10px 0;
}

.selected{
background:#00ffff !important;
color:black !important;
}

.result ul{
text-align:left;
margin-top:20px;
}

.result li{
background:#1e293b;
padding:10px;
margin:10px 0;
border-radius:10px;
}

.flash-card{
background:#111827;
padding:25px;
border-radius:20px;
margin-top:20px;
text-align:center;
cursor:pointer;
min-height:180px;
display:flex;
justify-content:center;
align-items:center;
font-size:22px;
font-weight:bold;
}

.flash-answer{
color:#00ffff;
font-size:28px;
}

.logout{
background:red;
color:white;
}

</style>
</head>
<body>

<div class="container">

<h1>AI Smart Quiz Hub</h1>

<!-- LOGIN -->

<div class="auth-box" id="authBox" style="display:block;">

<h2>Login</h2>

<input type="text" id="loginUser" placeholder="Username">
<input type="password" id="loginPass" placeholder="Password">

<button onclick="login()">Login</button>

<br><br>

<h2>Signup</h2>

<input type="text" id="signupUser" placeholder="Create Username">
<input type="password" id="signupPass" placeholder="Create Password">

<button onclick="signup()">Signup</button>

</div>

<!-- HOME -->

<div class="home" id="home">

<h2 id="welcome"></h2>

<h3>Select Subject</h3>

<div class="subjects">

<button class="subject-btn" onclick="startQuiz('math')">
Mathematics
</button>

<button class="subject-btn" onclick="startQuiz('science')">
Science
</button>

<button class="subject-btn" onclick="startQuiz('computer')">
Computer
</button>

</div>

<button onclick="openFlashcards()" style="background:#facc15;">
📘 Flash Cards
</button>

<button onclick="openAdmin()" style="background:#22c55e;">
➕ Add Your Own Questions
</button>

<button class="logout" onclick="logout()">
Logout
</button>

</div>

<!-- ADMIN PANEL -->

<div class="admin-panel" id="adminPanel">

<h2>Add New Question</h2>

<select id="subjectSelect">

<option value="math">Mathematics</option>
<option value="science">Science</option>
<option value="computer">Computer</option>

</select>

<input type="text" id="newQuestion" placeholder="Enter Question">

<input type="text" id="option1" placeholder="Option 1">
<input type="text" id="option2" placeholder="Option 2">
<input type="text" id="option3" placeholder="Option 3">
<input type="text" id="option4" placeholder="Option 4">

<input type="text" id="correctAnswer" placeholder="Correct Answer">

<button onclick="addQuestion()">
Save Question
</button>

<button onclick="goHomeFromAdmin()">
⬅ Back
</button>

</div>

<!-- QUIZ -->

<div class="quiz-box" id="quizBox">

<div class="info">
<span id="timer">Time: 20</span>
<span id="score">Score: 0</span>
</div>

<div class="progress">
<div class="progress-bar" id="progressBar"></div>
</div>

<div class="question" id="question"></div>

<div class="options" id="options"></div>

<button onclick="nextQuestion()">Next Question</button>

</div>

<!-- RESULT -->

<div class="result" id="result">

<h2>Quiz Completed 🎉</h2>

<h3 id="finalScore"></h3>

<div id="review"></div>

<button onclick="goHome()">Back To Home</button>

</div>

<!-- FLASHCARDS -->

<div class="flashcards" id="flashcards">

<h2>📘 Flash Cards</h2>

<select id="flashSubject" onchange="loadFlashcards()">

<option value="math">Mathematics</option>
<option value="science">Science</option>
<option value="computer">Computer</option>

</select>

<div id="flashCardBox"></div>

<button onclick="goHomeFromFlash()">
⬅ Back
</button>

</div>

</div>

<script>

/* LOGIN SYSTEM */

function signup(){

let user=document.getElementById("signupUser").value.trim();
let pass=document.getElementById("signupPass").value.trim();

if(user==="" || pass===""){

alert("Fill all fields");
return;

}

if(localStorage.getItem(user)){

alert("Username already exists");
return;

}

localStorage.setItem(user,pass);

alert("Signup Successful");

}

function login(){

let user=document.getElementById("loginUser").value.trim();
let pass=document.getElementById("loginPass").value.trim();

if(user==="" || pass===""){

alert("Please fill all fields");
return;

}

let storedPass=localStorage.getItem(user);

if(storedPass===null){

alert("User not found");
return;

}

if(pass===storedPass){

localStorage.setItem("currentUser",user);

showHome();

}else{

alert("Wrong Password");

}

}

function logout(){

localStorage.removeItem("currentUser");

location.reload();

}

function showHome(){

document.getElementById("authBox").style.display="none";
document.getElementById("home").style.display="block";

let currentUser=localStorage.getItem("currentUser");

document.getElementById("welcome").innerText=
"Welcome "+currentUser+" 👋";

}

if(localStorage.getItem("currentUser")){
showHome();
}

/* QUIZ DATA */

let quizData={

math:[
{question:"5 + 7 = ?",options:["10","12","14","15"],answer:"12"},
{question:"Square root of 81 ?",options:["7","8","9","10"],answer:"9"},
{question:"10 × 5 = ?",options:["45","50","55","60"],answer:"50"},
{question:"15 - 8 = ?",options:["5","6","7","8"],answer:"7"},
{question:"100 ÷ 4 = ?",options:["20","25","30","35"],answer:"25"},
{question:"Value of π ?",options:["3.14","2.14","4.14","5.14"],answer:"3.14"},
{question:"9² = ?",options:["18","27","81","72"],answer:"81"},
{question:"Cube of 3 ?",options:["6","9","27","81"],answer:"27"},
{question:"Area of square formula ?",options:["a²","2a","4a","a³"],answer:"a²"},
{question:"Perimeter of rectangle ?",options:["2(l+b)","l+b","l×b","b²"],answer:"2(l+b)"}
],

science:[
{question:"Water Formula ?",options:["H2O","CO2","NaCl","O2"],answer:"H2O"},
{question:"Chemical symbol of Oxygen ?",options:["O","Ox","Og","Oo"],answer:"O"},
{question:"Red Planet ?",options:["Earth","Mars","Venus","Jupiter"],answer:"Mars"},
{question:"Sun is a ?",options:["Planet","Star","Asteroid","Satellite"],answer:"Star"},
{question:"Largest organ in human body ?",options:["Heart","Skin","Liver","Lungs"],answer:"Skin"},
{question:"Force SI unit ?",options:["Newton","Volt","Joule","Watt"],answer:"Newton"},
{question:"Boiling point of water ?",options:["50°C","90°C","100°C","120°C"],answer:"100°C"},
{question:"Plants use during photosynthesis ?",options:["Oxygen","Nitrogen","CO2","Hydrogen"],answer:"CO2"},
{question:"How many chambers in heart ?",options:["2","3","4","5"],answer:"4"},
{question:"Earth natural satellite ?",options:["Moon","Sun","Mars","Venus"],answer:"Moon"}
],

computer:[
{
question:"HTML stands for ?",
options:[
"Hyper Text Markup Language",
"Home Tool Markup",
"Hyper Transfer Markup",
"HighText Language"
],
answer:"Hyper Text Markup Language"
},
{
question:"CSS used for ?",
options:["Styling","Database","Programming","Hardware"],
answer:"Styling"
},
{
question:"JavaScript used for ?",
options:["Animation","Interactivity","Storage","Hardware"],
answer:"Interactivity"
},
{
question:"Brain of computer ?",
options:["CPU","RAM","Mouse","Keyboard"],
answer:"CPU"
},
{
question:"Full form of CPU ?",
options:[
"Central Processing Unit",
"Central Print Unit",
"Computer Processing Unit",
"Control Process Unit"
],
answer:"Central Processing Unit"
},
{
question:"Input device ?",
options:["Monitor","Keyboard","Speaker","Printer"],
answer:"Keyboard"
},
{
question:"WWW stands for ?",
options:[
"World Wide Web",
"Wide Web World",
"World Web Window",
"Web World Wide"
],
answer:"World Wide Web"
},
{
question:"Shortcut key for copy ?",
options:["Ctrl+C","Ctrl+V","Ctrl+X","Ctrl+Z"],
answer:"Ctrl+C"
},
{
question:"Binary digits are ?",
options:["0 and 1","1 and 2","A and B","Yes and No"],
answer:"0 and 1"
},
{
question:"Which language runs in browser ?",
options:["Java","Python","JavaScript","C"],
answer:"JavaScript"
}
]

};

/* LOAD SAVED QUESTIONS */

if(localStorage.getItem("customQuiz")){

quizData=JSON.parse(localStorage.getItem("customQuiz"));

}

/* ADD QUESTION */

function openAdmin(){

document.getElementById("home").style.display="none";
document.getElementById("adminPanel").style.display="block";

}

function addQuestion(){

let subject=document.getElementById("subjectSelect").value;

let question=document.getElementById("newQuestion").value;

let option1=document.getElementById("option1").value;
let option2=document.getElementById("option2").value;
let option3=document.getElementById("option3").value;
let option4=document.getElementById("option4").value;

let answer=document.getElementById("correctAnswer").value;

if(
question==="" ||
option1==="" ||
option2==="" ||
option3==="" ||
option4==="" ||
answer===""){

alert("Fill all fields");
return;

}

let newQ={

question:question,
options:[option1,option2,option3,option4],
answer:answer

};

quizData[subject].push(newQ);

localStorage.setItem(
"customQuiz",
JSON.stringify(quizData)
);

alert("Question Added Successfully");

/* CLEAR INPUTS */

document.getElementById("newQuestion").value="";
document.getElementById("option1").value="";
document.getElementById("option2").value="";
document.getElementById("option3").value="";
document.getElementById("option4").value="";
document.getElementById("correctAnswer").value="";

}

/* QUIZ */

let currentQuiz=[];
let currentQuestion=0;
let score=0;
let selectedAnswer="";
let timer;
let timeLeft=20;
let answers=[];

function startQuiz(subject){

document.getElementById("home").style.display="none";
document.getElementById("quizBox").style.display="block";

/* RANDOM 10 QUESTIONS */

let allQuestions=[...quizData[subject]];

allQuestions.sort(()=>Math.random()-0.5);

currentQuiz=allQuestions.slice(0,10);

currentQuestion=0;
score=0;
answers=[];

loadQuestion();

}

function loadQuestion(){

resetTimer();

selectedAnswer="";

let q=currentQuiz[currentQuestion];

document.getElementById("question").innerHTML=
(currentQuestion+1)+". "+q.question;

let html="";

q.options.forEach(option=>{

html+=`
<button onclick="selectAnswer(this,'${option}')">
${option}
</button>
`;

});

document.getElementById("options").innerHTML=html;

updateProgress();

}

function selectAnswer(button,answer){

selectedAnswer=answer;

let buttons=document.querySelectorAll(".options button");

buttons.forEach(btn=>btn.classList.remove("selected"));

button.classList.add("selected");

}

function nextQuestion(){

if(selectedAnswer===""){

alert("Select Answer");
return;

}

answers.push({

question:currentQuiz[currentQuestion].question,
your:selectedAnswer,
correct:currentQuiz[currentQuestion].answer

});

if(selectedAnswer===currentQuiz[currentQuestion].answer){
score++;
}

document.getElementById("score").innerText=
"Score: "+score;

currentQuestion++;

if(currentQuestion<currentQuiz.length){

loadQuestion();

}else{

showResult();

}

}

function showResult(){

clearInterval(timer);

document.getElementById("quizBox").style.display="none";
document.getElementById("result").style.display="block";

let percent=(score/currentQuiz.length)*100;

document.getElementById("finalScore").innerHTML=
`
Your Score: ${score}/${currentQuiz.length}<br>
Percentage: ${percent}%
`;

let reviewHTML="<ul>";

answers.forEach(item=>{

reviewHTML+=`
<li>
<b>Question:</b> ${item.question}<br>
<b>Your Answer:</b> ${item.your}<br>
<b>Correct:</b> ${item.correct}
</li>
`;

});

reviewHTML+="</ul>";

document.getElementById("review").innerHTML=
reviewHTML;

}

function resetTimer(){

clearInterval(timer);

timeLeft=20;

document.getElementById("timer").innerText=
"Time: "+timeLeft;

timer=setInterval(()=>{

timeLeft--;

document.getElementById("timer").innerText=
"Time: "+timeLeft;

if(timeLeft<=0){

clearInterval(timer);

nextQuestion();

}

},1000);

}

function updateProgress(){

let progress=
((currentQuestion+1)/currentQuiz.length)*100;

document.getElementById("progressBar").style.width=
progress+"%";

}

function goHome(){

document.getElementById("result").style.display="none";
document.getElementById("home").style.display="block";

}

function goHomeFromAdmin(){

document.getElementById("adminPanel").style.display="none";
document.getElementById("home").style.display="block";

}

/* FLASH CARDS */

const flashData={

math:[
{q:"5+7",a:"12"},
{q:"Square root of 81",a:"9"},
{q:"10×5",a:"50"},
{q:"Area of square",a:"a²"},
{q:"Cube of 3",a:"27"}
],

science:[
{q:"Water Formula",a:"H2O"},
{q:"Red Planet",a:"Mars"},
{q:"Largest organ",a:"Skin"},
{q:"Force SI Unit",a:"Newton"},
{q:"Sun is a",a:"Star"}
],

computer:[
{q:"Brain of Computer",a:"CPU"},
{q:"HTML stands for",a:"Hyper Text Markup Language"},
{q:"Shortcut for Copy",a:"Ctrl+C"},
{q:"WWW stands for",a:"World Wide Web"},
{q:"Browser language",a:"JavaScript"}
]

};

function openFlashcards(){

document.getElementById("home").style.display="none";
document.getElementById("flashcards").style.display="block";

loadFlashcards();

}

function loadFlashcards(){

let subject=
document.getElementById("flashSubject").value;

let cards=flashData[subject];

let html="";

cards.forEach((card,index)=>{

html+=`
<div class="flash-card"
onclick="flipCard(${index},'${subject}')"
id="card${index}">
${card.q}
</div>
`;

});

document.getElementById("flashCardBox").innerHTML=
html;

}

function flipCard(index,subject){

let card=flashData[subject][index];

let element=document.getElementById("card"+index);

if(element.innerHTML===card.q){

element.innerHTML=
`<span class="flash-answer">${card.a}</span>`;

}else{

element.innerHTML=card.q;

}

}

function goHomeFromFlash(){

document.getElementById("flashcards").style.display="none";
document.getElementById("home").style.display="block";

}

</script>

</body>
</html>
