<template>
  <div class="learner">
    <div @click="startPlay">开始</div><!--开始-->
    <div @click="unZip">解压</div><!--解压-->
    <div @click="AES">加密</div><!--加密-->
    <div @click="unAES">解密</div><!--解密-->
    <div @click="base64">转进base64</div><!--解密-->
    <div @click="unBase64">转出base64</div><!--解密-->
    <div @click="doWork">转入base64-解密-转出base64-解压</div><!--解密-->
    <div @click="doWork10">加密解密</div><!--解密-->
    <input type="file" id="data" @change="dataChange" ref="data"><span>导入</span>


    <div @click="startPlay">开始播放</div>
    <div class="topBar" :style="{height:svgHeight,width:svgWidth}" id="app">
      <svg width="100%" height="100%" version="1.1"
           xmlns="http://www.w3.org/2000/svg">
        <path :fill="filterColor(svgFront.color)" v-for="(svgFront,index) in svgFronts"
              :stroke="svgFront.id"
              :stroke-width="svgFront.width"

              :d="svgFront.path"
        /><!--:color="filterColor(svgFront.color)"-->
        <path :fill="filterColor(svgR.color)" v-for="(svgR,index) in svgRs"
              :stroke="svgR.id"
              :stroke-width="svgR.width"
              :d="svgR.path"
        />
      </svg>
    </div>
  </div>
