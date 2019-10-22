<template>
  <div>
      <div>
          left_top: {{ leftTop }}
          left_bottom: {{ leftBottom }}
          left_open: {{ leftOpen }}
          right_top: {{ rightTop }}
          right_bottom:{{ rightBottom }}
          right_open: {{ rightOpen }}
          <div><span :style="{ color: openEyeLabel == '開' ? '#f00' : '#000' }">{{ openEyeLabel }}</span>: {{ eyeBlinkCount }}回</div>
          <div><button @click="start">スタート</button></div>
      </div>
    <video id="v" width="640" height="480" class="video" autoplay></video>
    <canvas id="c" width="640" height="480"></canvas>
    <script src="https://shimabox.github.io/face_recognition_with_clmtrackr/js/clmtrackr.min.js"></script>
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
        }
    },
    mounted() {
        this.video = document.getElementById("v")
        navigator.mediaDevices.getUserMedia({
            video: { facingMode: "environment", width: 320, height: 240 },
            audio: false
        })
        .then((stream) => {
            console.log("cam")
            this.video.srcObject=stream
            this.video.play()
            this.ctracker = new clm.tracker();
            this.ctracker.init();
            this.ctracker.start(this.video);
            this.loop()
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
        },
        isOpen(top, bottom) {
            const s = this.currentRate
            const a = s * 0.83
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
