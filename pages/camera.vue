<template>
  <div>
      <div v-if="isCamera === false" style="background: #ff5555;">
          <div>カメラが起動していません</div>
          <p v-if="cameraErr">{{ cameraErr }}</p>
      </div>
      <div class="camera_title"></div>
      <div class="buttons">
          <button class="button set-camera" @click="a('button')">撮影</button>
      </div>
      <nuxt-link class="camera_back" :to="{path:'./'}">◀︎back</nuxt-link>
      <div v-if="isMajor" style="background:#ff0000;text-align: center;color:#fff;">
          計測中
      </div>
      <div v-if="mouseCountInterval" style="background:#ff8888;text-align: center;color:#fff;">
          起動準備中 {{mouseOpenCount}}/3
      </div>
      <div>
          <!--
          <div>left_top: {{ leftTop }}</div> 
          <div>left_bottom: {{ leftBottom }}</div>
          <div>left_open: {{ leftOpen }}</div>
          <div>right_top: {{ rightTop }}</div>
          <div>right_bottom:{{ rightBottom }}</div>
          <div>right_open: {{ rightOpen }}</div>
          -->
          <div>現在の値: {{ currentRate }}</div>
          <div><span :style="{ color: openEyeLabel == '開' ? '#f00' : '#000' }">{{ openEyeLabel }}</span>: {{ eyeBlinkCount }}回</div>
          <div>mouse_top: {{ mouseTop }}</div>
          <div>mouse_bottom: {{ mouseBottom }}</div>
          <div>mouse_open: {{ mouseOpen }}</div>
          <div>現在の値: {{ currentMouseRate }} {{ mouseCountInterval }}</div>
          <div>口を開けた回数: {{ mouseOpenCount }}</div>
          <div><span :style="{ color: openMouseLabel == 'あ' ? '#f00' : '#000' }">{{ openMouseLabel }}</span>: {{ mouseCount }}回</div>
      </div>
    <video id="v" width="640" height="480" class="video" autoplay playsinline></video>
    <canvas id="c" width="640" height="480"></canvas>
    <!--
    <script src="https://shimabox.github.io/face_recognition_with_clmtrackr/js/clmtrackr.min.js"></script>
    -->
  </div>
</template>

