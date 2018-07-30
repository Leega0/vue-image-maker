<template>
  <div id="app">
    <el-container>
      <el-header>
        header
      </el-header>
      <el-main>
          <div class="annotation-wrap">
              <div id="an_container">
                <div id="an_map" @click="bindEvent($event)" style="width:827px;height:497px;">
                   <img :src="bgimg" alt="img" id="hideimg">
                </div>
              </div>
          </div>
      </el-main>
      <el-footer>
          footer
      </el-footer>
    </el-container>
  </div>
</template>

<script>
export default {
  data(){
    return{
      anArr:[],
      mark:{},
      bgimg:'http://pa7krp3eu.bkt.clouddn.com/u2159.png',
    }
  },
  methods: {
    drawImg(){
      let that = this;
      let container = this.getid('an_container');
      let map = this.getid("an_map");
      let newCanvas = document.createElement('canvas');
      let context = newCanvas.getContext('2d');

      let newImage = new Image();
      newImage.src = this.bgimg;

      let imgOjb = this.getid("hideimg");

      let imgWidth = imgOjb.getBoundingClientRect().width
      let imgHeight = imgOjb.getBoundingClientRect().height
      
      // 父亲

      newCanvas.width = imgWidth;
      newCanvas.height = imgHeight;

      context.drawImage(newImage,0,0,imgWidth,imgHeight)
      // 移除原始图片
      map.removeChild(imgOjb);
      // 把canvas插入页面
      map.appendChild(newCanvas);

      // let imgData   = localStorage.getItem('pdfsaveimg');
      // let canvasWidth  = localStorage.getItem('imgWidth');
      // let canvasHeight = localStorage.getItem('imgHeight');
    },
    getid(id){
        //获取dom对象
        return document.getElementById(id);
    },
    getOffset(obj){
      // 获取坐标系
      let x = 0, y = 0;
      while (obj) {
        x += obj.offsetLeft;//offsetLeft返回的就是元素距离父元素左边的距离，单位是像素
        y += obj.offsetTop;//offsetTop返回的就是元素距离父元素上边的距离，单位是像素
        obj = obj.offsetParent;//指定的父元素
      }
      return {
        // 返回x，y坐
        x: x,
        y: y
      }
    },
    addMark(p, x, y, index) {//封装创建圆点

            var div = document.createElement("div");//创建div元素
            div.id = "mark" + index;//给div元素添加id
            div.className = "mark";//给div元素添加class
            div.style.left = x + "px";//div的css属性
            div.style.top = y + "px";

            p.appendChild(div);//把这个div元素追加到传过来的元素的下面

    },
    bindEvent(event){
         let oEvent = event;
         let container = this.getid("an_container");

         let offset = this.getOffset(container);  //获取坐标

         console.log(offset);

         console.log("eventClienty:"+oEvent.clientY);
         let scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
         console.log("scrollTop:"+scrollTop);
         let x = oEvent.clientX - offset.x;      //用浏览器窗口的可视区域减去getOffset函数返回的x值
         let y = oEvent.clientY - offset.y + scrollTop;
         let i = this.anArr.length;
         this.addMark(container, x, y, i);//添加标记
         this.mark.x = x;
         this.mark.y = y;
         let an_arr = sessionStorage['an_arr']   //获取本地存储里面的值
         if(an_arr !='' && an_arr != undefined){
              this.anArr = JSON.parse(an_arr)
            }else{
              this.anArr = [];
              console.log('本地存储没有值')
          }
         console.log(this.mark)
         this.anArr.push(this.mark);//把x，y坐标放到数组里面
         this.saveMark();//调用本地存储的函数
    },
    saveMark(){
      let an_arr = JSON.stringify(this.anArr);
      console.log('保存至对话存储', an_arr);
      sessionStorage['an_arr'] = an_arr;
    },
    loadMark(){
      // 加载已打点标记
      let arr = sessionStorage['an_arr']//获取本地存储里面的值
      if(arr !='' && arr != undefined){
                console.log(arr)
                this.an_arr = JSON.parse(arr)
            }else{
                this.an_arr = [];
                console.log('本地存储没有值')
            }
      if(this.anArr) {//判断本地存储里面是否有值
                let container = this.getid("container");
                for(var i = 0; i < this.anArr.length; i++) {
                    console.log(this.anArr[i])
                    addMark(container, this.anArr[i].x, this.anArr[i].y, i);//在页面上创建和本地存储对应的内容
                }
      }

    },
    clearMark(){
      // 清除加载的打点
      arr = sessionStorage['an_arr']   //获取本地存储里面的值
      if(arr !='' && arr != undefined){
          this.an_arr = JSON.parse(arr)
      }
      if(arr.length === 0) {
                alert('标记已全部清除')
      } else {
      let container = this.getid("container");
      let i = this.an_arr.length - 1;
      console.log(i);
      container.removeChild(this.getid("mark" + i));
      this.an_arr.length = this.an_arr.length - 1;
          this.saveMark();
      }
    }
  },
  mounted() {
    //this.drawImg();
  },
}
</script>

<style>
#an_container{
  position: relative;
  overflow: hidden;
  width: 1200px;
  height: 800px;
}
#an_map{
  position: absolute;
}
@keyframes pulse {
    0% {
        -webkit-transform: scale(0);
        transform: scale(0)
    }

    100% {
        -webkit-transform: scale(1);
        opacity: 0
    }
}
.mark{
            position: absolute;
            width: 20px;
            height: 20px;
            font-size: 0px;
            background: url("http://pa7krp3eu.bkt.clouddn.com/u3147.png") no-repeat left center;
            background-size:100% ;
        }
.mark::before{
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgb(254, 203, 202);
  border-radius: 50%;
}
.mark::after{
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 4px solid #eee;
  background-color: transparent;
  box-shadow: 0 0 5px #000;
  top: -50%;
  left: -50%;
  animation: pulse 2s infinite;
  transform-origin: center center;
}
</style>