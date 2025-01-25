### JS-SDK 使用说明

### 1 引入文件

在您的页面中引入以下js以及css文件（从SDK示例中获取）

```html

    <link rel="stylesheet" href="./static/css/bootstrap.min.css">
    <link rel="stylesheet" href="./static/css/jquery-confirm.min.css" >
    <link rel="stylesheet" href="./static/css/webapp.css?v=7896112516">
        
    <script type="text/javascript" src="./static/js/jquery1.10.min.js"></script>
    <script type="text/javascript" src="./static/js/bootstrap.min.js"></script>
    <script type="text/javascript" src="./static/js/jquery-confirm.min.js"></script>
    <script type="text/javascript" src="./static/js/jquery.qrcode.min.js"></script>
    <script type="text/javascript" src="./static/js/custom-protocol-check.min.js"></script>
    <script type="text/javascript" src="./static/js/adapter-8.0.js"></script>
    <script type="text/javascript" src="./static/js/webstream.min.js?v=1.312717c741"></script>
    <script type="text/javascript" src="./static/js/webaudio.min.js?v=20223330"></script>
    <script type="text/javascript" src="./static/js/dolitcloud.pack.min.js?v=1.369169"></script>
    <script type="text/javascript" src="./static/js/vconsole.min.js?v=980666"></script>
    <script type="text/javascript" src="./static/js/md5.js"></script>

```

### 2 定义渲染元素

    html的body中添加id为desktop的div元素
    ```html
        <body>
            <div id="desktop"></div>
        </body>
    ```

### 3 调用sdk示例

#### 调用示例
```javascript
        
        //webServerAddr变量填写云流管理平台的web服务地址，端口号默认是8085
        var webServerAddr= "http://127.0.0.1:8085/";
         //下面三个参数不建议在前端生成，而是由后端生成，渲染到前端，或者ajax异步从后端获取
        var reqTime = Math.ceil(new Date().getTime()/1000).toString(); //当前时间戳
        var userKey = "153d6d17"; //请求key，随机字符串
        var userSign= getUserSign(reqTime,userKey); //请求Sign,生成算法 userSign = Md5(Md5(userKey+"dolitclouddesktop")+Md5(userKey+reqTime))，参考下面示例中的getUserSign函数
        
        var loadType = "group"; //应用加载模式 group为按应用识别码自动加载
        var groupNo = "abcu45168123"; //这里修改为后台云应用列表中的要加载的应用的应用识别码

        var connToken  = "";//connToken:流路访问token.如果url中已经存在，则此参数可留为空字符串.loadType参数是stream的时候必须设置此参数

        var sessionId = ""; //访问会话id,32位字符串，auto模式下，流路打开后，再次刷新页面或者打开新的标签页，如果sessionId不变，则一直访问同一个流路，如果更换sessionId了,则自动申请新的并发资源。如果不设置sessionId，默认每次都自动申请新的并发资源。

        var isOpenFixedSession = true; //auto加载模式下，并且sessionId为空的情况下，设置isOpenFixedSession=true,表示sessionId的生成使用系统默认规则，可以实现当前浏览器固定访问流路，刷新页面不申请新并发资源。

        var LoadParams = {
             webServerAddr : webServerAddr, 
             reqTime       : reqTime,
             userKey       : userKey,
             userSign      : userSign,
             connToken     : connToken,
             connMode      : "sdk",
             runType       : "app",
             pointerMode   : "default",
             moveMode      : "default",
             touchMode     : "default",
             scaleMode     : "default",
             loadSkin      : "default",
             loadType      : loadType,  
             group         : groupNo,
             forceRenderMode : "default",
             isForceControlMode:  false,  
             isShowViewOnlyMask: true,   
             isShowSetBar  :  true ,
             isShowHandleBar  :  true ,  
             isDefaultShowHandleBar  : true,
             isForceHorizontal : true, 
             isShowKeyBar  :  true ,
             isShowMenu    :  false ,   
             isShowParam   :  false ,  
             isShortcutParam: true ,  
             isShowPointer :  true ,    
             isShowRemotePointer :  false , 
             isReceiveAudio:  true ,
             isAutoExit:  1 ,
             isShowReloadMenu : true,
             isAutoShowCnInput: true,
             sessionId: sessionId,
             isOpenFixedSession: isOpenFixedSession,
             defaultLang: "chs",
             tipLevel: "debug",
             joystickLeftKey : cloudDesktopApp.keyMsgType.KEYCODE_LEFT_ARROW,
             joystickRightKey : cloudDesktopApp.keyMsgType.KEYCODE_RIGHT_ARROW,
             joystickDownKey : cloudDesktopApp.keyMsgType.KEYCODE_DOWN_ARROW,
             joystickUpKey : cloudDesktopApp.keyMsgType.KEYCODE_UP_ARROW,
             ignoreKeyboardKeys : [
                     cloudDesktopApp.keyMsgType.KEYCODE_F11
             ], 
        };
        var dataChannelConnected = false;
        //应用画面将显示在id为desktop对应的dom元素上
        cloudDesktopApp.loadApp("desktop",LoadParams);
        cloudDesktopApp.on('stat',function(data){
            var stat = data;
        });
        cloudDesktopApp.on('ready',function(){
            console.log('conn ready');
        });
        cloudDesktopApp.on('exit',function(data){
            $(".datachannel").hide();
            console.log('exit success');
        });
        cloudDesktopApp.on('dataChannelConnected',function(data){
          console.log('conn dataChannelConnected');
          dataChannelConnected = true;
        });
        cloudDesktopApp.on('dataChannelDisConnected',function(data){
          console.log('conn dataChannelConnected');
          dataChannelConnected = false;
        });
        cloudDesktopApp.on('dataChannel',function(isText,data){
          var dataStr = cloudDesktopApp.tools.Utf8ArrayToStr(data);
          console.log("dataStr",dataStr);
          //发送数据
          var sendData = "text";
          cloudDesktopApp.sendDataChannelMsg(true,cloudDesktopApp.tools.StrToUint8Array(sendData));

        });
        function getUserSign(reqTime,userKey){
            var userSign  = hex_md5(hex_md5(userKey+"dolitclouddesktop")+hex_md5(userKey+reqTime));
            return userSign;
        }

```

