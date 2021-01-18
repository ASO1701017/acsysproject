<template>
    <div class="container">
        <div class="row">
            <h1 class="col-auto pt-4 pb-2">消費カロリー入力</h1>
        </div>
        <!--日付選択-->
        <datepicker
            v-model=selectedDate
            :format="DatePickerFormat"
            :language="ja"
            :highlighted="highlighted"
            :disabled-dates="disabledDates">
        </datepicker>
        <!--リスト-->
        <table class="table table-hover mt-1 table-sm col-auto">
            <thead>
                <tr class="table-danger">
                    <th class="addDate">日付</th>
                    <th class="training">トレーニング</th>
                    <th class="calorie">カロリー</th>
                    <th class="delete">削除</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in addItem" v-bind:key="item.id">
                    <td>{{ item.add_date }}</td>
                    <td>{{ item.motion_name }}</td>
                    <td>{{ item.motion_calorie }}kcal</td>
                    <td class="deleteButton">
                        <!-- 削除ボタン-->
                        <button v-on:click="removeItem(item)" class="btn btn-outline-danger btn-sm">ー</button>
                    </td>
                </tr>
                <!--リストが空だった時-->
                <td v-if="!addItem.length">リストは空です</td>
            </tbody>
        </table>
        <!--合計-->
        <div class="row">
            <h4 class="col-xs-6 col-auto pt-1 pb-2">消費カロリー合計：{{sumCalories}}kcal</h4>
        </div>
        <div class="row">
            <button @click="showInputModal" class="btn btn-outline-info col-md-3 col-auto mr-3 ml-3">入力して追加する</button>
            <button @click="openSelectModal" class="btn btn-outline-primary col-md-3 col-auto">選択して追加する</button>
        </div>
        <button @click="enterInformation" class="btn btn-outline-success col-md-3 mt-3 float-right" >決定</button>

        <!--入力モーダル-->
        <b-modal ref="trainingInputModal" title="トレーニングとカロリーを入力してください" centered hide-footer>
            <div class="form-group mt-auto">
                <!--トレーニング入力-->
                <input type="text" placeholder="トレーニング" v-model="inputTraining" class="form-control" v-bind:class="{'is-invalid':!inputTrainingError}">
                <span class="invalid-feedback text-center">{{inputTrainingResult}}</span>
                <!--カロリー入力-->
                <input type="number" placeholder="カロリー" v-model="inputCalorie" class="form-control mt-3" v-bind:class="{'is-invalid':!inputCalorieError}">
                <span class="invalid-feedback text-center">{{inputCalorieResult}}</span>
                <!--ボタン-->
                <div class="mt-4 row float-right">
                    <button @click="hideInputModal" class="btn btn-outline-secondary mr-3">キャンセル</button>
                    <button @click="addInputData" class="btn btn-outline-success mr-3">追加</button>
                </div>
            </div>
        </b-modal>

        <div class="example-modal-window">
            <!-- コンポーネント MyModal -->
            <inputMyModal @close="closeSelectModal" v-if="selectModal">
                <!-- default スロットコンテンツ -->
                <h4 class="px-lg-5 mx-5">分類を選択してください</h4>
                <table class="table table-hover table-sm ">
                    <thead>
                    <tr class="table-info">
                        <th class="genre">分類</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr v-for="item in genreBox" v-bind:key="item.id">
                        <td @click="getTraining(item)">{{ item.genre_name }}</td>
                    </tr>
                    </tbody>
                </table>
                <!-- /default -->
                <!-- footer スロットコンテンツ -->
                <template slot="footer">
                    <button @click="closeSelectModal" class="btn btn-outline-secondary">キャンセル</button>
                </template>
                <!-- /footer -->
            </inputMyModal>
        </div>

        <div class="example-modal-window">
            <!-- コンポーネント MyModal -->
            <inputMyModal @close="closeTrainingSelectModal" v-if="selectTrainingModal">
                <!-- default スロットコンテンツ -->
                <h4 class="px-lg-5 mx-5">トレーニングを選択してください</h4>
                <table class="table table-hover table-sm ">
                    <thead>
                    <tr class="table-info">
                        <th class="food">トレーニング</th>
                        <th class="calorie">カロリー</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr v-for="item in trainingBox" v-bind:key="item.id">
                        <td @click="addSelectData(item.motion_name,item.motion_carolies)">{{ item.motion_name }}</td>
                        <td @click="addSelectData(item.motion_name,item.motion_carolies)">{{item.motion_carolies}}</td>
                    </tr>
                    </tbody>
                </table>
                <!-- /default -->
                <!-- footer スロットコンテンツ -->
                <template slot="footer">
                    <button @click="closeTrainingSelectModal" class="btn btn-outline-secondary">キャンセル</button>
                </template>
                <!-- /footer -->
            </inputMyModal>
        </div>
    </div>
