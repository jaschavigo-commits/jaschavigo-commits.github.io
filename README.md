<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Aviation Career Personality Assessment</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      min-height: 100vh;
      padding: 20px;
      color: #333;
    }
    .container {
      max-width: 950px;
      margin: auto;
      background: #fff;
      border-radius: 20px;
      overflow: hidden;
      box-shadow: 0 15px 30px rgba(0,0,0,0.2);
    }
    .header {
      background: linear-gradient(45deg, #2c5aa0, #1e3c72);
      color: white;
      padding: 40px;
      text-align: center;
    }
    .header h1 { font-size: 2.5rem; margin-bottom: 10px; }
    .instructions {
      background: #e8f4fd;
      border-left: 5px solid #2196f3;
      padding: 20px;
      margin: 20px;
      border-radius: 10px;
    }
    .content { padding: 20px 40px; }
    .question-container { margin-bottom: 20px; }
    .question-text { margin-bottom: 10px; font-weight: 500; }
    .yes-no-container { display: flex; gap: 15px; }
    .yes-no-option {
      flex: 1;
      text-align: center;
      padding: 10px;
      border: 2px solid #ccc;
      border-radius: 25px;
      cursor: pointer;
      background: #fff;
      font-weight: bold;
      transition: 0.3s;
    }
    .yes-no-option:hover { background: #f0f4ff; border-color: #667eea; }
    .yes-no-option.selected { background: #667eea; color: #fff; border-color: #667eea; }
    input[type="radio"] { display: none; }
    .progress-container {
      height: 10px;
      background: #eee;
      margin: 20px 0;
      border-radius: 5px;
    }
    .progress-fill {
      height: 100%; width: 0%;
      background: linear-gradient(90deg, #667eea, #764ba2);
      border-radius: 5px;
      transition: width 0.3s;
    }
    .progress-text { text-align: center; margin-bottom: 20px; color: #555; }
    .submit-btn {
      display: block; margin: 20px auto;
      padding: 15px 40px;
      border: none; border-radius: 12px;
      background: linear-gradient(45deg, #667eea, #764ba2);
      color: white; font-size: 1.2rem;
      cursor: pointer; transition: 0.3s;
    }
    .submit-btn:disabled { opacity: 0.5; cursor: not-allowed; }
    .results { display: none; padding: 40px; }
    .personality-type {
      background: linear-gradient(135deg, #667eea, #764ba2);
      color: white; text-align: center;
      padding: 30px; border-radius: 15px;
      margin-bottom: 20px;
    }
    .career-path { background: #f9f9f9; padding: 15px; margin: 10px 0; border-left: 5px solid #4caf50; border-radius: 8px; }
    .reset-btn { display: block; margin: 20px auto; padding: 12px 30px; border: none; border-radius: 8px; background: #555; color: #fff; cursor: pointer; }
    .reset-btn:hover { background: #333; }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>Aviation Career Personality Assessment</h1>
      <p>Discover your ideal aviation career path ✈️</p>
    </div>
    <div class="instructions">
      <h3>📋 Instructions</h3>
      <p>Answer each question honestly with <b>YES</b> or <b>NO</b>. Your answers will determine your strongest aviation career match.</p>
    </div>
    <div class="content">
      <div class="progress-container"><div class="progress-fill" id="progressFill"></div></div>
      <div class="progress-text" id="progressText">0 of 40 questions answered</div>
      <form id="assessmentForm">
        <div class="question-container" id="questions"></div>
        <button type="submit" class="submit-btn" id="submitBtn" disabled>See My Results</button>
      </form>
      <div class="results" id="results">
        <div class="personality-type">
          <h2 id="personalityTitle">Your Career Match</h2>
          <p id="matchPercentage"></p>
        </div>
        <div id="careerPaths"></div>
        <button class="reset-btn" onclick="resetAssessment()">Take Again</button>
      </div>
    </div>
  </div>

<script>
const totalQuestions = 40;
let answeredQuestions = new Set();

// Full 40-question list
const questionList = [
  "I enjoy working with my hands to fix or build things.",
  "I prefer working independently rather than in large teams.",
  "I am comfortable making quick decisions under pressure.",
  "I enjoy analyzing data and finding patterns.",
  "I naturally take charge in group situations.",
  "I prefer following established procedures rather than improvising.",
  "I enjoy interacting with customers or passengers.",
  "I am drawn to understanding how complex systems work.",
  "I thrive in high-stress, fast-paced environments.",
  "I prefer detailed, systematic approaches to problem-solving.",
  "I enjoy coordinating multiple tasks simultaneously.",
  "I am comfortable with long periods of focused concentration.",
  "I enjoy teaching or training others.",
  "I am energized by working in team environments.",
  "I prefer using proven methods rather than experimental approaches.",
  "I am comfortable with irregular schedules and travel.",
  "I enjoy creating and implementing new procedures.",
  "I am detail-oriented and rarely overlook important information.",
  "I enjoy the responsibility that comes with making important decisions.",
  "I prefer working with concrete, tangible objects rather than abstract concepts.",
  "I am comfortable communicating clearly under stressful conditions.",
  "I enjoy managing budgets and financial resources.",
  "I am fascinated by how technology and engineering principles work.",
  "I prefer routine, predictable work schedules.",
  "I enjoy investigating and preventing potential safety hazards.",
  "I am comfortable with spatial reasoning and three-dimensional thinking.",
  "I enjoy planning and organizing complex projects.",
  "I am comfortable making decisions with incomplete information.",
  "I prefer working behind the scenes rather than being in the spotlight.",
  "I am good at multitasking and managing multiple priorities.",
  "I enjoy working with mathematical calculations and formulas.",
  "I am comfortable delegating tasks to others.",
  "I enjoy troubleshooting and diagnosing mechanical problems.",
  "I am motivated by helping ensure the safety of others.",
  "I prefer working in controlled, indoor environments.",
  "I enjoy continuous learning and staying updated with industry changes.",
  "I am comfortable with physical demands and manual labor.",
  "I enjoy networking and building professional relationships.",
  "I am comfortable working with regulatory requirements and compliance.",
  "I thrive when given autonomy and independence in my work."
];

// Render questions
const questionsDiv = document.getElementById('questions');
questionList.forEach((q, i) => {
  const id = `q${i+1}`;
  questionsDiv.innerHTML += `
    <div class="question-text">${i+1}. ${q}</div>
    <div class="yes-no-container">
      <div class="yes-no-option" onclick="selectOption(this,'${id}','yes')">
        <input type="radio" name="${id}" value="yes">YES
      </div>
      <div class="yes-no-option" onclick="selectOption(this,'${id}','no')">
        <input type="radio" name="${id}" value="no">NO
      </div>
    </div><br>
  `;
});

// Selection
function selectOption(el, qId, val) {
  const options = el.parentNode.querySelectorAll('.yes-no-option');
  options.forEach(opt => opt.classList.remove('selected'));
  el.classList.add('selected');
  el.querySelector('input').checked = true;
  answeredQuestions.add(qId);
  updateProgress();
}
function updateProgress() {
  const progress = (answeredQuestions.size/totalQuestions)*100;
  document.getElementById('progressFill').style.width = progress + '%';
  document.getElementById('progressText').textContent = `${answeredQuestions.size} of ${totalQuestions} answered`;
  document.getElementById('submitBtn').disabled = answeredQuestions.size < totalQuestions;
}

// Careers
const careers = {
  "Aircraft Maintenance": {score:0, max:7},
  "Pilot Training": {score:0, max:7},
  "Air Traffic Management": {score:0, max:7},
  "Flight Operations": {score:0, max:7},
  "Aviation Safety": {score:0, max:7},
  "Airport & Airline Management": {score:0, max:7},
  "Aviation Engineering": {score:0, max:7}
};

// Scoring Matrix
const scoringRules = {
  // Aircraft Maintenance
  q1:['Aircraft Maintenance'], q15:['Aircraft Maintenance'], q20:['Aircraft Maintenance'],
  q25:['Aircraft Maintenance'], q33:['Aircraft Maintenance'], q34:['Aircraft Maintenance'], q37:['Aircraft Maintenance'],
  // Pilot Training
  q3:['Pilot Training'], q9:['Pilot Training'], q16:['Pilot Training'], q19:['Pilot Training'],
  q21:['Pilot Training'], q26:['Pilot Training'], q36:['Pilot Training'],
  // Air Traffic Management
  q3:['Air Traffic Management'], q9:['Air Traffic Management'], q11:['Air Traffic Management'],
  q18:['Air Traffic Management'], q21:['Air Traffic Management'], q30:['Air Traffic Management'], q34:['Air Traffic Management'],
  // Flight Operations
  q7:['Flight Operations'], q11:['Flight Operations'], q14:['Flight Operations'],
  q27:['Flight Operations'], q30:['Flight Operations'], q32:['Flight Operations'], q38:['Flight Operations'],
  // Aviation Safety
  q4:['Aviation Safety'], q10:['Aviation Safety'], q13:['Aviation Safety'],
  q18:['Aviation Safety'], q25:['Aviation Safety'], q34:['Aviation Safety'], q39:['Aviation Safety'],
  // Airport & Airline Management
  q5:['Airport & Airline Management'], q7:['Airport & Airline Management'], q14:['Airport & Airline Management'],
  q22:['Airport & Airline Management'], q27:['Airport & Airline Management'], q32:['Airport & Airline Management'], q38:['Airport & Airline Management'],
  // Aviation Engineering
  q4:['Aviation Engineering'], q8:['Aviation Engineering'], q23:['Aviation Engineering'],
  q26:['Aviation Engineering'], q31:['Aviation Engineering'], q36:['Aviation Engineering'], q40:['Aviation Engineering']
};

// Submit
document.getElementById('assessmentForm').addEventListener('submit', e=>{
  e.preventDefault();
  calculateResults();
});
function calculateResults(){
  Object.keys(careers).forEach(c=>careers[c].score=0);
  const formData = new FormData(document.getElementById('assessmentForm'));
  for(let [q,ans] of formData.entries()){
    if(ans==='yes' && scoringRules[q]){
      scoringRules[q].forEach(c=>careers[c].score++);
    }
  }
  let topScore = Math.max(...Object.values(careers).map(c=>c.score));
  let topCareer = Object.keys(careers).find(c=>careers[c].score===topScore);
  displayResults(topCareer,topScore);
}
function displayResults(topCareer,topScore){
  document.getElementById('assessmentForm').style.display='none';
  document.getElementById('results').style.display='block';
  document.getElementById('personalityTitle').textContent = `Top Match: ${topCareer}`;
  document.getElementById('matchPercentage').textContent = `${topScore}/7 points`;
  const careerPaths = document.getElementById('careerPaths');
  careerPaths.innerHTML='';
  Object.keys(careers).sort((a,b)=>careers[b].score-careers[a].score).forEach(c=>{
    careerPaths.innerHTML += `<div class="career-path"><h4>${c}</h4><p>${careers[c].score} pts</p></div>`;
  });
}
function resetAssessment(){
  answeredQuestions.clear();
  document.getElementById('assessmentForm').reset();
  document.getElementById('assessmentForm').style.display='block';
  document.getElementById('results').style.display='none';
  document.getElementById('progressFill').style.width='0%';
  document.getElementById('progressText').textContent=`0 of ${totalQuestions} answered`;
  document.getElementById('submitBtn').disabled=true;
  document.querySelectorAll('.yes-no-option').forEach(opt=>opt.classList.remove('selected'));
}
</script>
</body>
</html>