#### 摇杆对应的键配置示例
```js
//wasd
var LoadParams = {
        joystickLeftKey : cloudDesktopApp.keyMsgType.KEYCODE_A,
        joystickRightKey : cloudDesktopApp.keyMsgType.KEYCODE_D,
        joystickDownKey : cloudDesktopApp.keyMsgType.KEYCODE_S,
        joystickUpKey : cloudDesktopApp.keyMsgType.KEYCODE_W,
    };
//上下左右箭头
var LoadParams = {
        joystickLeftKey : cloudDesktopApp.keyMsgType.KEYCODE_LEFT_ARROW,
        joystickRightKey : cloudDesktopApp.keyMsgType.KEYCODE_RIGHT_ARROW,
        joystickDownKey : cloudDesktopApp.keyMsgType.KEYCODE_DOWN_ARROW,
        joystickUpKey : cloudDesktopApp.keyMsgType.KEYCODE_UP_ARROW,
    };
```

### 4 函数列表

   ####  加载渲染服务
```text
cloudDesktopApp.loadApp(domid,params);
参数：
    domid : 指定id的div元素
        类型： 字符串
    params：服务的初始化参数
        类型： json对象
         对象参数说明：
              webServerAddr : 服务地址  【字符串】
              reqTime       : 秒级时间戳,        【字符串】
              userKey       : 请求key:随机字符串, 【字符串】
              userSign      : 请求sign:生成算法 userSign = Md5(Md5(userKey+"dolitclouddesktop")+Md5(userKey+reqTime)) 【字符串】
              connToken     : 连接token,请通过api或者后台流路列表中创建token, 【字符串】
              connMode      : sdk调用固定为sdk 【字符串】
              runType       : 可选值为app 或者box。运行模式，如果要支持鼠标无限移动的游戏类应用可以设置为box 【字符串】
              pointerMode   : 可选值为default, 【字符串】
              moveMode      : 可选值为default, 【字符串】
              touchMode     : 触摸模式，ue类支持触屏的应用可以改为touch  【字符串】
              scaleMode     : 画面比例，支持height【适应高度】 width【适应宽度】 default【原始尺寸】 native【原始尺寸】 fill【铺满屏幕】 cover【保持比例铺满屏幕自动裁剪】
              loadSkin      : 可选值为default,【字符串】
              loadType      :  加载模式，支持管理端web端口 可选： auto stream 以及留空
              group         :  应用识别码 ，loadType为auto时填写应用识别码
              forceRenderMode : 可选值为default 【字符串】
              isShowViewOnlyMask: 是否显示旁边提示的遮罩   【布尔值 true or false】
              isShowSetBar  :  是否显示设置按钮  【布尔值 true or false】
              isShowHandleBar  :  移动端是否显示手柄
              isDefaultShowHandleBar  : 移动端是否默认直接显示手柄，而不是通过菜单打开.如果该参数设置为true，则isShowHandleBar强制设置为true
              isForceHorizontal :  移动端是否强制横屏 【布尔值 true or false】
              isShowKeyBar  :  是否显示键盘按钮  【布尔值 true or false】
              isShowMenu    :  是否默认显示菜单    【布尔值 true or false】
              isShowParam   :  是否默认显示网络参数  【布尔值 true or false】
              isShortcutParam: 是否启用显示隐藏延迟参数的快捷键 【布尔值 true or false】
              isShowPointer :  /是否显示用户浏览器所在系统的鼠标 【布尔值 true or false】
              isReceiveAudio:  是否接受音频数据 【布尔值 true or false】
              isAutoExit:  长时间无操作是否自动退出画面 【布尔值 true or false】
              isShowDefinitionMenu : 是否显示清晰度列表 【布尔值 true or false】
              isShowScaleMenu : 是否显示画面比例菜单 【布尔值 true or false】
              isShowResolutionMenu : 是否显示分辨率列表 【布尔值 true or false】
              isShowShortcutControlMenu : 是否显示控制按钮 【布尔值 true or false】
              isShowReloadMenu :  是否显示重启应用按钮 【布尔值 true or false】
              defaultLang: 默认语言 支持default【自适应】 chs【中文简体】 cht【中文繁体】 en【英文】
              tipLevel: 错误提示级别，访问异常时要显示更详细的错误提示请设置为debug  支持【debug】 调试模式 【default】 默认模式
              sessionId: 访问会话id,32位字符串，auto模式下，流路打开后，再次刷新页面或者打开新的标签页，如果sessionId
              isOpenFixedSession: auto加载模式下，并且sessionId为空的情况下，设置isOpenFixedSession=true,表示sessionId的生成使用系统默认规则，可以实现当前浏览器固定访问流路，刷新页面不申请新并发资源
              joystickLeftKey : 手柄向左滑动模拟的键盘按键【默认 cloudDesktopApp.keyMsgType.KEYCODE_A】
              joystickRightKey : 手柄向右滑动模拟的键盘按键【默认cloudDesktopApp.keyMsgType.KEYCODE_D】
              joystickDownKey : 手柄向下滑动模拟的键盘按键【默认cloudDesktopApp.keyMsgType.KEYCODE_S】
              joystickUpKey : 手柄向上滑动模拟的键盘按键【默认cloudDesktopApp.keyMsgType.KEYCODE_W】
              ignoreKeyboardKeys : 不向服务器传递的键盘按钮，具体支持的按键加 键盘常量列表 【cloudDesktopApp.keyMsgType的数组】
               [
                      cloudDesktopApp.keyMsgType.KEYCODE_F11
               ]
```

   #### 重连服务  
