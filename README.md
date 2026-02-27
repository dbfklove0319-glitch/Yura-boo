<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>YURA CUT</title>

<style>
body {
  text-align: center;
  background: #ffffff;
  font-family: Arial, sans-serif;
}

video {
  width: 260px;
  border-radius: 20px;
}

button {
  padding: 10px 18px;
  margin: 10px;
  font-size: 15px;
  border-radius: 8px;
  border: none;
  background: black;
  color: white;
}

#result img {
  width: 260px;
  margin-top: 20px;
}
</style>
</head>

<body>

<h2>YURA CUT 📸</h2>

<video id="video" autoplay playsinline></video>
<canvas id="canvas" style="display:none;"></canvas>

<br>
<button onclick="startBooth()">4컷 촬영</button>

<div id="result"></div>

<script>
const video = document.getElementById("video");
const canvas = document.getElementById("canvas");
const result = document.getElementById("result");

navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } })
.then(stream => {
  video.srcObject = stream;
});

async function startBooth() {
  let images = [];

  for (let i = 0; i < 4; i++) {
    await countdown(3);
    images.push(capture());
  }

  combineImages(images);
}

function countdown(seconds) {
  return new Promise(resolve => {
    let count = seconds;
    const interval = setInterval(() => {
      document.querySelector("h2").innerText = count;
      count--;
      if (count < 0) {
        clearInterval(interval);
        document.querySelector("h2").innerText = "YURA CUT 📸";
        resolve();
      }
    }, 1000);
  });
}

function capture() {
  const context = canvas.getContext("2d");
  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;
  context.drawImage(video, 0, 0);
  return canvas.toDataURL("image/png");
}

function combineImages(images) {
  const finalCanvas = document.createElement("canvas");
  const ctx = finalCanvas.getContext("2d");

  const width = 400;
  const height = 1000;

  finalCanvas.width = width;
  finalCanvas.height = height;

  // 흰 배경
  ctx.fillStyle = "white";
  ctx.fillRect(0, 0, width, height);

  const photoAreaHeight = 760;
  const imgHeight = photoAreaHeight / 4;
  const startY = 120;

  let loaded = 0;
  const imgs = [];

  images.forEach((src, index) => {
    const img = new Image();
    img.src = src;
    img.onload = () => {
      imgs[index] = img;
      loaded++;

      if (loaded === 4) {
        imgs.forEach((image, i) => {
          ctx.drawImage(image, 20, startY + i * imgHeight, width - 40, imgHeight - 10);
        });

        // 상단 제목
        ctx.fillStyle = "black";
        ctx.font = "bold 28px Arial";
        ctx.textAlign = "center";
        ctx.fillText("YURA BIRTHDAY CAFE", width / 2, 60);

        // 날짜 자동
        const today = new Date();
        const dateText = today.getFullYear() + "." +
          String(today.getMonth()+1).padStart(2,"0") + "." +
          String(today.getDate()).padStart(2,"0");

        ctx.font = "18px Arial";
        ctx.fillText(dateText, width / 2, height - 60);

        // 인스타 아이디
        ctx.fillText("@yura_cut", width / 2, height - 30);

        const finalImage = finalCanvas.toDataURL("image/png");
        result.innerHTML =
          `<img src="${finalImage}">
           <br>
           <a href="${finalImage}" download="yura_cut.png">다운로드</a>`;
      }
    };
  });
}
</script>

</body>
</html>
