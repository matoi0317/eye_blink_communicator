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
        <input type="text" placeholder="1回" v-model="message1">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="2回" v-model="message2">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="3回" v-model="message3">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="4回" v-model="message4">
        </label>
      </div>
       <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="5回" v-model="message5">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="6回" v-model="message6">
        </label>
      </div>
      <div class="cp_iptxt">
        <label class="ef">
        <input type="text" placeholder="7回" v-model="message7"> 
        </label>
      </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      message1: this.$cookies.get("message1"),
      message2: this.$cookies.get("message2"),
      message3: this.$cookies.get("message3"),
      message4: this.$cookies.get("message4"),
      message5: this.$cookies.get("message5"),
      message6: this.$cookies.get("message6"),
      message7: this.$cookies.get("message7"),
    }
  },
  mounted() {
  },
  methods:{
    save(){
      console.log("aaaa", this.message1)
      const maxAge = 60 * 60 * 24 * 365 * 10
      this.$cookies.set("message1", this.message1, {
        maxAge: maxAge,
      })
      console.log("bbbb", this.message2)
      this.$cookies.set("message2", this.message2, {
        maxAge: maxAge,
      })
      console.log("cccc", this.message3)
      this.$cookies.set("message3", this.message3, {
        maxAge: maxAge,
      })
      console.log("dddd", this.message4)
      this.$cookies.set("message4", this.message4, {
        maxAge: maxAge,
      })
      console.log("eeee", this.message5)
      this.$cookies.set("message5", this.message5, {
        maxAge: maxAge,
      })
      console.log("ffff", this.message6)
      this.$cookies.set("message6", this.message6, {
        maxAge: maxAge,
      })
      console.log("gggg", this.message7)
      this.$cookies.set("message7", this.message7, {
        maxAge: maxAge,
      })
    }
  }
}
</script>

<style>
.setting-title{
    width: 100%;
    height: 50px;
    background-color: lightseagreen;
    opacity: 0.5; /*透明度*/
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
	color: #000;
  font-weight: bold;
	border: 1px solid #1b2538;
	border-radius: 4px;
}
.ef input[type='text']:focus {
	border: 1px solid #da3c41;
	outline: none;
	box-shadow: 0 0 5px 1px rgba(218,60,65, .5);
}

</style>
