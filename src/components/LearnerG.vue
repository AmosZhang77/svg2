<template>
  <div class="learner">
    <div @click="startPlay">开始</div><!--开始-->
    <div @click="unZip">解压</div><!--解压-->
    <input type="file" id="zipData" @change="zipDataChange" ref="zipData"><span>导入</span>
  </div>
</template>

<script>
  var JSZip = require("jszip");

export default {
  name: 'learner',
  data () {

    return {
      zipDataFiles:[],
      dateObj:{},
      msg: 'Welcome to Your Vue.js App'
    }
  },
  methods: {
    startPlay(){

    },
    unZip(){
    	var _this=this;
      var zip1 = new JSZip();
      zip1.loadAsync(this.zipDataFile)
        .then(function(file) {
          // you now have every files contained in the loaded zip
          zip1.file("canvas.json").async("string") // 此处是压缩包中的testTXT.txt文件，以string形式返回其内容，此时已经可以获取zip中的所有文件了
            .then(function (content) {
              // use content
              //alert(content)
              _this.dataObj = JSON.parse(content);
              console.log(_this.dataObj);
            });
        });
    },
    unAES(){
      /*var zip1 = new JSZip();
      zip1.loadAsync(this.zipDataFile)
        .then(function(file) {
          // you now have every files contained in the loaded zip
          zip1.file("testTXT.txt").async("string") // 此处是压缩包中的testTXT.txt文件，以string形式返回其内容，此时已经可以获取zip中的所有文件了
            .then(function (content) {
              // use content
              alert(content)
            });
        });*/
    },
    zipDataChange(){
    	//alert(1);
      if(this.$refs.zipData.files&&this.$refs.zipData.files.length){
        this.zipDataFile=this.$refs.zipData.files[0];
        console.log("zipDataFiles",this.zipDataFile);
        console.log("name",this.zipDataFile.name);
        console.log("size",this.zipDataFile.size);
        console.log("type",this.zipDataFile.type);
      }
    }
  }


}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
