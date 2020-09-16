<template>
    <div>
        <a :href="twUrl"></a>
    </div>
</template>

<script>
    export default {
        name: "CalorieShare",
        data : function(){
        return {
            // Twitterのシェア用URL 空でもOK
            twUrl : '',
        }
    },
        methods : {

            createSnsUrl : function(){
                // 今のurlエンコード
                var url = encodeURIComponent(location.href);
                // タイトル又はdescription
                var txt = encodeURIComponent(document.querySelector("meta[name='description']").getAttribute('content'));
                // Twitter用url作成
                this.twUrl = 'https://twitter.com/intent/tweet?text=' + txt + '&hashtags=' + txt + '&url=' + url;
            }
        }
    }
</script>