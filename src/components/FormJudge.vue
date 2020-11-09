<template>
  <!-- PoseNetモデルの読み込み -->
  <div id="app" class="container">
    <h1>Count demo</h1>
    <p id='status'></p>
    <div class="container">
      <div class="dash-unit">
        <h2 class="a-spacing-none">回数</h2>
        <div class="cont">
          <div id="disp_count">0</div>
        </div>
        <div class="reset">
          <button id="btn_reset" >リセット</button>
        </div>
      </div>
    </div>
  </div>

</template>

<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
<script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/posenet"></script>

<script>
import p5 from 'p5'
import ml5 from 'ml5'
//import poseNet from '@tensorflow-models/posenet'

export default {
  // props: ['url1','url2','url3'],
  data() {
    return {
      video: '',
      posenet: '',
      poses: [],
      count_value: 0,
      should_count: true,
      count_disp: document.getElementById("disp_count"),
      reset_btn: document.getElementById("btn_reset"),
      keypoint: '',
      // pose_url1: 'https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.8.0/p5.min.js',
      // pose_url2: 'https://unpkg.com/ml5@latest/dist/ml5.min.js',
      // pose_url3: 'https://unpkg.com/@tensorflow-models/posenet',
    }
  },

  created() {
    const script1 = function (p5,ml5) {
      p5.setup = _ => {
        const canvas = p5.createCanvas(500, 500);
        canvas.parent('videoContainer');
        this.video = p5.createCapture();
        this.video.size(width, height);
        this.posenet = ml5.poseNet(this.video, this.modelReady);
        this.posenet.on('pose', function (results) {
          this.poses = results;
        });
        this.video.hide();
        this.draw()
      }
    }
    const P5 = require('p5')
    new P5(script1(p5, ml5))
  },
  methods: {
    modelReady: function(){
      select('#status').html('モデルを読み取りました');
    },

    // count: function() {
    //     this.count_value += 1;
    //     console.log(this.count_value);
    //     this.count_disp.innerHTML = this.count_value;
    // },

    draw: function () {
      if (this.poses.length > 0) {
        let pose = this.poses[0].pose;
        this.keypoint = pose.keypoints[0];
        for (let i = 0; i < this.poses.length; i++) {
          // poseが持つ情報を出力
          let pose = this.poses[i].pose;
          if (pose.score >= 0.6) {
            console.log("ok");
            if (pose.nose.y >= 250.0 && this.should_count) {
              console.log(pose.nose.y);
              this.count_value += 1;
              this.should_count = false;
              console.log(this.count_value);
              console.log(this.should_count);
              this.count_disp.innerHTML = this.count_value;
            } else if (pose.nose.y <= 240.0) {
              // 姿勢が元に戻った判定
              this.should_count = true;
            }
            if (this.count_value === 10) {
              document.getElementById('disp_count').style.color = "#FF0000";
            }
          }
        }
        image(this.video, 0, 0, width, height);
        this.drawSkeleton();
        this.drawKeypoints();
      }
      fill(255, 0, 0);
      stroke('red');
      line(0, 250, 600, 250);
    },

    drawKeypoints: function () {
      for (let i = 0; i < this.poses.length; i++) {
        let pose = this.poses[i].pose;
        for (let j = 0; j < pose.keypoints.length; j++) {
          this.keypoint = pose.keypoints[j];
          if (this.keypoint.score > 0.2) {
            fill(color(0, 0, 255));
            stroke(20);
            ellipse(this.keypoint.position.x, this.keypoint.position.y, 10, 10);
          }
        }
      }
    },
    drawSkeleton: function(){
      for (let i = 0; i < this.poses.length; i++) {
        let skeleton = this.poses[i].skeleton;
        for (let j = 0; j < skeleton.length; j++) {
          let partA = skeleton[j][0];
          let partB = skeleton[j][1];

          stroke('rgb(0,255,0)');
          // strokeWeight(5);
          line(partA.position.x, partA.position.y, partB.position.x, partB.position.y);
        }
      }
    },
    // resetValue: function(){
    //     this.count_value = 0;
    //     this.count_disp.innerHTML = this.count_value;
    // }
  }
}

</script>
