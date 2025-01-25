<template>
    <div id="desktop"></div>
</template>

<script>
export default {
  name: 'sdk-demo ',
  data () {
    return {
      msg: 'sdk-demo'
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
    var webServerAddr= "http://127.0.0.1:8085";//流路的目标服务器地址

    var userKey = this.randomString(6);
    var reqTime = Math.ceil(new Date().getTime()/1000).toString(); //当前时间戳
    var userSign= this.getUserSign(reqTime,userKey);

    var loadType = "auto"; //应用加载模式 auto为按应用识别码自动加载,如果使用固定流路方式，则传stream,并设置connToken
    var groupNo = "abcu45168123"; //这里修改为后台云应用列表中的要加载的应用的应用识别码

    var connToken  = "";//connToken:流路访问token.如果url中已经存在，则此参数可留为空字符串.loadType参数是stream的时候必须设置此参数

    var sessionId = ""; //访问会话id,32位字符串，auto模式下，流路打开后，再次刷新页面或者打开新的标签页，如果sessionId不变，则一直访问同一个流路，如果更换sessionId了,则自动申请新的并发资源。如果不设置sessionId，默认每次都自动申请新的并发资源。

    var isOpenFixedSession = true; //auto加载模式下，并且sessionId为空的情况下，设置isOpenFixedSession=true,表示sessionId的生成使用系统默认规则，可以实现当前浏览器固定访问流路，刷新页面不申请新并发资源。

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
        loadType      : loadType,  //加载模式，支持管理端web端口 可选： auto stream 以及留空
        group         : groupNo,   //应用识别码 ，loadType为auto时填写应用识别码
        touchMode     : "default", //触摸模式，ue类支持触屏的应用可以改为touch
        scaleMode     : "default", //画面比例，支持height【适应高度】 width【适应宽度】 default【原始尺寸】 native【原始尺寸】 fill【铺满屏幕】 cover【保持比例铺满屏幕自动裁剪】
        forceRenderMode : "default",  //强制使用的渲染类型
        isForceControlMode:  false , // 是否强制抢占操控主控模式
        isShowViewOnlyMask: true , //进入旁观模式时是否显示遮罩
        isShowSetBar  :  true ,  //是否显示设置图标
        isShowHandleBar: true,  //移动端是否显示手柄
        isDefaultShowHandleBar: false, //移动端是否默认直接显示手柄，而不是通过菜单打开.如果该参数设置为true，则isShowHandleBar强制设置为true
        isShowKeyBar  :  true ,  //是否显示键盘图标
        isShowMenu    :  false , //是否默认显示菜单
        isShowParam   :  false , //是否默认显示延迟等参数
        isShortcutParam: true ,  //是否启用显示隐藏延迟参数的快捷键
        isShowPointer :  true ,  //是否显示用户浏览器所在系统的鼠标
        isShowRemotePointer :  false ,  // 是否显示远端所在系统的鼠标
        isReceiveAudio:  true ,  //是否接受音频
        isAutoExit:  1 ,  //页面没有人操作超过一定时间时是否自动退出
        isShowReloadMenu : true, //是否显示重启按钮
        isShowDefinitionMenu : true, //是否显示清晰度列表
        isShowScaleMenu : true, //是否显示画面比例菜单
        isShowResolutionMenu : true, //是否显示分辨率列表
        isShowShortcutControlMenu : true,//是否显示控制按钮
        menuPositionMode : "top", //菜单开关按钮默认位置
        menuStyleMode : "oval", //菜单开关按钮默认样式
        isShowUpControlButton : true, //旁观时是否显示升级主控按钮
        isAutoShowCnInput: false, //文字输入框类型是default时，是否点击内容输入框自动弹出
        isAutoEnterPointerLockWhenMousemove:false,//鼠标移动时是否自动进入鼠标锁定模式
        cnInputType: "auto", // 文字输入框类型 auto: 系统自动输入法，default:弹窗输入
        moveHandle : "add_abs", // default add_abs,旧版本的cloudapp添加此参数兼容无限移动， 新版本如果使用whole模式废弃该参数
        mouseEventHandle : "whole", // default whole,支持whole的cloudapp使用whole,不支持的不设置或者使用default
        openMicrophone: false,//开启麦克风，必须https方式
        moveScaleFactor : 1, //鼠标移动灵敏度
        defaultLang: "default",//默认语言 支持default chs cht en
        tipLevel: "debug", //错误提示级别，支持debug default。 访问异常时要显示更详细的错误提示请设置为debug
        sessionId: sessionId, //访问会话id,32位字符串，auto模式下，流路打开后，再次刷新页面或者打开新的标签页，如果sessionId不变，则一直访问同一个流路，如果更换sessionId了,则自动申请新的并发资源。如果不设置sessionId，默认每次都自动申请新的并发资源。
        isOpenFixedSession: isOpenFixedSession, //auto加载模式下，并且sessionId为空的情况下，设置isOpenFixedSession=true,表示sessionId的生成使用系统默认规则，可以实现当前浏览器固定访问流路，刷新页面不申请新并发资源。
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
