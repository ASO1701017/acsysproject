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
<!--        <LoadExternalJS :url="some_url"></LoadExternalJS>-->
    </div>

</template>
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.8.0/p5.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.8.0/addons/p5.dom.min.js"></script>

<script>
    import poseNet from '@tensorflow-models/posenet';
    import ml5 from 'ml5';
    export default {
        // props:['url'],
        // mounted(){
        //     let tag_name = 'script'
        //     let recaptchaScript = document.createElement(tag_name)
        //     recaptchaScript.setAttribute('src', this.url)
        //     document.head.appendChild(recaptchaScript)
        // },
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
                some_url: 'https://unpkg.com/@tensorflow-models/posenet',
            }
        },
        created() {
            const script1 = function (p5,ml5) {
                p5.setup = _ => {
                    p5.createCanvas(500, 500);
                    p5.createCapture();
                    this.posenet = ml5.poseNet(video, this.modelReady);
                    this.posenet.on('pose', function (results) {
                        this.poses = results;
                    });
                    this.video.hide();
                    this.draw()
                }
            }
            const P5 = require('p5')
            new P5(script1)
        },
        methods: {
            modelReady: function(){
                select('#status').html('');
            },

            // count: function() {
            //     this.count_value += 1;
            //     console.log(this.count_value);
            //     this.count_disp.innerHTML = this.count_value;
            // },

            draw: function () {
                if (poses.length > 0) {
                    let pose = poses[0].pose;
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
                            if (this.count_value == 10) {
                                document.getElementById('disp_count').style.color = "#FF0000";
                            }
                        }
                    }
                    image(video, 0, 0, width, height);
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
