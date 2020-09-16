<template>
    <div id="twitter_share">
        <input type="text" id="detail" v-model="TweetDetail" maxlength="280" placeholder="SNSで共有する内容" />
        <button @click="twitterShare">ツイッターでシェアする</button>
        </div>
</template>

<script>
    export default {
        name: "Training",
        data() {
            return {
                TweetDetail: "",
            }
        },methods:{
            twitterShare(){
                //シェアするためのtwitterの画面を設定（textareaに記載された内容付きで）
                var shareURL = 'https://twitter.com/intent/tweet?text=' + this.TweetDetail + "%20%23テスト用";
                //シェア用の画面へ移行
                window.open(shareURL,null,'left=350,top=200,width=700,height=550')
                // location.href = shareURL
                // this.$router.replace("/savecalorie")
            }
        }
    }
</script>