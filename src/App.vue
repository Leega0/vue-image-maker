<template>
  <div id="app">
    <el-container>
      <el-header>
        header
      </el-header>
      <el-main>
          <div class="annotation-wrap">
              <div id="an_container">
                <div id="an_map"></div>
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
    }
  },
  methods: {
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
    bindEvent(){
       this.getid("an_map").onclick = function(oEvent) {//点击图片调用
         oEvent = oEvent || event;
         let container = getid("container");
         let offset = getOffset(container);  //获取坐标

         console.log("offsety:"+offset.y);
         console.log("eventClienty:"+oEvent.clientY);
         let scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
         console.log("scrollTop:"+scrollTop);
         let x = oEvent.clientX - offset.x;      //用浏览器窗口的可视区域减去getOffset函数返回的x值
         let y = oEvent.clientY - offset.y + scrollTop;
         let i = this.an_arr.length;
         this.addMark(container, x, y, i);//添加标记
         this.mark.x = x;
         this.mark.y = y;
         let an_arr = sessionStorage['an_arr']   //获取本地存储里面的值
         if(an_arr !='' && an_arr != undefined){
              this.an_arr = JSON.parse(an_arr)
            }else{
              this.an_arr = [];
              console.log('本地存储没有值')
          }
         console.log(this.mark)
         this.an_arr.push(mark);//把x，y坐标放到数组里面
         this.saveMark();//调用本地存储的函数
       }
    },
    saveMark(){
      an_arr = JSON.stringify(this.an_arr);
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
      if(this.an_arr) {//判断本地存储里面是否有值
                let container = this.getid("container");
                for(var i = 0; i < this.an_arr.length; i++) {
                    console.log(this.an_arr[i])
                    addMark(container, this.an_arr[i].x, this.an_arr[i].y, i);//在页面上创建和本地存储对应的内容
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
  }
}
</script>

<style>
</style>