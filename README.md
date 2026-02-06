typing-game/
├─ index.html
├─ style.css
├─ main.js
├─ words/
│   ├─ easy.json
│   ├─ normal.json
│   └─ hard.json
├─ README.md
└─ .gitignore
const gameState = {
  text: "",
  index: 0,
  startTime: null,
  mistakes: 0,
  isPlaying: false
};
document.addEventListener("keydown", (e) => {
  if (!gameState.isPlaying) return;

  const expected = gameState.text[gameState.index];
  if (e.key === expected) {
    gameState.index++;
  } else {
    gameState.mistakes++;
  }

  if (gameState.index === gameState.text.length) {
    finishGame();
  }

  render();
});
function calcStats() {
  const time = (Date.now() - gameState.startTime) / 1000;
  const chars = gameState.text.length;
  const wpm = Math.round((chars / 5) / (time / 60));
  const accuracy = Math.max(
    0,
    Math.round(((chars - gameState.mistakes) / chars) * 100)
  );
  return { wpm, accuracy };
}
