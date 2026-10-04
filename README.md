<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Roulette Solo</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #10131c;
  color: white;
  font-family: Arial, sans-serif;
  text-align: center;
}

h1 {
  margin: 20px 0 5px;
}

#coins {
  font-size: 22px;
  color: #ffd700;
  margin-bottom: 15px;
}

/* ルーレット */
.wheel-area {
  position: relative;
  width: 320px;
  height: 320px;
  margin: 20px auto;
}

#wheel {
  width: 320px;
  height: 320px;
  border-radius: 50%;
  border: 10px solid gold;

  background:
    conic-gradient(
      #c62828 0deg 10deg,
      #111 10deg 20deg,
      #c62828 20deg 30deg,
      #111 30deg 40deg,
      #c62828 40deg 50deg,
      #111 50deg 60deg,
      #c62828 60deg 70deg,
      #111 70deg 80deg,
      #c62828 80deg 90deg,
      #111 90deg 100deg,
      #c62828 100deg 110deg,
      #111 110deg 120deg,
      #c62828 120deg 130deg,
      #111 130deg 140deg,
      #c62828 140deg 150deg,
      #111 150deg 160deg,
      #c62828 160deg 170deg,
      #111 170deg 180deg,
      #c62828 180deg 190deg,
      #111 190deg 200deg,
      #c62828 200deg 210deg,
      #111 210deg 220deg,
      #c62828 220deg 230deg,
      #111 230deg 240deg,
      #c62828 240deg 250deg,
      #111 250deg 260deg,
      #c62828 260deg 270deg,
      #111 270deg 280deg,
      #c62828 280deg 290deg,
      #111 290deg 300deg,
      #c62828 300deg 310deg,
      #111 310deg 320deg,
      #c62828 320deg 330deg,
      #111 330deg 340deg,
      #087f55 340deg 360deg
    );

  transition: transform 4s cubic-bezier(0.12, 0.8, 0.15, 1);
}

.pointer {
  position: absolute;
  z-index: 5;

  top: -5px;
  left: 50%;

  transform: translateX(-50%);

  width: 0;
  height: 0;

  border-left: 15px solid transparent;
  border-right: 15px solid transparent;
  border-top: 35px solid white;
}

/* 結果 */
#result {
  font-size: 24px;
  font-weight: bold;
  margin: 15px;
}

#message {
  color: #aaa;
  min-height: 25px;
}

/* 操作 */
.panel {
  width: min(500px, 92%);
  margin: auto;
}

input {
  width: 100%;
  padding: 13px;
  border-radius: 10px;
  border: 2px solid #444;
  background: #191d28;
  color: white;
  font-size: 18px;
  text-align: center;
}

.buttons {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin-top: 10px;
}

button {
  padding: 13px;
  border: none;
  border-radius: 10px;
  background: #252b3a;
  color: white;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
}

button:active {
  transform: scale(0.96);
}

button.selected {
  background: #d89b00;
}

#spin {
  width: 100%;
  margin-top: 15px;

  background: linear-gradient(
    135deg,
    #ffd700,
    #ff9500
  );

  color: #111;
  font-size: 22px;
}

#spin:disabled {
  opacity: 0.5;
}

/* ランキング */
#ranking {
  margin: 25px auto;
  width: min(500px, 92%);

  background: #191d28;
  padding: 15px;
  border-radius: 15px;
}

#ranking ol {
  text-align: left;
  padding-left: 35px;
}

#ranking li {
  padding: 8px;
  border-bottom: 1px solid #333;
}
</style>
</head>

<body>

<h1>🎰 ROULETTE</h1>

<div id="coins">
🪙 100,000
</div>

<div class="wheel-area">

  <div class="pointer"></div>

  <div id="wheel"></div>

</div>

<div id="result">
🎰 SPINしてね
</div>

<div id="message"></div>

<div class="panel">

  <input
    id="bet"
    type="number"
    value="5000"
    min="5000"
    step="1000"
  >

  <div class="buttons">

    <button id="red">
      🔴 赤 ×2
    </button>

    <button id="black">
      ⚫ 黒 ×2
    </button>

    <button id="even">
      偶数 ×2
    </button>

    <button id="odd">
      奇数 ×2
    </button>

  </div>

  <button id="spin">
    🎰 SPIN
  </button>

</div>

<div id="ranking">

  <h2>🏆 ランキング</h2>

  <ol id="rankingList"></ol>

</div>

<script>

let coins = 100000;

let bestCoins = 100000;

let selected = "red";

let spinning = false;

let rotation = 0;


/*
  赤の数字
*/
const redNumbers = [
  1,3,5,7,9,
  12,14,16,18,
  19,21,23,25,27,
  30,32,34,36
];


