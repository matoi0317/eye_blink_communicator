<template>
  <div>
      <div v-if="isCamera === false" style="background: #ff5555;">
          <div>カメラが起動していません</div>
          <p v-if="cameraErr">{{ cameraErr }}</p>
      </div>
      <div>
          <!-- <div>left_top: {{ leftTop }}</div> -->
          <!--<div>left_bottom: {{ leftBottom }}</div>-->
          <div>left_open: {{ leftOpen }}</div>
          <!--<div>right_top: {{ rightTop }}</div>-->
          <!--<div>right_bottom:{{ rightBottom }}</div>-->
          <div>right_open: {{ rightOpen }}</div>
          <div>currnetRate: {{ currentRate }}</div>
          <div><span :style="{ color: openEyeLabel == '開' ? '#f00' : '#000' }">{{ openEyeLabel }}</span>: {{ eyeBlinkCount }}回</div>
          <div><button @click="start">スタート</button></div>
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
        }
    },
    mounted() {
        const constraints = navigator.mediaDevices.getSupportedConstraints();
        console.log(constraints)
        this.video = document.getElementById("v")
        const facingMode = { facingMode: "user" } // 内側
        // const facingMode = { facingMode: { exact: "environment" } } // 外側
        navigator.mediaDevices.getUserMedia({
            video: {
                ...facingMode
            },
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
            this.leftTop = Math.floor(position[24][1] * 100)
            this.leftBottom = Math.floor(position[26][1] * 100)
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
        },
        isOpen(top, bottom) {
            const s = this.currentRate
            const a = s * 0.845
            if (bottom - top > a) {
                return true
            }
            return false
        },
        start() {
            this.eyeBlinkCount = 0
            this.isMajor = true
            this.currentRate = this.leftOpen;
            setTimeout(() => {
                alert("測定終了" + this.eyeBlinkCount + "回瞬きを検知しました。")
                if(this.eyeBlinkCount == 1){
                    alert("YES")
                }
                if(this.eyeBlinkCount == 2){
                    alert("NO")
                }
                if(this.eyeBlinkCount == 3){
                    alert("あいう")
                }
                if(this.eyeBlinkCount == 4){
                    alert("kakiku")
                }
                if(this.eyeBlinkCount == 5){
                    alert("さしす")
                }
            }, 10000)
        },
    }
}
</script>


<style>
.video{
    border: solid 2px red;
}
</style>
