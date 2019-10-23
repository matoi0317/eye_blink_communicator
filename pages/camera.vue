<template>
  <div>
      <div v-if="isCamera === false" style="background: #ff5555;">
          <div>カメラが起動していません</div>
          <p v-if="cameraErr">{{ cameraErr }}</p>
      </div>
      <div class="camera_title">
          <p>撮影してください</p>
      </div>
      <div>
          <!--
          <div>left_top: {{ leftTop }}</div> 
          <div>left_bottom: {{ leftBottom }}</div>
          <div>left_open: {{ leftOpen }}</div>
          <div>right_top: {{ rightTop }}</div>
          <div>right_bottom:{{ rightBottom }}</div>
          <div>right_open: {{ rightOpen }}</div>
          <div>currnetRate: {{ currentRate }}</div>
          <div><span :style="{ color: openEyeLabel == '開' ? '#f00' : '#000' }">{{ openEyeLabel }}</span>: {{ eyeBlinkCount }}回</div>
          <div>mouse_top: {{ mouseTop }}</div>
          <div>mouse_bottom: {{ mouseBottom }}</div>
          <div>mouse_open: {{ mouseOpen }}</div>
          <div>current_mouse: {{ currentMouseRate }}</div>
          <div><span :style="{ color: openMouseLabel == 'あ' ? '#f00' : '#000' }">{{ openMouseLabel }}</span>: {{ mouseCount }}回</div>
          <div><button @click="start">スタート</button></div>
          -->
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
                        if(this.isMajor2) {
                            this.mouseCount = this.mouseCount + 1
                            this.isSurvey = true
                            setTimeout(() => {
                                this.isSurvey = false
                            }, 500)
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
            const n = m * 3
            if (bottom2 - top2 < n) {
                return false
            }
            return true
        },
        start() {
            this.eyeBlinkCount = 0
            this.mouseCount = 0
            this.isMajor = true
            this.isMajor2 = true
            this.currentRate = this.leftOpen;
            this.currentMouseRate = this.mouseOpen;
            setTimeout(() => {
                alert("測定終了" + this.eyeBlinkCount + "回瞬きを検知しました。")
                if(this.eyeBlinkCount == 0){
                }
                if(this.eyeBlinkCount == 1){
                    alert(this.$cookies.get("message1"))
                }
                if(this.eyeBlinkCount == 2){
                    alert(this.$cookies.get("message2"))
                }
                if(this.eyeBlinkCount == 3){
                    alert(this.$cookies.get("message3"))
                }
                if(this.eyeBlinkCount == 4){
                    alert(this.$cookies.get("message4"))
                }
                if(this.eyeBlinkCount == 5){
                    alert(this.$cookies.get("message5"))
                }
                if(this.eyeBlinkCount == 6){
                    alert(this.$cookies.get("message6"))
                }
                if(this.eyeBlinkCount == 7){
                    alert(this.$cookies.get("message7"))
                }
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

.title p{
    position: absolute;
    font-size: 30px;
    top: 0px;
    left: 150px;
    color: white;
    font-weight: bold;
}
.video{
    border: solid 2px lightseagreen;
}
</style>
