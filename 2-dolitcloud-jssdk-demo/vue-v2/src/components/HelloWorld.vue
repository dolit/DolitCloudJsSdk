<template>
    <div id="desktop"></div>
</template>

<script>
export default {
  name: 'HelloWorld ',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  },
  created() {
  },
  beforeDestroy() {
    // 组件即将被销毁前的操作
    cloudDesktopApp.exit();
  },
  mounted() {
    cloudDesktopApp.exit();
    var webServerAddr = "http://10.0.0.66:9000"; //渲染服务web地址
    var connToken = "00016c63e4841371ac73078bb710001";//从后台复制访问32位访问token
    var userKey = this.randomString(6);
     var reqTime = Math.ceil(new Date().getTime()/1000).toString(); //当前时间戳
    var userSign= this.getUserSign(reqTime,userKey);
    var LoadParams = {
          webServerAddr : webServerAddr, //流路的目标服务器地址
          reqTime       : reqTime,
          userKey       : userKey,
          userSign      : userSign,
          connToken     : connToken,
          connMode      : "sdk", //sdk模式
          runType       : "box", //app模式和box。3d类应用选box,可隐藏默认鼠标使用远端鼠标
          pointerMode   : "default", //鼠标模式，配合runType使用
          moveMode      : "default", //预留，不可更改
          loadSkin      : "default", //预留，不可更改
          forceRenderMode : "default",  //强制使用的渲染类型
          isForceControlMode:  false , // 是否强制抢占操控主控模式
          isShowViewOnlyMask: true , //进入旁观模式时是否显示遮罩
          isShowSetBar  :  true ,  //是否显示设置图标
          isShowKeyBar  :  true ,  //是否显示键盘图标
          isShowMenu    :  false , //是否默认显示菜单
          isShowParam   :  false , //是否默认显示延迟等参数
          isShortcutParam: true ,  //是否启用显示隐藏延迟参数的快捷键
          isShowPointer :  true ,  //是否显示用户浏览器所在系统的鼠标
          isShowRemotePointer :  false ,  // 是否显示远端所在系统的鼠标
          isReceiveAudio:  true ,  //是否接受音频
          isAutoExists:  1 ,  //页面没有人操作超过一定时间时是否自动退出
          isShowReloadMenu : true, //是否显示重启按钮
          isShowDefinitionMenu : true, //是否显示清晰度列表
          isShowScaleMenu : true, //是否显示画面比例菜单
          isShowResolutionMenu : true, //是否显示分辨率列表
          isShowShortcutControlMenu : true,//是否显卡控制按钮
          menuPositionMode : "top", //菜单开关按钮默认位置
          menuStyleMode : "oval", //菜单开关按钮默认样式
          moveHandle : "add_abs", // default add_abs,旧版本的cloudapp添加此参数兼容无限移动， 新版本如果使用whole模式废弃该参数
          mouseEventHandle : "whole", // default whole,支持whole的cloudapp使用whole,不支持的不设置或者使用default
          moveScaleFactor : 1, //鼠标移动灵敏度
          ignoreKeyboardKeys : [
            cloudDesktopApp.keyMsgType.KEYCODE_F11
          ], //键盘按下是不向服务器发送该按键消息的按键列表
        };
        var isShowUEMessageTestInput = true;
         //应用画面将显示在id为desktop对应的dom元素上
        cloudDesktopApp.loadApp("desktop",LoadParams);
        cloudDesktopApp.on('stat',function(data){
          var stat = data;
        });
        cloudDesktopApp.on('ready',function(){
          console.log('conn ready');
        });
        cloudDesktopApp.on('dataChannelConnected',function(data){
          console.log('conn dataChannelConnected');
          setTimeout(function () {
            $(".datachannel").show();
          },500);
          $("#sendChannelDataBtn").click(function(){
            var sendData = $("#sendChannelData").val();
            console.log("sendData",sendData);
            cloudDesktopApp.sendDataChannelMsg(true,cloudDesktopApp.tools.StrToUint8Array(sendData));
            $.alert({
              title: '提示',
              content: '发送成功!',
            });
          });
        });
        cloudDesktopApp.on('dataChannelDisConnected',function(data){
          console.log('conn dataChannelConnected');
        });
        cloudDesktopApp.on('dataChannel',function(isText,data){
          var dataStr = cloudDesktopApp.tools.Utf8ArrayToStr(data);
          var sendInput = $("#showChannelData").get(0);
          if(sendInput){
            sendInput.value = sendInput.value + "\n" + dataStr;
          }
        });
        $(".full").click(function(){
          cloudDesktopApp.appEnterFullscreen();
        });
        $(".exit").click(function(){
          cloudDesktopApp.appExitFullscreen();
        });
        cloudDesktopApp.on('exit',function(data){
          $(".datachannel").hide();
          console.log('exit success');
        });
  },
  methods: {
    randomString(strLen) {
      var strLen = strLen || 32
      var t = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678',
        a = t.length,
        n = ''
      for (let i = 0; i < strLen; i++) n += t.charAt(Math.floor(Math.random() * a))
      return n;
    },
    getUserSign(reqTime,userKey){
          var userSign  = hex_md5(hex_md5(userKey+"dolitclouddesktop")+hex_md5(userKey+reqTime));
          return userSign;
    },
    hide(){
      ele.style.display = "none";
    },
    checkIE(){
          var userAgent = navigator.userAgent;
          var isIE = userAgent.indexOf("compatible") > -1 && userAgent.indexOf("MSIE") > -1;
          var isIE11 = userAgent.indexOf('Trident')  > -1 && userAgent.indexOf("rv:11.0") > -1;
          if(isIE || isIE11){
            return true;
          }else{
            return false;
          }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