```text
 cloudDesktopApp.reconnect()
 参数：
    无。【自动使用第一次loadApp函数的params】
```

   #### 退出服务  
```text
  cloudDesktopApp.exit()
    参数：
       无。【自动使用第一次loadApp函数的params】
```
   #### 获取网络参数 
```text
  cloudDesktopApp.getAppStat()
    参数：
        无。
    返回值示例：
        {
            "jitterBufferDelay": 0,
            "framesBufferDelay": 1.21,
            "lossRate": 0,
            "rtt": 0.39,
            "fps": 0,
            "bitrate": 1.86,
            "resolutionWidth": 1920,
            "resolutionHeight": 1080,
            "frameTransDelay": 0.19,
            "endToEndDelay": 11.6,
            "renderDelay": 10
        }
    返回值参数说明：
        jitterBufferDelay：抖动延迟
        framesBufferDelay：解码缓冲区的延迟
        frameTransDelay：帧传输延迟
        renderDelay：渲染延迟
        endToEndDelay：端到端延迟
        lossRate：丢包率
        rtt：往返时延
        fps：帧率
        bitrate：码率
        resolutionWidth：获取分辨率-宽度
        resolutionHeight：获取分辨率-高度
```
   #### 切换当前响应的鼠标方向 【常用于移动端】  
