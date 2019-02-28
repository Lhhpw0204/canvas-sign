<template>
  <div id="app">
    <canvas id="cavs" width="600" height="360" @mousedown="moveBefore($event)" @mousemove="moveIng($event)" @mouseup="moveEd($event)" @mouseleave="moveEd($event)"></canvas>
    <ul class="list">
      <li><input @change="colorChange" id="colorChange" type="color"></li>
      <li><input @click="cleanBoard" id="cleanBoard" type="button" value="清屏"></li>
      <li><input @click="earser" id="earser" type="button" value="橡皮擦"></li>
      <li><input @click="rescind" id="rescind" type="button" value="撤销"></li>
      <li><input @change="slideBar" id="slideBar" type="range"></li>
    </ul>
    <input class="createImg" type="button" value="生成图片" @click="createImg">
    <p>当前的画笔大小为：{{lineWidth}}</p>
  </div>
</template>

<script>

export default {
  name: 'app',
  data(){
    return {
      startX:'',
      startY:'',
      flag:false,
      lineColor:'',
      lineWidth:'',
      imgsArr:[],
      cavs:'',
      cavsboard:'',
    }
  },
  mounted(){
    this.lineWidth = 5;
    this.cavs = document.querySelector("#cavs");
    this.cavsboard = document.querySelector("#cavs").getContext("2d")
  },
  methods:{
    colorChange:function(e){
      this.lineColor = e.target.value;
    },
    cleanBoard:function(){
      this.cavsboard.clearRect(0,0,600,360);
    },
    earser:function(){
      this.lineColor = '#fff';
    },
    rescind:function(){
      if (this.imgsArr.length > 0) {
        this.cavsboard.putImageData(this.imgsArr.pop(), 0, 0);
      }
    },
    slideBar:function(e){
      if(e.target.value === '0'){
        this.lineWidth = 5;
      }else{
        this.lineWidth = e.target.value / 2;
      }
    },
    moveBefore:function(e){
      this.flag = true;
      this.startX = e.clientX;
      this.startY = e.clientY;
      this.cavsboard.beginPath();
      this.cavsboard.moveTo(e.clientX, e.clientY);
      this.cavsboard.lineCap = "round";
      this.cavsboard.lineJoin = "round";
      this.cavsboard.strokeStyle = this.lineColor;
      this.cavsboard.lineWidth = this.lineWidth;
      var img = this.cavsboard.getImageData(0, 0, this.cavs.offsetWidth, this.cavs.offsetHeight);
      this.imgsArr.push(img);
    },
    moveIng:function(e){
      var self = this;
      if(self.flag){
        self.cavsboard.lineTo(e.clientX, e.clientY);
        self.cavsboard.stroke();
      }
    },
    moveEd:function(){
      this.flag = false;
      this.cavsboard.closePath();
    },
    createImg:function () {
      // var image = new Image();
      // image.src = this.cavs.toDataURL("image/png");
      // console.log(image);
      this.cavs.toBlob(function(blob){
        var formData = new FormData(),
            httpDemo = new XMLHttpRequest();
        formData.append("上传文件",blob);
        httpDemo.timeout = 10000;
        //请求完成后执行的操作
        httpDemo.onreadystatechange = function(){
          if(httpDemo.readyState == 4 && httpDemo.status == 200){
            console.log(JSON.parse(httpDemo.responseText));
          }
        };
        httpDemo.ontimeout = function () {
            //  超时操作
        }
        httpDemo.open("post","https://user.intechtrading.com/api/Overseas/setOpenAcountInfo.ashx?action=addressphoto&proxyid=160&usertoken=DiB4pC5S2kL2jzGK4dkGB8rej9PVlVICxhnLihj5HUC6u5dGfzEaOSDJbJUOk8PjTOxxfMQxqAOjYkn6R6GMfQgPAEHArsKewycYkCNff2Wz7MqCuTaKJw**&lang=en",true);
        httpDemo.send(formData);
      });
    }
  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
  list-style: none;
}
canvas{
  margin:0 0 20px 0;
  border: solid 1px rgba(0,0,0,.5);
  border-radius: 12px;
  box-shadow: 5px 10px 16px rgba(0,0,0,.3);
}
.list{
  margin: 0;
  width: 600px;
  text-align: center;
}
.list li{
  display: inline-block;
  margin-left: 24px;
  text-align: center;
}
.list li input{
  padding: 10px 15px;
  background-color: #269fab;
  border-radius: 15px;
  border: none;
  outline: none;
  color: #fff;
  transition: all .7s ease;
  cursor: pointer;
}
.list li input:hover{
  box-shadow: 0 12px 16px 0 rgba(0,0,0,.25);
  border: solid 1px rgba(0,0,0,.6);
}
.createImg{
  display: block;
  margin: 20px auto;
  padding: 10px 15px;
  background-color: #269fab;
  color: #fff;
  border: none;
  border-radius: 15px;
  cursor: pointer;
}
</style>