<script>
export default {
    data() {
        return {
            video: null,
            ctracker: null,
            leftTop: 0,
            leftBottom: 0,
            leftOpen: 0,
            rightTop: 0,
            rightBottom: 0,
            rightOpen: 0,
            openEyeLabel: "閉",
            eyeBlinkCount: 0,
            currentRate: 14,
            isMajor: false,
            isDetencting: false,
            isCamera: false,
            cameraErr: "",
            mouseTop: 0,
            mouseBottom: 0,
            mouseOpen: 0,
            openMouseLabel: "ん",
            mouseCount: 0,
            currentMouseRate: 14,
            isMajor2: false,
            isSurvey: false,
            mouseOpenCount: 0,
            mouseCountInterval: null,
        }
    },
    watch: {
        mouseOpenCount: function(val) {
            if (val >= 3 ) {
                this.a()
            }
        }
    },
    mounted() {
        console.log("test", this.$cookies.get("message1"))
        const constraints = navigator.mediaDevices.getSupportedConstraints();
        console.log(constraints)
        this.video = document.getElementById("v")
        // const facingMode = { facingMode: "user" } // 内側
        // const facingMode = { facingMode: { exact: "environment" } } // 外側
        navigator.mediaDevices.getUserMedia({
            // video: {
            //     ...facingMode
            // },
            video: true,
            audio: false
        })
        .then((stream) => {
            console.log("cam")
            this.isCamera = true
            this.video.srcObject=stream
            this.video.play()
            this.ctracker = new clm.tracker();
            this.ctracker.init();
            this.ctracker.start(this.video);
            this.loop()
        }).catch(err => {
            console.log(err)
            this.isCamera = false
        })
    },
    destroyed() {
        clearInterval(this.mouseCountInterval)
    },
    methods: {
        loop() {
            requestAnimationFrame(this.loop)
            const position = this.ctracker.getCurrentPosition()
            if (position === false) {
                return;
            }
            this.leftTop = Math.floor(position[24][1])
            this.leftBottom = Math.floor(position[26][1])
            this.leftOpen = this.leftBottom - this.leftTop
            this.rightTop = Math.floor(position[29][1])
            this.rightBottom  = Math.floor(position[31][1])
            this.rightOpen = this.rightBottom - this.rightTop
            const isOpen = this.isOpen(this.leftTop, this.leftBottom)
            if (isOpen === true) {
                if (this.openEyeLabel == "閉") {
                    if (this.isDetencting == false) {
                        if (this.isMajor) {
                            this.eyeBlinkCount = this.eyeBlinkCount + 1
                            this.isDetencting = true
                            setTimeout(() => {
                                this.isDetencting = false
                            }, 500)
                        }
                    }
                }
                this.openEyeLabel = "開"
            } else {
                this.openEyeLabel = "閉"
            }

            this.mouseTop = Math.floor(position[60][1])
            this.mouseBottom = Math.floor(position[57][1])
            this.mouseOpen = this.mouseBottom - this.mouseTop
            const isOpen2 = this.isOpen2(this.mouseTop, this.mouseBottom)
            if(isOpen2 == true) {
                if (this.openMouseLabel == "ん") {
                    if (this.isSurvey == false) {
                        this.mouseCount = this.mouseCount + 1
                        if (!this.isMajor) {
                          this.mouseOpenCount = this.mouseOpenCount + 1
                        }
                        this.isSurvey = true
                        setTimeout(() => {
                            this.isSurvey = false
                        }, 500)
                        if (this.mouseCountInterval === null && !this.isMajor) {
                            console.log("startInterval")
                            this.mouseCountInterval = setInterval(() => {
                                console.log("interval")
                                this.mouseOpenCount = 0;
                                clearInterval(this.mouseCountInterval)
                                this.mouseCountInterval = null
                            }, 5000)
                        }
                    }
                } 
                this.openMouseLabel = "あ"
            }else {
                this.openMouseLabel = "ん"
            }
        },
        isOpen(top, bottom) {
            const s = this.currentRate
            const a = s * 0.845
            if (bottom - top > a) {
                return false
            }
            return true
        },
        isOpen2(top2, bottom2) {
            const m = this.currentMouseRate
            const n = m * 2
            if (bottom2 - top2 < n) {
                return false
            }
            return true
        },
        a(from="") {
            console.log("=== 計測スタート ===")
            if (from === "button") {
                alert("撮影を開始します。画面に向かって瞬きをしてください。")
            }
            this.eyeBlinkCount = 0
            this.mouseCount = 0
            this.isMajor = true
            this.isMajor2 = true
            this.currentRate = this.leftOpen;
            this.currentMouseRate = this.mouseOpen;
            clearInterval(this.mouseCountInterval)
            this.mouseCountInterval = null
            setTimeout(() => {
                clearInterval(this.mouseCountInterval)
                this.mouseOpenCount = 0;
            }, 200)
            
            setTimeout(async () => {
                if (from == "button") {
                  alert("測定終了" + this.eyeBlinkCount + "回瞬きを検知しました。")
                }
                this.isMajor = false
                this.currentMouseRate = 14
                let message = "..."
                if(this.eyeBlinkCount > 7){
                    const m = "message" + this.eyeBlinkCount
                    if (from == "button") {
                      alert("計測できませんでした")
                    }
                    message = "計測できませんでした"
                } else {
                    const m = "message" + this.eyeBlinkCount
                    if (from == "button") {
                      alert(this.$cookies.get(m))
                    }
                    message = this.$cookies.get(m)
                }
                if(this.eyeBlinkCount == 1){
                    if (from == "button") {
                        alert(this.$cookies.get("message1"))
                    }
                    message = this.$cookies.get("message1")
                }
                if(this.eyeBlinkCount == 2){
                    if (from == "button") {
                        alert(this.$cookies.get("message2"))
                    }                    
                    message = this.$cookies.get("message2")
                }
                if(this.eyeBlinkCount == 3){
                    if (from == "button") {
                        alert(this.$cookies.get("message3"))
                    }
                    message = this.$cookies.get("message3")
                }
                if(this.eyeBlinkCount == 4){
                    if (from == "button"){
                        alert(this.$cookies.get("message4"))
                    }
                    message = this.$cookies.get("message4")
                }
                if(this.eyeBlinkCount == 5){
                    if (from == "button"){
                        alert(this.$cookies.get("message5"))
                    }
                    message = this.$cookies.get("message5")
                }
                if(this.eyeBlinkCount == 6){
                    if (from == "button"){
                        alert(this.$cookies.get("message6"))
                    }
                    message = this.$cookies.get("message6")
                }
                if(this.eyeBlinkCount == 7){
                    if (from == "button"){
                        alert(this.$cookies.get("message7"))
                    }
                    message = this.$cookies.get("message7")   
                }
                const response = await this.$axios.post('https://7a4c1754.ngrok.io/post', {
                    blinkCount: this.eyeBlinkCount,
                    message: message
                })
            }, 10000)
        },
    }
}
</script>


<style>
.camera_title {
    width: 100%;
    height: 50px;
    background-color: lightseagreen;
    opacity: 0.5; /*透明度*/
}

.camera_back {
    position: absolute;
    left: 0px;
    top: 0px;
    font-weight: bold;
    font-size: 30px;
    color: white;
}
.video{
    border: solid 2px lightseagreen;
}

.set-camera {
  position: absolute;
  top: 5px;
  left: 160px;
  font-size: 20px;
  width: 130px;
  height: 40px;
  background-color: lightseagreen;
  font-weight: bold;
}
</style>
