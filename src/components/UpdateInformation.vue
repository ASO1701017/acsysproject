<template>
    <div id="#app">
        <div class="jumbotron jumbotron-fluid bg-dark">
            <div class="container">
                <h1 class="display-4 font-weight-lighter" style="font-family: Helvetica,'Yu Gothic UI',serif">更新情報</h1>
            </div>
        </div>
        <div class="container">
            <div class="row">
                <div class="col-3 lead">
                    日付
                </div>
                <div class="col lead ">
                    内容
                </div>
            </div>
            <hr>
            <div class="row mb-3">
                <div class="col-3 lead">
                    9月17日
                </div>
                <div class="col lead">
                    Twiiterアカウントを開設しました。新しく出来た機能や更新情報を呟いていきたいと思います。
                    ぜひフォローしてください！
                    <a href="https://twitter.com/acsys_calorie">Twitterアカウントはこちら</a>
                </div>
            </div>
            <div class="row mb-3">
                <div class="col-3 lead">
                    8月20日
                </div>
                <div class="col lead">
                    <div class="mb-2">
                        以下の機能を公開しました。<br>
                        ・カレンダー<br>
                        ・統計<br>
                        ・トレーニング<br>
                        ・登録情報の変更<br>
                    </div>
                    以下のバグを修正しました。<br>
                    ・新しく摂取カロリーを登録すると、前に登録した摂取カロリーが消えること。<br>
                    ・統計機能でリストにある貯金の表示ミスを修正しました。
                </div>
            </div>
            <div class="row mb-3">
                <div class="col-3 lead">
                    8月14日
                </div>
                <div class="col lead">
                    サイト公開しました。
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "UpdateInformation"
    }
</script>

<style scoped>
    .jumbotron {
        background-position: center 60%;
        color: white;
    }
</style>