```text
  cloudDesktopApp.setMouseDirection(mouseDirection)
       参数：
            鼠标方向 【int】
            可选值：
                cloudDesktopApp.mouseMsgType.MOUSE_CHOOSE_DIRECTION_LEFT 鼠标左键
                cloudDesktopApp.mouseMsgType.MOUSE_CHOOSE_DIRECTION_MIDDLE 鼠标滚轮
                cloudDesktopApp.mouseMsgType.MOUSE_CHOOSE_DIRECTION_RIGHT  鼠标右键
```

   #### 获取当前响应的鼠标方向 【常用于移动端】  
```text
   cloudDesktopApp.getMouseDirection()
   参数：
        无
   返回值：获取当前响应的鼠标方向 【int】
            cloudDesktopApp.mouseMsgType.MOUSE_CHOOSE_DIRECTION_LEFT 鼠标左键
            cloudDesktopApp.mouseMsgType.MOUSE_CHOOSE_DIRECTION_MIDDLE 鼠标滚轮
            cloudDesktopApp.mouseMsgType.MOUSE_CHOOSE_DIRECTION_RIGHT  鼠标右键
```

   #### 进入全屏  
```text
    cloudDesktopApp.appEnterFullscreen()
    参数：
       无。
```

   #### 退出全屏  
```text
   cloudDesktopApp.appExitFullscreen()
   参数：
      无。                 
```

   #### 显示网络参数  
```text
      cloudDesktopApp.showParam()
      参数：
         无。 
```

   #### 隐藏网络参数  
```text
    cloudDesktopApp.hideParam()
    参数：
    	无。 
```

   #### 显示菜单  
```text
    cloudDesktopApp.showMenu()
    参数：
    	无。 
```

   #### 隐藏菜单  
```text
    cloudDesktopApp.hideMenu()
    参数：
   	 无。 
```

   #### 显示键盘  
```text
    cloudDesktopApp.showKeyboard()
    参数：
    	无。 
```

   #### 隐藏键盘  
```text
    cloudDesktopApp.hideKeyboard()
    参数：
   	 无。
```

   #### 显示手柄
```text
    cloudDesktopApp.showJoystick()
    参数：
   	 无。 
```

   #### 隐藏手柄  
```text
    cloudDesktopApp.hideJoystick()
    参数：
    	无。 
```

   #### 显示加载失败提示 
```text
    cloudDesktopApp.showLoadError(msg)
    参数：
    	msg: 加载失败提示
```

   #### 显示加载进度提示 
```text
    cloudDesktopApp.showLoadProcess(msg)
    参数：
   	 msg: 加载进度提示 
```

   #### 暂停串流  
```text
    cloudDesktopApp.pauseStream()
    参数：
    	无。
```

   #### 继续串流  
```text
    cloudDesktopApp.resumeStream()
    参数：
    	无。    
```


   #### 消息通道发送消息给应用  
