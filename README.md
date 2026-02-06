class TypingGame {
  constructor(text) {
    this.text = text;
    this.index = 0;
    this.miss = 0;
    this.startTime = null;
  }

  start() {
    this.startTime = Date.now();
  }

  judge(char) {
    if (char === this.text[this.index]) {
      this.index++;
      return true;
    } else {
      this.miss++;
      return false;
    }
  }

  isFinished() {
    return this.index >= this.text.length;
  }

  getStats() {
    const time = (Date.now() - this.startTime) / 1000;
    const wpm = Math.round((this.text.length / 5) / (time / 60));
    const accuracy = Math.round(
      ((this.text.length - this.miss) / this.text.length) * 100
    );
    return { wpm, accuracy };
  }
}

// ====== DOM ======
const textEl = document.getElementById("text");
const inputEl = document.getElementById("input");
const missEl = document.getElementById("miss");
const wpmEl = document.getElementById("wpm");
const accEl = document.getElementById("accuracy");
const startBtn = document.getElementById("start");

let game;

// ====== 表示更新 ======
function render() {
  textEl.innerHTML = "";
  for (let i = 0; i < game.text.length; i++) {
    const span = document.createElement("span");
    span.textContent = game.text[i];

    if (i < game.index) span.className = "correct";
    else if (i === game.index) span.className = "current";

    textEl.appendChild(span);
  }

  missEl.textContent = game.miss;
  const stats = game.getStats();
  wpmEl.textContent = stats.wpm;
  accEl.textContent = stats.accuracy;
}

// ====== ゲーム開始 ======
async function startGame() {
  const res = await fetch("words.json");
  const words = await res.json();
  const text = words[Math.floor(Math.random() * words.length)];

  game = new TypingGame(text);
  game.start();

  inputEl.value = "";
  inputEl.disabled = false;
  inputEl.focus();

  render();
}

// ====== 入力処理 ======
inputEl.addEventListener("input", () => {
  const value = inputEl.value.slice(-1);
  game.judge(value);
  inputEl.value = "";

  if (game.isFinished()) {
    inputEl.disabled = true;
    alert("終了！");
  }

  render();
});

startBtn.addEventListener("click", startGame);
