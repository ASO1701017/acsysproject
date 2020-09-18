<template>
    <div id="twitter_share" class="container">
        <div class="text-success mt-5 mb-3 text-center h1 font-weight-bold">
            Twitterにシェア
        </div>
        <form>
            <div>
                <h4 class="text-success border-bottom border-success mb-5 col-11 text-left mx-auto">投稿内容</h4>
                <span class="form-group row mx-auto">
                    <label for="detail" class="col-sm-3  col-form-label text-right col-auto" >SNSで共有する内容</label>
                    <input type="text" class="col-sm-7 col-auto form-control ml-xs-5 " id="detail" maxlength="280" v-model="TweetDetail">
                </span>
                <span class="form-group row mx-auto">
                    <label for="detail" class="col-sm-3  col-form-label text-right col-auto" >ハッシュタグ</label>
                    <input type="text" class="col-sm-7 col-auto form-control ml-xs-5 " id="hashtag" maxlength="280" v-model="HashTag">
                </span>
            </div>
            <div class="form-group col text-center">
                <button class="btn btn-success btn-lg mx-auto col-md-offset-3 mt-4 " @click="twitterShare">ツイッターでシェアする</button>
            </div>
        </form>
    </div>
</template>

<script>
    export default {
        name: "Share",
        data() {
            return {
                TweetDetail: "",
                HashTag: "",
            }
        },methods:{
            twitterShare(){
                //シェアするためのtwitterの画面を設定（textareaに記載された内容付きで）
                var shareURL = 'https://twitter.com/intent/tweet?text=' + this.TweetDetail + "%20%23" + this.HashTag;
                //シェア用の画面へ移行
                window.open(shareURL,null,'left=350,top=200,width=700,height=550')
                // location.href = shareURL
                // this.$router.replace("/savecalorie")
            }
        }
    }
</script>