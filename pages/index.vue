<template>
  <div>
    <div class="title">
      <p>Talk with you</p>
      <img src="./talking.jpg">
    </div>
    <div class="sub-title">あなたとしゃべりたい</div>
    <nuxt-link class="button run-camera" :to="{path:'/camera'}"> カメラを起動</nuxt-link>
    <nuxt-link class="setting-text" :to="{path:'/setting'}"> 設定を開く</nuxt-link>
    <div>ver. 1.1.1</div>
  </div>
</template>

<script>
export default {}
</script>

<style>
.title{
  position: relative; /*相対位置*/
}

.title p {
  position: absolute; /*自由にpositionが決められる */
  top: 240px; /*上からの位置*/
  left: 30px; /*左からの位置*/
  font-size: 50px; /*フォントのサイズ*/
  font-weight: bold; /*文字の太さ*/
  font-style: italic; /*斜め文字にする*/
  color: black; /*フォントの色*/
}

.title img {
  width: 100%; /*画像のサイズ*/
}

.sub-title {
  position: absolute;
  top: 250;
  right: 10px;
  font-size: 15px;
  color: black;
  font-weight: bold;
  font-style: italic;
}

.button {
  color:white;
}

.run-camera {
  position: absolute;
  top: 350px;
  left: 60px;
  font-size: 20px;
  width: 250px;
  height: 50px;
  background-color: lightseagreen;
}

.setting-text {
  position: absolute;
  color: lightseagreen;
  font-size: 20px;
  top: 410px;
  left: 130px;
  text-decoration: underline; /*下線を引く*/
}
</style>
