<template>
    <div :style="{ backgroundColor: rgba }">
        <video class="video" ref="videoRef"></video>
        <canvas ref="canvasRef"></canvas>
        <div>
            <button @click="snap">取得</button>
            <div class="indicator">{{ rgba }}</div>
        </div>
    </div>
</template>
<script lang="ts">
import { ref } from '@vue/reactivity'
import { defineComponent, onMounted, watch } from '@vue/runtime-core'

export default defineComponent({
    setup() {
        const canvasRef = ref<HTMLCanvasElement>()
        const videoRef = ref<HTMLVideoElement>()
        let rgba = ref<string>('')
        
        onMounted(()=>{
            if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
                navigator.mediaDevices.getUserMedia({ video: {facingMode: { exact: "environment" }}}).then(stream => {
                    if(!videoRef.value){
                        return;
                    }
                    videoRef.value.srcObject = stream
                    videoRef.value?.play()
                })
            }
        })
        function snap() {
            if(!videoRef.value){
                return;
            }
            const context = canvasRef.value?.getContext('2d')
            context?.drawImage(videoRef.value, 0, 0, 1, 1)
            const colorData = context?.getImageData(0,0,1,1)
            rgba.value = "rgba("+ colorData?.data + ")"
            console.log(rgba.value)
        }
        return {
            canvasRef, videoRef, rgba, snap
        }
    }
})
</script>
<style scoped>
* {
    text-align: center;
}
.video {
    width: 90vw;
}
.indicator {
    color: #fff;
    font-weight: bold;
    font-size: 1.2rem;
}
</style>
