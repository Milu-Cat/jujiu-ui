<template>
  <div class="scroll-page" :style="{height: scrollPageHeight}">
    <div v-if="type === 'left' || type === 'sider'" class="sider"></div>
    <div v-else class="left"></div>
    <div ref="scrollBox" :id="`scrollBox${viewKey}`" class="scrollBox" :style="{paddingTop: paddingBox, maxWidth: scrollBoxMaxWidth}">
      <div v-for="(item, index) in scrollData" :key="index" class="blockDiv">
        <img :src="item.ico_path" :style="{width: scrollItemWidth, height: scrollItemHeight}">
      </div>
    </div>
    <div v-if="type === 'right' || type === 'sider'" class="sider"></div>
    <div v-else class="right"></div>
  </div>
</template>

<script>
export default {
  name: 'ScrollView',
  props: {
    viewKey: {
      type: Number,
      required: true
    },
    scrollItem: {
      type: Array,
      required: false,
      default: ()=>[]
    },
    scrollItemHeight: {
      type: String,
      required: false,
      default: '20px'
    },
    scrollItemWidth: {
      type: String,
      required: false,
      default: '20px'
    },
    paddingBox: {
      type: String,
      required: false,
      default: '16px'
    },
    scrollPageHeight: {
      type: String,
      required: false,
      default: '50px'
    },
    scrollBoxMaxWidth: {
      type: String,
      required: false,
      default: '180px'
    }
  },
  data(){
    return {
      type: 'left',
      scrollData: []
    }
  },
  watch: {
    scrollItem: {
      handler(newVal) {
        console.log(newVal)
        this.scrollData = newVal
        if(this.scrollData.length<=5){
          this.type = 'sider'
        }else {
          this.type = 'left'
        }
      },
      immediate: true
    }
  },
  mounted() {
    this.scroollMousewheel()
    this.initSider()
  },
  methods: {
    initSider(){
      console.log(this.scrollData)
      
    },
    scroollMousewheel(){
      let userAgent = navigator.userAgent
      let contaer = document.getElementById(`scrollBox${this.viewKey}`);
      // 谷歌
      if(userAgent.indexOf("Chrome") > -1){
        contaer.addEventListener(
          'mousewheel',
          this.fn,
          false
        )
      }
      // 火狐
      if(userAgent.indexOf("Firefox") > -1){
        contaer.addEventListener(
          'DOMMouseScroll',
          this.fn,
          false
        )
      }
    },
      fn(ev){
        let contaer = document.getElementById(`scrollBox${this.viewKey}`);
          ev = ev || event;
          //计算鼠标滚轮滚动的距离
          console.log(ev.detail)
          if(ev.wheelDelta){
            let v = -ev.wheelDelta / 10;
            contaer.scrollLeft += v;
          } else if(ev.detail){
            if(ev.detail>0){
              contaer.scrollLeft += 10;
            } else {
              contaer.scrollLeft -= 10;
            }
          }
          let scrollLeft = contaer.scrollLeft;
          let widthBox = contaer.clientWidth;
          let scrollWidth = contaer.scrollWidth;
          // console.log(scrollLeft);
          // console.log(widthBox);
          // console.log(scrollWidth);
          if (scrollWidth === widthBox + scrollLeft) {
            if(this.scrollData.length<=5)return
            // console.log("到右头了");
            this.type = 'right'
          } else if (scrollLeft === 0) {
            // console.log("到左头了");
            if(this.scrollData.length<=5)return
            this.type = 'left'
          } else {
            if(this.scrollData.length<=5)return
            this.type = 'center'
          }
          //阻止浏览器默认方法
          ev.preventDefault();
  },
  },
   destroyed: function () {
    window.removeEventListener('mousewheel', this.scroollMousewheel);
  }
};
</script>

<style scoped>
.scroll-page{
  width: 100%;
  /* height: 30px; */
  display: flex;
  /* background: red;   */
}
.scrollBox {
  height: 100%;
  overflow-x: scroll;
  box-sizing: border-box;
  /* padding-top: 16px; */
  overflow-y: hidden;
  white-space: nowrap; /* 不换行 */
  -ms-overflow-style: none;
  scrollbar-width: none;
}
.isRight{
   margin-left: 100px;
  width: 180px;
  height: 100%;
  overflow-x: scroll;
  box-sizing: border-box;
  padding-top: 4px;
  overflow-y: hidden;
  white-space: nowrap; 
  -webkit-box-shadow:3px 0px 0px #c8c8c8 inset;
  box-shadow:3px 0px 0px #c8c8c8 inset;
}
.blockDiv {
  width: 20px;
  display: inline-table;
  padding: 0 5px;
}
::-webkit-scrollbar {
  display: none; 
}
.sider,.left,.right{
  width:20px;
  height: 100%;
}
.left{
  box-shadow:2px 0px 3px 0px #c8c8c8;
  z-index: 10000;
}
.right{
  box-shadow:-2px 0px 3px 0px #c8c8c8;
  z-index: 10000;
}
</style>
