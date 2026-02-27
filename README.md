<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>My Photo Booth</title>

<style>
body {
  text-align: center;
  background: #fce4ec;
  font-family: Arial;
}

video {
  width: 300px;
  border-radius: 15px;
  margin-top: 20px;
  transform: scaleX(-1); /* 화면 거울모드 */
}

button {
  margin: 5px;
  padding: 8px 12px;
  border-radius: 10px;
  border: none;
  background: #ff80ab;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

img {
  margin-top: 15px;
  width: 300px;
  border-radius: 15px;
}
</style>
</head>

<body>

<h2>💗 감성 포토부스 💗</h2>

<video id="video" autoplay playsinline></video>
<br>

<button onclick="takePhoto()">📸 사진찍기</button>

<div>
  <button onclick="setFilter('none')">기본 카메라</button>

  <button onclick="setFilter('contrast(110%) saturate(105%) brightness(102%)')">
    iPhone SE
  </button>

  <button onclick="setFilter('contrast(95%) saturate(85%) brightness(98%) sepia(10%)')">
    iPhone 6
  </button>
</div>

<canvas id="canvas" style="display:none;"></canvas>
<img id="photo">

<script>
const video = document.getElementById("video");
let currentFilter = "none";

navigator.mediaDevices.getUserMedia({ video: true })
.then(stream => {
  video.srcObject = stream;
})
.catch(err => {
  alert("카메라 허용을 눌러주세요!");
});

function setFilter(filter) {
  currentFilter = filter;
  video.style.filter = filter;
}

function takePhoto() {
  const canvas = document.getElementById("canvas");
  const context = canvas.getContext("2d");

  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;

  // 좌우반전 + 필터 동시에 적용
  context.save();
  context.scale(-1, 1);
  context.translate(-canvas.width, 0);
  context.filter = currentFilter;
  context.drawImage(video, 0, 0, canvas.width, canvas.height);
  context.restore();

  const imageData = canvas.toDataURL("image/png");
  document.getElementById("photo").src = imageData;
}
</script>

</body>
</html>
