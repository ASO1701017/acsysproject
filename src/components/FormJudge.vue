<template>
    <div id="app">
        <label for="input-video">{{ isLoading ? '読み込み中...' : '動画を選択'}}</label>
        <input id="input-video" type="file" accept="video/mp4,video/x-m4v" @change="handleFileSelect">
        <video controls v-if="src">
            <source :src="src" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'>
        </video>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                input_video: null,
                uploadVideoUrl: '',
                src: null,
                selected: 0,
                isLoading: false
            }
        },
        methods: {
            handleFileSelect(event) {
                // reset data
                this.src = null
                this.selected = 0

                // varidate file
                const file = event.target.files[0]
                if (!file || !file.type.match('video/*')) return

                // read file
                const reader = new FileReader()
                reader.onload = (evt) => {
                    this.src = evt.target.result
                    this.isLoading = false
                }
                reader.readAsDataURL(file)
                this.isLoading = true
            },
        }
    }
</script>
