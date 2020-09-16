<template>
    <div class="twitter_share">
        <textarea id="detail" v-model="TweeteDetail" maxlength="280" placeholder="SNSで共有する内容"></textarea>
        <button @click="twitterShare">ツイッターでシェアする</button>
        </div>
</template>

<script>
    export default {

        methods:{
            twitterShare(){
                //シェアするためのtwitterの画面を設定（textareaに記載された内容付きで）
                var shareURL = 'https://twitter.com/intent/tweet?text=' + "テスト";
                //シェア用の画面へ移行
                window.open(shareURL)
                // location.href = shareURL
                // this.$router.replace("/savecalorie")
            }
        }
    }
</script>