</template>

<script>
    import inputMyModal from "./MyModal";
    import Datepicker from "vuejs-datepicker";
    import {ja} from 'vuejs-datepicker/dist/locale'

    export default {
        name: "ConsumptionCalorieRegistration",
        components: { inputMyModal,Datepicker },
        data(){
            return{
                //モーダル
                selectModal:false,
                selectTrainingModal:false,
                //直接入力のデータ
                inputTraining:"",
                inputCalorie:"",
                //直接エラー名入れ
                inputTrainingResult:"",
                inputCalorieResult:"",
                //入力欄エラー判定
                inputTrainingError:true,
                inputCalorieError:true,
                //リスト用
                addItem:[],
                trainingArray:[],
                //日付選択
                selectedDate: new Date(),
                //日付形式
                DatePickerFormat: 'yyyy-MM-dd',
                //土日を強調表示
                highlighted: {
                    days: [6, 0],
                },
                //日本語設定
                ja:ja,
                //日付制約
                disabledDates: {
                    from: new Date(),
                },
                //分類
                genreBox:[],
                trainingBox:[],
            }
        },
        methods:{
            //リストの削除
            removeItem:function (item) {
                const index = this.addItem.indexOf(item);
                this.addItem.splice(index, 1)
            },
            //直接入力のモーダルを開く
            showInputModal() {
                if(!this.selectedDate){
                    alert("日付呼び出しに失敗しました。もう一度やり直してください")
                }
                else {
                    this.$refs['trainingInputModal'].show()
                }
            },
            //直接入力のモーダルを閉じる
            hideInputModal() {
                this.$refs['trainingInputModal'].hide()
                this.inputTrainingResult=""
                this.inputCalorieResult=""
                this.inputTrainingError = true
                this.inputCalorieError = true
            },

            //直接入力のモーダルを開く
            openSelectModal(){
                this.selectModal = true
            },
            //直接入力のモーダルを閉じる
            closeSelectModal() {
                this.selectModal = false
            },
            //直接入力のモーダルを開く
            openTrainingSelectModal(){
                this.selectTrainingModal = true
            },
            //直接入力のモーダルを閉じる
            closeTrainingSelectModal() {
                this.selectTrainingModal = false
            },
            addInputData(){
                //バリデーション
                //トレーニングが空だった時
                if (!this.inputTraining){
                    this.inputTrainingResult="トレーニングを入力してください"
                    this.inputTrainingError = false
                }
                //文字数が多い時
                else if (this.inputTraining.length>75){
                    this.inputTrainingResult="文字数が多すぎます"
                    this.inputTrainingError = false
                }
                //正常
                else {
                    this.inputTrainingResult=""
                    this.inputTrainingError = true
                }
                //カロリーが空だったとき
                if (!this.inputCalorie){
                    this.inputCalorieResult="カロリーを入力してください"
                    this.inputCalorieError = false
                }
                //値が負数だったとき
                else if(Number(this.inputCalorie) < 0){
                    this.inputCalorieResult="プラスで入力してください"
                    this.inputCalorieError = false
                }
                //桁数が多いとき
                else if (this.inputCalorie.length > 7){
                    this.inputCalorieResult="桁数が多すぎます"
                    this.inputCalorieError = false
                }
                //値が正常
                else {
                    this.inputCalorieResult=""
                    this.inputCalorieError = true
                }
                //日付加工
                let time = this.selectedDate.getFullYear() + ("0" + (this.selectedDate.getMonth() + 1)).slice(-2) +("0" + this.selectedDate.getDate()).slice(-2)
                //リストに登録
                if (this.inputTrainingError === true && this.inputCalorieError === true) {
                    //追加処理
                    this.addItem.push({
                        motion_name: this.inputTraining,
                        motion_calorie: this.inputCalorie,
                        add_date:Number(time),
                    })
                    this.inputTraining = ""
                    this.inputCalorie = ""
                    this.hideInputModal()
                }
            },
            //データ選択時リスト追加
            addSelectData(training,calorie){
                let time = this.selectedDate.getFullYear() + ("0" + (this.selectedDate.getMonth() + 1)).slice(-2) +("0" + this.selectedDate.getDate()).slice(-2)
                this.addItem.push({
                    add_date:Number(time),
                    motion_calorie: calorie,
                    motion_name: training,
                })
                this.closeTrainingSelectModal()
            },
            //データ送信
            enterInformation:async function(){

                if (this.addItem.length===0){
                    alert("一つ以上入力してください")
                    return
                }

                //ローディングアニメーションを起動
                this.$store.commit("setLoading", true)

                const URL = "https://fat3lak1i2.execute-api.us-east-1.amazonaws.com/acsys/users/schedule/motion"

                this.trainingArray ={
                    'account_token':this.$store.state.accountToken,
                    'data':this.addItem
                }

                const json_data = JSON.stringify(this.trainingArray)
                await fetch(URL,{
                    mode:'cors',
                    method:'POST',
                    body:json_data,
                    headers:{'Content-type':'application'},
                })
                    .then(response => response.json())
                    .then(data => {
                        console.log(data)
                        let check = data["isSuccess"]
                        //ローディングアニメーションを終了
                        this.$store.commit("setLoading", false)
                        if (check === true){
                            console.log("消費カロリー登録:ok")
                            this.$router.replace("/savecalorie")
                        }else {
                            alert("エラーが発生しました。もう一度やり直してください")
                            console.log("消費カロリー登録:ng")
                        }
                    })
                    .catch(function (error) {
                        console.log(error)
                        alert("エラーが発生しました。もう一度やり直してください")
                    })

            },
            //トレーニングの中身取得
            getTraining:async function(item){
                this.selectModal = false
                this.openTrainingSelectModal()
                const URL = "https://fat3lak1i2.execute-api.us-east-1.amazonaws.com/acsys/calorie/motion"
                let getTrainingItem ={
                    'genre_ID':item.genre_ID
                }
                const json_data = JSON.stringify(getTrainingItem)
                await fetch(URL,{
                    mode:'cors',
                    method:'POST',
                    body:json_data,
                    headers:{'Content-type':'application'},
                })
                    .then(response => response.json())
                    .then(data => {
                        console.log("トレーニング取得:ok")
                        this.trainingBox = data
                    })
                    .catch(function (error) {
                        console.log(error)
                        console.log("トレーニング取得:ng")
                        alert("エラーが発生しました。もう一度やり直してください")
                    })
            },
        },
        computed:{
            //カロリー合計計算
            sumCalories(){
                return this.addItem.reduce(function(sum, item) {
                    return Number(sum) + Number(item.motion_calorie)
                }, 0)
            }
        },
        async created() {
            //トレーニング分類取得
            const URL = "https://fat3lak1i2.execute-api.us-east-1.amazonaws.com/acsys/calorie/motion"

            await fetch(URL,{
                mode:'cors',
                method:'Get',
                headers:{'Content-type':'application'},
            })
                .then(response => response.json())
                .then(data => {
                    console.log("トレーニング分類取得:ok")
                    this.genreBox = data
                })
                .catch(function (error) {
                    console.log(error)
                    console.log("トレーニング分類取得:ng")
                    alert("エラーが発生しました。もう一度やり直してください")
                })
        }
    }
</script>