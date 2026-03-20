<template>
<!-- v-bind:style="{backgroundImage:'url('+loadImg+')',backgroundRePeat:'no-repeat',backgroundSize:'100% 100%'}" -->
  <div class="lunbo" >
      <van-swipe class="my-swipe-isWechatwoerk" :autoplay="3000" v-if="isWechatwoerk">
        <van-swipe-item v-for="(item, index) in imageList" :key="index" >
          <a :href="`wxwork://openurl?url=${encodeURIComponent( url + item.wechaturl)}`">
            <!-- <img v-if!="imgShow" class="imgPattern" v-lazy="item.pcpic" :onerrpr="loadImg" /> -->
            <!-- v-if="imgShow == true&&item.pcpic.indexOf('.thumb.jpg') != -1" -->
            <img v-if="item.pcpic.indexOf('.thumb.jpg') != -1" :class="isAndroid?'android-imgPattern':'ios-imgPattern'" :src="item.pcpic.split('.thumb.jpg')[0]" alt="" />
            <img v-else :class="isAndroid?'android-imgPattern':'ios-imgPattern'" :src="item.pcpic" alt="" />
            <span class="imgWord" :style="{'top':u.indexOf('wxworklocal') != -1 ? '140px' : '185px'}">
              <span class="imgWordWidth">{{item.title}}</span>
            </span>
          </a>
        </van-swipe-item>
      </van-swipe>
      <van-swipe class="my-swipe" :autoplay="3000" v-else>
        <van-swipe-item v-for="(item, index) in imageList" :key="index" >
          <a :href="`wxwork://openurl?url=${encodeURIComponent( url + item.wechaturl)}`">
            <!-- <img v-if!="imgShow" class="imgPattern" v-lazy="item.pcpic" :onerrpr="loadImg" /> -->
            <!-- v-if="imgShow == true&&item.pcpic.indexOf('.thumb.jpg') != -1" -->
            <img v-if="item.pcpic.indexOf('.thumb.jpg') != -1" class="imgPattern" :src="item.pcpic.split('.thumb.jpg')[0]" alt="" />
            <img v-else  class="imgPattern" :src="item.pcpic" alt="" />
            <span class="imgWord" :style="{'top':u.indexOf('wxworklocal') != -1 ? '140px' : '185px'}">
              <span class="imgWordWidth">{{item.title}}</span>
            </span>
          </a>
        </van-swipe-item>
      </van-swipe>
  </div>
</template>

<script>
// import Cookies from "js-cookie";
export default {
  data(){
    return {
      imageList:[
      ],
      empcode:"",
      token:"",
      wxcode:"",
      url: process.env.VUE_APP_BASE_URL,
      imgShow:false,
      u:'',
      // loadImg:require('@/assets/images/image2.png'),
      isAndroid:/Android/i.test(navigator.userAgent),
      isWechatwoerk:/wxworklocal/i.test(navigator.userAgent)
    };
  },
  watch:{
    // imageList: function(){
    //   var that = this;
    //   that.$nextTick(function(){
    //     this.imgShow = true;
    //   })
    // },
  },
  created(){
    this.getImgUrl();
    this.u = navigator.userAgent;
  },
  mounted(){
  },
  activated(){
    // this.getImgUrl();
  },
  methods:{
    openImgUrl(item){
      // var homeUrl = window.location.host;
      // var imgUrl = 'https://' + homeUrl +'/' + item.wechaturl;
      // window.open(imgUrl,'_blank');
      wx.invoke('openDefaultBrowser', {
        'url': item.wechaturl
      }, function(res) {
        if (res.err_msg != 'openDefaultBrowser:ok') {
          console.log('错误')
        }
      })
    },
    getToken(wxcode) {
      var params = {
        code: wxcode,
        appid:"XW01",
      };
      this.$httpFetch.post("800100", params).then(res => {
        if (res.data.errcode == "000000") {
          this.empcode = res.data.body.empcode;
          this.token = res.data.body.token;
          // Cookies.set("news-empcode", res.data.body.empcode);
          // Cookies.set("news-token", res.data.body.token);
          this.getImgUrl();
          
        }else{
          // alert('获取失败');
        }
      });
    },
    getImgUrl(){
      this.imgShow = false;
      var params = {
      };
      this.$httpFetch.post("700234", params).then(res => {
        if (res.data.errcode == "000000") {
          this.imageList = res.data.body.list;
          console.log("this.imageList:",this.imageList)
        }else{
          // alert('res.data.body.errmsg');
        }
      });
    },
  },

}
</script>

<style lang="scss" scoped>
.lunbo{
  flex: 1;
  overflow: hidden;
  height:170px;
  width: 100%;
  background-color: #E9E9E9;
}
.my-swipe{
  height: 100%; 
  overflow-y: hidden;
};
.my-swipe-isWechatwoerk{
  height: 170px;
  overflow-y: hidden;
}
//图片的文字
.imgWord {
  position: absolute;
  // top:185px;
  // text-align:center;
  left: 20px;
  width:80%;
  height: 14px;
  line-height: 20px;
  // background: #;
  display: flex;
  justify-content: left;
}
//文字的宽度
.imgWordWidth{
  position:absolute;
  font-size: 16px;
  display: flex;
  // align-items: center;
  // justify-content: center;
  padding-left: 10px;
  width:80%;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  display: block;
  z-index:100;
  color: #fff;
}
.ios-imgPattern{
  width:100%;
  height:100%;
  object-fit:cover;
  position:absolute;
  z-index:99;
  padding:0px;
  object-position: center center;
  // transform: translateY(-18%);
}

.android-imgPattern{
  width:100%;
  height:100%;
  object-fit:cover;
  position:absolute;
  z-index:99;
  padding:0px
}

.imgPattern{
  width:100%;
  height:100%;
  object-fit:cover;
  position:absolute;
  z-index:99;
  padding:0px
}

/deep/.van-swipe__indicators{
  /* align-items: right; */
  /* right: 10%; */
    left: 90%;
}
/deep/ .van-swipe{
  width: 100%;
}
/deep/ .van-swipe-item{
  width: 100%;
  padding: 0px;
}
/deep/ .van-swipe__indicator--active{
  background-color: #ffffff;
}
</style>