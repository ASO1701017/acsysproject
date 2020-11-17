<template>
  <!-- PoseNetモデルの読み込み -->
  <div id="app" class="container">
    <h1>デモ</h1>
    <p id='status'></p>
    <div class="container">
      <div class="dash-unit">
        <h2 class="a-spacing-none">回数</h2>
        <div class="count">
          <div id="disp_count"></div>
        </div>
        <div class="reset">
          <button id="btn_reset" >リセット</button>
<!--          <button id="draw" @click="draw();">draw確認</button>-->
        </div>
        <canvas ref="canvas" id="canvas" width="500" height="500"></canvas>
        <video ref="video" id="video" width="500" height="500" autoplay></video>
      </div>
    </div>
  </div>

</template>

<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs"></script>
<script src="https://cdn.jsdelivr.net/npm/@tensorflow-models/posenet"></script>

<script>
import p5 from 'p5'
import ml5 from 'ml5'

export default {
  // props:[{
  //   poses: [],
  // }],
  data() {
    return {
      video: {},
      posenet: '',
      poses: [],
      count_value: 0,
      should_count: true,
      count_disp: document.getElementById("dips_count"),
      reset_btn: document.getElementById("btn_reset"),
      keypoint: '',
      canvas: {},
    }
  },

  mounted() {
    //Webカメラ
    this.video = this.$refs.video
    if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
      navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
        this.video.srcObject = stream
        this.video.play()
      })
    }

    //poseNetのセットアップ処理
    console.log("挙動確認")
    // this.video = p5.createCapture();
    this.posenet = ml5.poseNet(this.video,this.modelReady());
    let val=[];
    const ref = this;
    this.posenet.on('pose', function (results) {
      val = this.poses = results;
      console.log("pose_length:", val.length)
      console.log("姿勢判定処理を開始します")
      ref.draw(val)
    })
        // .then(
        // function (){
        //   this.draw(results)
        // }
    // );
    //canvasに描画が成功したのでvideo要素を見えないようにする
    // this.video.hide();
  },
  methods: {
    //canvasにVIDEOの内容を描画
    capture () {
      this.canvas = this.$refs.canvas
      this.canvas.getContext('2d').drawImage(this.video, 0, 0, 500, 500)
    },

    //poseNetのモデルが読み取られたか確認するメソッド
    modelReady: function () {
      console.log('モデルの読取に成功しました')
    },

    //回数をカウントするメソッド
    count: function () {
      this.count_value += 1;
      console.log(this.count_value);
      this.count_disp.innerHTML = this.count_value;
    },


    draw: function (poses) {
      // let poses = this.poses;
      console.log('描画を開始')
      // poses.length +=1;
      console.log(poses)
      if (poses.length > 0) {
        let pose = poses[0].pose;
        this.keypoint = pose.keypoints[0];
        // console.log('部位名：' + this.keypoint.part);
        for (let i = 0; i < poses.length; i++) {
          // poseが持つ情報を出力
          console.log('poseが持つ情報を出力します')
          let pose = poses[i].pose
          //本当は0.6以上だが挙動確認のため低い値に設定
          if (pose.score >= 0.3) {
            console.log("ok");
            //ユーザーが判定ラインより下にいった時
            //本当は250以上だが挙動確認のため低い値に設定
            if (pose.nose.y >= 220.0 && this.should_count) {
              console.log('カウントします')
              console.log(pose.nose.y);
              this.count_value += 1;
              this.should_count = false;
              console.log(this.count_value);
              console.log(this.should_count)
              let i =  document.getElementById("dips_count")
              i = this.count_value;
              console.log(this.count_disp)
            } else if (pose.nose.y <= 240.0) {
              // 姿勢が元に戻った判定
              this.should_count = true;
            }
            if (this.count_value === 10) {
              document.getElementById('disp_count').style.color = "#FF0000";
            }
          }
        }
        // image(this.video, 0, 0, width, height);
        this.drawSkeleton();
        this.drawKeypoints();
      }
      else {
        console.log('pose情報が見当たりません')
      }
      //判定ラインの線を引く
      // fill(255, 0, 0);
      // stroke('red');
      // line(0, 250, 600, 250);
    },


    drawKeypoints: function (poses) {
      console.log('drawKeypoints起動')
      for (let i = 0; i < this.poses.length; i++) {
        let pose = poses[i].pose;
        for (let j = 0; j < pose.length; j++) {
          this.keypoint = pose.keypoints[j];
          if (this.keypoint > 0.2) {
            console.log('線を引きます')
            // シェイプの塗りに使用するカラーを設定
            this.keypoint.fill(color(0, 0, 255));
            // 線とシェイプの枠線の描画に使用するカラーを設定
            this.keypoint.stroke(20);
            this.keypoint.ellipse(this.keypoint.position.x, this.keypoint.position.y, 10, 10);
          }
        }
      }
    },


    drawSkeleton: function () {
      for (let i = 0; i < this.poses.length; i++) {
        let skeleton = this.poses[i].skeleton;
        for (let j = 0; j < skeleton.length; j++) {
          let partA = skeleton[j][0];
          let partB = skeleton[j][1];

          //骨格の線を引く
          stroke('rgb(0,255,0)');
          // strokeWeight(5);
          line(partA.position.x, partA.position.y, partB.position.x, partB.position.y);
        }
      }
    },

    //回数の値をリセットするメソッド
    resetValue: function () {
      console.log("リセットしました")
      this.count_value = 0;
      this.count_disp.innerHTML = this.count_value;
    }
  }
}
</script>
