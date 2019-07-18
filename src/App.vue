<template>
  <div id="app">
    <input type="file" @change="getFile">
    <button @click="cropImg">Crop</button>
    <div class="preview-img" v-if="isRender">
        <vue-cropper
          ref="cropper"
          :src="imgSrc"
          alt="Source Image"
          drag-mode="crop"
          :aspect-ratio="1/1"
          :zoomable="false"
          :img-style="config"
        >
        </vue-cropper>
    </div>
    <img ref="myImg"/>
  </div>
</template>

<script>
import VueCropper from 'vue-cropperjs';
import 'cropperjs/dist/cropper.css';
export default {
  name: 'app',
  data(){
    return {
      imgSrc:'',
      isRender:false,
      config:{
        width:'100%',
        height:'100%',

      }
    }
  },
  methods:{
    getFile(e){
      let file = e.target.files[0]
      this.isRender = false
      this.getBase64(file)
      .then((res)=>{
        this.imgSrc = res
        this.isRender = true
        // this.$refs.myImg.setAttribute('src',res)
      })
    },
    getBase64(file){
      return new Promise((resolve,reject) => {
        var render = new FileReader();
        render.readAsDataURL(file);
        render.onload = function() {
          resolve(render.result)
        }
        render.onerror = function (error) {
          reject(error)
        };
      })
    },
    cropImg(){
      let el = this.$refs.cropper
      let data = el.getData()
      el.getCroppedCanvas(data).toBlob((blob)=>{
        var reader = new FileReader();
          reader.readAsDataURL(blob); 
          reader.onloadend = ()=>{ 
            this.$refs.myImg.setAttribute('src',reader.result)
        }
      })
    }
  },
  components: { VueCropper }
}
</script>
<style scoped>
  .preview-img{
    width:450px;
    height: auto;
  }
</style>
