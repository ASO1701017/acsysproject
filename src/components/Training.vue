<template>
    <div class="container">
        <b-card no-body class="mt-3">
            <b-tabs card>
                <b-tab title="おすすめ" active>
                    <div class="row">
                        <div class="card mx-3 mx-auto" style="width: 18rem;">
                            <div class="card-body">
                                <h5 class="card-title">自宅で出来る10分筋トレ（初級編）</h5>
                                <h6 class="card-subtitle mb-2 text-muted">鈴木達也</h6>
                                <p class="card-text">自宅の狭いスペースで行える全身の筋力トレーニングです。
                                    1日1セット、たった10分で全身のトータルトレーニングが行えます。</p>
                                <button v-b-modal.modal-center class="btn btn-outline-success">挑戦してみる</button>
                            </div>
                        </div>
                    </div>
                </b-tab>
            </b-tabs>
        </b-card>

        <b-modal id="modal-center" centered title="自宅で出来る10分筋トレ（初級編）">
            <b-embed
                    type="iframe"
                    aspect="16by9"
                    src="https://www.youtube.com/embed/qOiDlprXF2w"
                    allowfullscreen></b-embed>
        </b-modal>
    </div>
</template>

<script>
    export default {
        name: "Training",
        data(){
            return{
            }
        },methods:{

        }
    }
</script>