```text
	cloudDesktopApp.sendDataChannelMsg(isText,data)
    参数：
        isText ： 是否是文本类型
        data : 发送的数据,发送时需调用cloudDesktopApp.tools.StrToUint8Array函数转成Unit8Array类型
	示例：
		var sendData = "text";
		cloudDesktopApp.sendDataChannelMsg(true,cloudDesktopApp.tools.StrToUint8Array(sendData));
``` 

   #### 重启应用  
```text
    cloudDesktopApp.restartApp()
    参数：
   	 无。   
```


   #### 修改分辨率  
```text
    cloudDesktopApp.setResolution(width,height)
    参数：
    	width:  要设置的宽度， int类型，10-8192之间。    
        height:  要设置的高度， int类型，10-8192之间。
```

   #### 发送组合键
```text
    cloudDesktopApp.sendCombinationKey(type)
    参数：组合键类型  【int】
        0:windows
        1:windows + L
        2:ctrl + alt + delete
        3:ctrl + alt + end
```
   #### 发送自定义键值
```text
    cloudDesktopApp.sendCustomKey(keyCode,isDown)
    参数：
    keyCode: 要发送的按键的keycode只，支持以下下9个键值
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_UP_ARROW,
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_DOWN_ARROW,
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_LEFT_ARROW,
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_RIGHT_ARROW,
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_W,
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_A,
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_S,
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_D,
            cloudDesktopApp.KEYMSGTYPE.KEYCODE_SPACE,
    isDown：是否按下。按下按键设置为true,释放设置为false
```

### 5 回调事件

   #### ready事件：渲染服务加载成功

```javascript
cloudDesktopApp.on('ready',function(){
    console.log('conn ready');
});
```

   #### exit事件：渲染服务退出

```javascript
cloudDesktopApp.on('exit',function(data){
    console.log('exit success');
});
```

   #### stat事件：实时获取网络参数
```javascript
cloudDesktopApp.on('stat',function(data){
    //实时获取网络参数
    //获取抖动延迟
    let jitterBufferDelay = data.jitterBufferDelay;
    //获取解码缓冲区的延迟
    let framesBufferDelay = data.framesBufferDelay;
    //获取帧传输延迟
    let frameTransDelay = data.frameTransDelay;
    //获取渲染延迟
    let renderDelay = data.renderDelay;
    //获取端到端延迟
    let endToEndDelay = data.endToEndDelay;
    //获取丢包率
    let lossRate = data.lossRate;
    //获取往返时延
    let rtt = data.rtt;
    //获取帧率
    let fps = data.fps;
    //获取码率
    let bitrate = data.bitrate;
    //获取分辨率-宽度
    let resolutionWidth = data.resolutionWidth;
    //获取分辨率-高度
    let resolutionHeight = data.resolutionHeight;
});
```
   #### streamPause事件：串流暂停
```javascript
cloudDesktopApp.on('streamPause',function(){
    console.log('streamPause');
});
```

   #### streamResume事件：串流继续
```javascript
cloudDesktopApp.on('streamResume',function(){
    console.log('streamResume');
});
```

   #### appClosed事件：应用关闭
```javascript
cloudDesktopApp.on('appClosed',function(){
    console.log('appClosed');
});
```

   #### clientKilled事件：客户端被退出
```javascript
cloudDesktopApp.on('clientKilled',function(){
    console.log('clientKilled');
});
```

   #### dataChannelConnected事件：消息通信连接成功
```javascript
cloudDesktopApp.on('dataChannelConnected',function(){
    console.log('dataChannelConnected');
});
```

   #### dataChannelDisConnected事件：消息通信断开连接
```javascript
cloudDesktopApp.on('dataChannelDisConnected',function(){
    console.log('dataChannelDisConnectedy');
});
```

   #### dataChannel事件：实时消息发送
```javascript
cloudDesktopApp.on('dataChannel',function(isText,data){
	 var dataStr = cloudDesktopApp.tools.Utf8ArrayToStr(data);
     console.log(isText,dataStr);
 });
```