/*
  HTML要素を取得
*/
const wheel =
  document.getElementById("wheel");

const coinDisplay =
  document.getElementById("coins");

const result =
  document.getElementById("result");

const message =
  document.getElementById("message");

const betInput =
  document.getElementById("bet");

const spinButton =
  document.getElementById("spin");

const rankingList =
  document.getElementById("rankingList");


/*
  コイン表示
*/
function updateCoins() {

  coinDisplay.textContent =
    "🪙 " + coins.toLocaleString();

}


/*
  選択ボタン
*/
function select(type) {

  selected = type;

  document
    .querySelectorAll(".buttons button")
    .forEach(button => {

      button.classList.remove("selected");

    });

  document
    .getElementById(type)
    .classList.add("selected");

}


/*
  色判定
*/
function getColor(number) {

  if (number === 0) {
    return "green";
  }

  if (redNumbers.includes(number)) {
    return "red";
  }

  return "black";

}


/*
  勝敗判定
*/
function checkWin(number) {

  const color =
    getColor(number);

  if (selected === "red") {

    return color === "red";

  }

  if (selected === "black") {

    return color === "black";

  }

  if (selected === "even") {

    return number !== 0 &&
           number % 2 === 0;

  }

  if (selected === "odd") {

    return number % 2 === 1;

  }

  return false;

}


/*
  ルーレット
*/
function spin() {

  if (spinning) {
    return;
  }

  const bet =
    Number(betInput.value);


  /*
    ベットチェック
  */
  if (
    !Number.isFinite(bet) ||
    bet < 5000
  ) {

    message.textContent =
      "⚠️ 最低5,000コインです";

    return;

  }


  if (bet > coins) {

    message.textContent =
      "⚠️ コインが足りません";

    return;

  }


  /*
    スタート
  */
  spinning = true;

  spinButton.disabled = true;

  spinButton.textContent =
    "🎰 回転中...";

  message.textContent =
    "";


  /*
    ベット分を減らす
  */
  coins -= bet;

  updateCoins();


  /*
    0〜36
  */
  const number =
    Math.floor(
      Math.random() * 37
    );


  /*
    ルーレットを回す
  */
  rotation +=
    360 * 8 +
    Math.random() * 360;

  wheel.style.transform =
    "rotate(" +
    rotation +
    "deg)";


  /*
    回転終了
  */
  setTimeout(() => {

    const color =
      getColor(number);

    const win =
      checkWin(number);


    let reward = 0;


    if (win) {

      reward =
        bet * 2;

      coins += reward;

      result.textContent =
        "🎉 " +
        number +
        " — " +
        (
          color === "red"
            ? "🔴 赤"
            : "⚫ 黒"
        );

      message.textContent =
        "+" +
        reward.toLocaleString() +
        " コイン！";

    } else {

      result.textContent =
        "😢 " +
        number +
        " — " +
        (
          color === "green"
            ? "🟢 緑"
            : color === "red"
              ? "🔴 赤"
              : "⚫ 黒"
        );

      message.textContent =
        "-" +
        bet.toLocaleString() +
        " コイン";

    }


    /*
      最高コイン
    */
    if (coins > bestCoins) {

      bestCoins =
        coins;

    }


    updateCoins();

    updateRanking();


    /*
      再びSPIN可能
    */
    spinning = false;

    spinButton.disabled = false;

    spinButton.textContent =
      "🎰 SPIN";


  }, 4100);

}


/*
  ランキング
*/
function updateRanking() {

  const rankings = [

    {
      name: "あなた",
      coins: bestCoins
    },

    {
      name: "PLAYER 2",
      coins: 85000
    },

    {
      name: "PLAYER 3",
      coins: 62000
    },

    {
      name: "PLAYER 4",
      coins: 45000
    }

  ];


  rankings.sort(
    (a,b) =>
      b.coins - a.coins
  );


  rankingList.innerHTML = "";


  rankings.forEach(
    (player,index) => {

      const li =
        document.createElement("li");

      li.textContent =
        player.name +
        " — " +
        player.coins.toLocaleString() +
        " コイン";

      rankingList.appendChild(li);

    }
  );

}


/*
  ボタン
*/
document
  .getElementById("red")
  .addEventListener(
    "click",
    () => select("red")
  );

document
  .getElementById("black")
  .addEventListener(
    "click",
    () => select("black")
  );

document
  .getElementById("even")
  .addEventListener(
    "click",
    () => select("even")
  );

document
  .getElementById("odd")
  .addEventListener(
    "click",
    () => select("odd")
  );

document
  .getElementById("spin")
  .addEventListener(
    "click",
    spin
  );


/*
  最初の状態
*/
select("red");

updateCoins();

updateRanking();

</script>

</body>
</html>
