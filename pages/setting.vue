<template>
  <div>
      <div class="setting-title">
          <p>設定</p>
      </div>
      <div class="keep">
          <p @click="save">保存</p>
      </div>
      <nuxt-link class="back" :to="{path:'./'}">◀︎back</nuxt-link>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="1回">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="2回">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="3回">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="4回">
        </label>
      </div>
       <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="5回">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="6回">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="7回">
        </label>
      </div>

  </div>
</template>

<script>
export default {
    methods:{
        save(){
            alert("aaaa")
        }
    }
}
</script>

<style>
.setting-title{
    width: 100%;
    height: 50px;
    background-color: lightseagreen;
    opacity: 0.5;
}

.setting-title p{
    position: absolute;
    font-size: 30px;
    top: 0px;
    left: 150px;
    color: white;
    font-weight: bold;
}

.back {
    position: absolute;
    left: 0px;
    top: 0px;
    font-weight: bold;
    font-size: 30px;
    color: white;
}

.keep {
    position: absolute;
    font-weight: bold;
    font-size: 30px;
    color: white;
    top: 0px;
    left: 300px;
}

.cp_iptxt {
	position: relative;
	width: 90%;
	margin: 40px 3%;
}
.cp_iptxt input[type='text'] {
	font: 15px/24px sans-serif;
	box-sizing: border-box;
	width: 100%;
	padding: 0.3em;
	transition: 0.3s;
	letter-spacing: 1px;
	color: #aaaaaa;
	border: 1px solid #1b2538;
	border-radius: 4px;
}
.ef input[type='text']:focus {
	border: 1px solid #da3c41;
	outline: none;
	box-shadow: 0 0 5px 1px rgba(218,60,65, .5);
}

</style>