### 6 键盘常量列表

```text
KEYCODE_BACKSPACE : 8,
KEYCODE_TAB    : 9,
KEYCODE_ENTER  : 13,
KEYCODE_SHIFT : 16,
KEYCODE_CTRL : 17,
KEYCODE_ALT : 18,
KEYCODE_PAUSE_BREAK : 19,
KEYCODE_CAPS_LOCK : 20,
KEYCODE_ESCAPE : 27,
KEYCODE_SPACE : 32,
KEYCODE_PAGE_UP : 33,
KEYCODE_PAGE_DOWN : 34,
KEYCODE_END : 35,
KEYCODE_HOME : 36,
KEYCODE_LEFT_ARROW : 37,
KEYCODE_UP_ARROW : 38,
KEYCODE_RIGHT_ARROW : 39,
KEYCODE_DOWN_ARROW : 40,
KEYCODE_INSERT : 45,
KEYCODE_DELETE : 46,
KEYCODE_F1 : 112,
KEYCODE_F2 : 113,
KEYCODE_F3 : 114,
KEYCODE_F4 : 115,
KEYCODE_F5 : 116,
KEYCODE_F6 : 117,
KEYCODE_F7 : 118,
KEYCODE_F8 : 119,
KEYCODE_F9 : 120,
KEYCODE_F10 : 121,
KEYCODE_F11 : 122,
KEYCODE_F12 : 123,
KEYCODE_0 : 48,
KEYCODE_1 : 49,
KEYCODE_2 : 50,
KEYCODE_3 : 51,
KEYCODE_4 : 52,
KEYCODE_5 : 53,
KEYCODE_6 : 54,
KEYCODE_7 : 55,
KEYCODE_8 : 56,
KEYCODE_9 : 57,
KEYCODE_A : 65,
KEYCODE_B : 66,
KEYCODE_C : 67,
KEYCODE_D : 68,
KEYCODE_E : 69,
KEYCODE_F : 70,
KEYCODE_G : 71,
KEYCODE_H : 72,
KEYCODE_I : 73,
KEYCODE_J : 74,
KEYCODE_K : 75,
KEYCODE_L : 76,
KEYCODE_M : 77,
KEYCODE_N : 78,
KEYCODE_O : 79,
KEYCODE_P : 80,
KEYCODE_Q : 81,
KEYCODE_R : 82,
KEYCODE_S : 83,
KEYCODE_T : 84,
KEYCODE_U : 85,
KEYCODE_V : 86,
KEYCODE_W : 87,
KEYCODE_X : 88,
KEYCODE_Y : 89,
KEYCODE_Z : 90,
KEYCODE_SUB : 189,
KEYCODE_EQUAL : 187,
KEYCODE_LEFT_BRACKET : 219,
KEYCODE_RIGHT_BRACKET : 221,
KEYCODE_Y_AXIS : 220,
KEYCODE_SEMICOLON : 186,
KEYCODE_UP_COMMA : 222,
KEYCODE_DOWN_COMMA : 188,
KEYCODE_POINT : 190,
KEYCODE_QUESTION : 191,
KEYCODE_BACK_QUOTE  : 192,
KEYCODE_NUM_0 : 96,
KEYCODE_NUM_1 : 97,
KEYCODE_NUM_2 : 98,
KEYCODE_NUM_3 : 99,
KEYCODE_NUM_4 : 100,
KEYCODE_NUM_5 : 101,
KEYCODE_NUM_6 : 102,
KEYCODE_NUM_7 : 103,
KEYCODE_NUM_8 : 104,
KEYCODE_NUM_9 : 105,
KEYCODE_NUM_MUL : 106,
KEYCODE_NUM_ADD : 107,
KEYCODE_NUM_ENTER : 108,
KEYCODE_NUM_SUB : 109,
KEYCODE_NUM_DOT : 110,
KEYCODE_NUM_DIV : 111,
KEYCODE_NUM_LOCK : 144,
```