</template>
<script>
  //import CryptoJS from '../../data/aes/aes.js'
  var JSZip = require("jszip");
  import mixinGlobal from '../mixin/mixinGlobal.js'

  import svgData from '../../data/canvas.json';
  export default {
    name: 'learner',
    data () {
      console.log(svgData);
      var svgHeight = svgData.initialHeight * 2 + "px";
      var svgWidth = svgData.initialWidth * 2 + "px";
      var svgFounts = svgData.linesBeforeRecord;
      var svgRs = [];
      console.log(svgData);


      return {
        dataFiles: [],
        dataFile: {},
        AESDataFiles: {},
        dateObj: {},
        base64Data: "",

        svgHeight: svgHeight,
        svgWidth: svgWidth,
        svgFronts: svgFounts,
        svgRs: [],
        svgAll: [],
        svgAllPlay: [],

        startTimeTime:0,
        startTimeDraw:0,
      }
    },
    mixins: [mixinGlobal],
   /* mounted:function() {

    },*/
    methods: {
      drawInterval: function() {

        setInterval(this.drawIntervalFun, 500)
      },
      drawIntervalFun: function() {
        console.log(1)
        var now = {};
        for(let i=0;i<this.svgAllPlay.length; i++){
          console.log(2)
           now = new Date();
          console.log(this.svgAllPlay[i].startTime,this.startTimeDraw,now.getTime()- this.startTimeTime);
            if((this.svgAllPlay[i].startTime - this.startTimeDraw) < (now.getTime()- this.startTimeTime)){
              console.log("if",this.svgAllPlay[i]);
              this.svgRs.push(this.svgAllPlay[i]);
              this.svgAllPlay.splice(i,1);
              i--
            }


        }
      },
      startPlay(){


        var tempOne = {};
        var count = 0;
        for (let i = 0; i < svgData.recordedOperations.length; i++) {

          if (svgData.recordedOperations[i].type == 1) {
            if (count == 0) {
              this.startTimeDraw = svgData.recordedOperations[i].startTime;
            }
            console.log("path", JSON.parse(svgData.recordedOperations[i].content));
            tempOne = JSON.parse(svgData.recordedOperations[i].content);
            tempOne.startTime = svgData.recordedOperations[i].startTime;
            tempOne.endTime = svgData.recordedOperations[i].endTime;
            this.svgAll.push(tempOne);
            count++;
          }
        }
        this.svgAllPlay = this.svgAll;
        var date = new Date();
        console.log(date);
        this.startTimeTime = date.getTime();

        this.drawInterval();
      },
      getObjectURL(file) {
        var url = null;
        if (window.createObjectURL != undefined) {
          url = window.createObjectURL(file);
          console.log("imgUrl1");
        } else if (window.URL != undefined) {
          url = window.URL.createObjectURL(file);//新ie火狐谷歌都走这
          console.log("imgUrl2");
        } else if (window.webkitURL != undefined) {
          url = window.webkitURL.createObjectURL(file);
          console.log("imgUrl3");
        }
        return url
      },

      unZip(data){
        var _this = this;
        var zip1 = new JSZip();
        //zip1.loadAsync(this.dataFile)
        zip1.loadAsync(data)
          .then(function (file) {
            console.log(file);

            // you now have every files contained in the loaded zip
            zip1.file("canvas.json").async("string") // 此处是压缩包中的testTXT.txt文件，以string形式返回其内容，此时已经可以获取zip中的所有文件了
              .then(function (content) {
                // use content
                //alert(content)
                _this.dataObjStr = content;
                _this.dataObj = JSON.parse(content);
                console.log("解压后对象", _this.dataObj);
                console.log("解压后字符串", _this.dataObjStr);
              });
          });
      },
      unAES(dataFrom, keyIn){
        /*var zip1 = new JSZip();
         zip1.loadAsync(this.dataFile)
         .then(function(file) {
         // you now have every files contained in the loaded zip
         zip1.file("testTXT.txt").async("string") // 此处是压缩包中的testTXT.txt文件，以string形式返回其内容，此时已经可以获取zip中的所有文件了
         .then(function (content) {
         // use content
         alert(content)
         });
         });*/
        //var word = this.AESDataFiles;

        var CryptoJS = require("crypto-js");
        /*require.config({
         paths: {
         'crypto-js': 'path-to/bower_components/crypto-js/crypto-js'
         }
         });*/
        var data = dataFrom;
        data = {a: 1, b: 2, c: 3}
        console.log("dataForm", dataFrom);

// Encrypt
        var ciphertext = CryptoJS.AES.encrypt(JSON.stringify(data), keyIn);
        console.log("加密", ciphertext);

// Decrypt
        var bytes = CryptoJS.AES.decrypt(ciphertext.toString(), keyIn);
        //alert(3);
        console.log("解密后1", bytes.toString(CryptoJS.enc.Utf8));

        var decryptedData = JSON.parse(bytes.toString(CryptoJS.enc.Utf8));

        console.log("解密后", decryptedData);


        /*// Encrypt
         var ciphertext = CryptoJS.AES.encrypt(data, keyIn);

         // Decrypt
         var bytes  = CryptoJS.AES.decrypt(ciphertext.toString(), keyIn);
         var plaintext = bytes.toString(CryptoJS.enc.Utf8);
         console.log("解密后",plaintext);*/


        /*var word = data;
         console.log("解密",data);
         var key = CryptoJS.enc.Utf8.parse(keyIn);
         var iv = CryptoJS.enc.Utf8.parse(0);//('十六位十六进制数作为秘钥偏移量');
         alert(3);
         var decrypt = CryptoJS.AES.decrypt(word, key, {iv:iv, mode: CryptoJS.mode.ECB, padding: CryptoJS.pad.Pkcs7});
         alert(5);
         var r = CryptoJS.enc.Utf8.stringify(decrypt).toString();
         alert(6);

         console.log("解密后",decrypt);
         console.log("解密后",r);*/

      },
      AES(){
        //console.log(CryptoJS)

        var word = this.dataObj;
        console.log("this.dataObjStr", this.dataObjStr);
        //var word = String(this.dataObjStr);
        console.log("word", word);

        //var word = "你好";
        var key = CryptoJS.enc.Utf8.parse("abcdefgabcdefg12");

        var srcs = CryptoJS.enc.Utf8.parse(word);
        var encrypted = CryptoJS.AES.encrypt(srcs, key, {mode: CryptoJS.mode.ECB, padding: CryptoJS.pad.Pkcs7});
        this.AESDataFiles = encrypted.toString();
        console.log("加密后", this.AESDataFiles);
      },
      dataChange(){
        //alert(1);
        if (this.$refs.data.files && this.$refs.data.files.length) {
          this.dataFile = this.$refs.data.files[0];
          console.log("dataFile", this.dataFile);
          console.log("name", this.dataFile.name);
          console.log("size", this.dataFile.size);
          console.log("type", this.dataFile.type);
        }
      },
      base64(data){
        var _this = this;
        var reader = new FileReader();
        reader.readAsDataURL(data);
        reader.onload = function (e) {
          console.log("base64格式内容", e.target.result);
          //$('#file_base64').val(e.target.result);
          _this.base64Data = e.target.result;
          _this.doWork2(e.target.result);
          //return e.target.result
        };
      },
      unBase64(data){
        var result = atob(data);
        console.log("解密结果", result);
        return result
      },
      doWork(){
        console.log("dataFile", this.dataFile);
        //var result1 = this.base64(this.dataFile);
        //console.log("result1",result1);
        //var keyIn = CryptoJS.enc.Utf8.parse("@#R$RBEH^%U&JRTBTRN%^J&^J&^%");

        //var keyIn = CryptoJS.enc.Utf8.parse("@#R$RBEH^%U&JRTB");//TRN%^J&^J&^%";
        //var keyIn = "@#R$RBEH^%U&JRTB";//TRN%^J&^J&^%";
        var keyIn = "abcdefgabcdefg12";//TRN%^J&^J&^%";

        //var keyIn = "@#R$RBEH^%U&JRTBTRN%^J&^J&^%";
        var result2 = this.unAES(this.dataFile, keyIn);
        // var result2 = this.unAES("一二三",keyIn);
        console.log("result2", result2);

      },
      doWork2(base64Data){
//alert(2);
        console.log("result1", base64Data);

        //var keyIn = "@#R$RBEH^%U&JRTB";//TRN%^J&^J&^%";

        //var keyIn = "@#R$RBEH^%U&JRTBTRN%^J&^J&^%";
        var keyIn = CryptoJS.enc.Utf8.parse("@#R$RBEH^%U&JRTB");//TRN%^J&^J&^%";
        //var keyIn = CryptoJS.enc.Utf8.parse("@#R$RBEH^%U&JRTBTRN%^J&^J&^%");

        /*var result2 = this.unAES(base64Data,keyIn);
         console.log("result2",result2);
         */
      },
      doWork10(){
        var CryptoJS = require("crypto-js");

        var data = this.dataFile;

// Encrypt
        var ciphertext = CryptoJS.AES.encrypt(JSON.stringify(data), 'secret key 123');

// Decrypt
        var bytes = CryptoJS.AES.decrypt(ciphertext.toString(), 'secret key 123');
        var decryptedData = JSON.parse(bytes.toString(CryptoJS.enc.Utf8));

        console.log(decryptedData);
      }
    }


  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
