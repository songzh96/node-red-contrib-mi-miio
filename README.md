# node-red-contrib-mi-miio

[![npm version](https://badge.fury.io/js/node-red-contrib-mi-miio.svg)](https://badge.fury.io/js/node-red-contrib-mi-miio)

这是一个Node-RED的小米MiIO设备的插件。
   
Bug反馈/建议等请提交[issues](https://github.com/YinHangCode/node-red-contrib-mi-miio/issues) 或 [QQ群: 107927710](//shang.qq.com/wpa/qunwpa?idkey=8b9566598f40dd68412065ada24184ef72c6bddaa11525ca26c4e1536a8f2a3d)。

## 支持设备
||设备名称|Device Name|MiIO model|
|:-:|:-|:-|:-|
|1|米家智能插线板|MiIntelligencePinboard|zimi.powerstrip.v2|
|2|Yeelight彩光灯带|YeelightColorLedStrip|yeelink.light.strip1<br>yeelink.light.strip2|
|3|智米直流变频落地扇|ZhiMiDCVariableFrequencyFan|zhimi.fan.v3|

更多设备待添加中。。。

## 安装
确认安装好Node-RED之后，执行如下命令：
```
npm install -g node-red-contrib-mi-miio
```

## 输入/输出消息说明
### 获取设备属性列表
输入内容：
```
{
    "cmd": "list"
}
```
![](https://raw.githubusercontent.com/YinHangCode/node-red-contrib-mi-miio/master/images/list.png)
输出内容：
```
{
    "cmd": "list_response",
    "attributes": ["power", "power_consume_rate", "temperature", "wifi_led"]
}
```
### 获取设备属性值
输入内容：
```
{
    "cmd": "get"
}
```
![](https://raw.githubusercontent.com/YinHangCode/node-red-contrib-mi-miio/master/images/get.png)   
输出内容：
```
{
    "cmd": "get_response",
    "values": {
        "power": "off",
        "power_consume_rate": 0,
        "temperature": 41.11,
        "wifi_led": "on"
    },
    "msg": "success"
}
```
### 修改设备属性值
输入内容：
```
{
    "cmd": "set",
    "attribute": "要修改的属性",
    "value": "要修改的值"
}
```
![](https://raw.githubusercontent.com/YinHangCode/node-red-contrib-mi-miio/master/images/set.png)   
输出内容：
```
{
    "cmd": "set_response",
    "result": "success",
    "msg": ["ok"]
}
```
### 属性值变动通知
当某个属性的值有变动时，通知如下消息：
attributes为值有变动的属性，oldValues为变动前个属性的值，newValues为变动后个属性的值。
```
{
    "cmd": "report",
    "attributes": ["power", "temperature"],
    "oldValues": {
        "power": "on",
        "power_consume_rate": 0,
        "temperature": 49.66,
        "wifi_led": "on"
    },
    "newValues": {
        "power": "off",
        "power_consume_rate": 0,
        "temperature": 49.59,
        "wifi_led": "on"
    }
}
```
![](https://raw.githubusercontent.com/YinHangCode/node-red-contrib-mi-miio/master/images/report.png)

## 与Dashboard配合的例子
![](https://raw.githubusercontent.com/YinHangCode/node-red-contrib-mi-miio/master/images/example1_1.png)
![](https://raw.githubusercontent.com/YinHangCode/node-red-contrib-mi-miio/master/images/example1_2.png)

## 智米直流变频落地扇与Dashboard配合的例子
![](https://raw.githubusercontent.com/YinHangCode/node-red-contrib-mi-miio/master/images/example2_1.png)
![](https://raw.githubusercontent.com/YinHangCode/node-red-contrib-mi-miio/master/images/example2_2.png)
```
[{"id":"2deacc39.7c3574","type":"Mi-ZhiMiDCVariableFrequencyFan","z":"ec7c6746.2fe518","name":"智米直流变频落地扇","device":"631357c0.f44fe8","x":210,"y":2250,"wires":[["19f3bf5b.56f711"]]},{"id":"dd1ec1aa.697ee","type":"ui_switch","z":"ec7c6746.2fe518","name":"","label":"电源","group":"6eac6248.a6ddac","order":1,"width":"2","height":"1","passthru":true,"decouple":"false","topic":"","style":"","onvalue":"on","onvalueType":"str","onicon":"fa-power-off","oncolor":"yellow","offvalue":"off","offvalueType":"str","officon":"fa-power-off","offcolor":"black","x":920,"y":1640,"wires":[["6d93649b.fe723c"]]},{"id":"cae11a76.1c69d8","type":"function","z":"ec7c6746.2fe518","name":"report-power","func":"var attribute = \"power\";\n\nreturn {\n    'payload': msg.payload.newValues[attribute]\n};","outputs":1,"noerr":0,"x":630,"y":1640,"wires":[["dd1ec1aa.697ee"]]},{"id":"6d93649b.fe723c","type":"function","z":"ec7c6746.2fe518","name":"set-power","func":"if(msg.socketid) {\n    return {\n        \"payload\": {\n            \"cmd\": \"set\",\n            \"attribute\": \"power\",\n            \"value\": msg.payload\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1060,"y":1640,"wires":[["d7672c0e.d7477"]]},{"id":"56f8d8b2.0a5f08","type":"ui_text","z":"ec7c6746.2fe518","group":"6eac6248.a6ddac","order":9,"width":"3","height":"1","name":"温度","label":"温度","format":"{{msg.payload}} °C","layout":"row-spread","x":920,"y":2040,"wires":[]},{"id":"fecf41b2.bedbb","type":"ui_text","z":"ec7c6746.2fe518","group":"6eac6248.a6ddac","order":10,"width":"3","height":"1","name":"湿度","label":"湿度","format":"{{msg.payload}} %","layout":"row-spread","x":920,"y":2090,"wires":[]},{"id":"1a361fc0.bd788","type":"function","z":"ec7c6746.2fe518","name":"report-temp_dec","func":"var attribute = \"temp_dec\";\n\nreturn {\n    'payload': msg.payload.newValues[attribute] / 10\n};","outputs":1,"noerr":0,"x":640,"y":2040,"wires":[["56f8d8b2.0a5f08"]]},{"id":"c3bbb01b.f1f73","type":"function","z":"ec7c6746.2fe518","name":"report-humidity","func":"var attribute = \"humidity\";\n\nreturn {\n    'payload': msg.payload.newValues[attribute]\n};","outputs":1,"noerr":0,"x":640,"y":2090,"wires":[["fecf41b2.bedbb"]]},{"id":"c6c3b278.c414f","type":"ui_switch","z":"ec7c6746.2fe518","name":"","label":"蜂鸣器","group":"6eac6248.a6ddac","order":11,"width":"3","height":"1","passthru":true,"decouple":"false","topic":"","style":"","onvalue":"on","onvalueType":"str","onicon":"fa-bell-o","oncolor":"yellow","offvalue":"off","offvalueType":"str","officon":"fa-bell-slash-o","offcolor":"black","x":920,"y":2140,"wires":[["3c5769ca.d81096"]]},{"id":"1ad1f0f2.1a16cf","type":"function","z":"ec7c6746.2fe518","name":"report-buzzer","func":"var attribute = \"buzzer\";\n\nreturn {\n    'payload': msg.payload.newValues[attribute]\n};","outputs":1,"noerr":0,"x":640,"y":2140,"wires":[["c6c3b278.c414f"]]},{"id":"3c5769ca.d81096","type":"function","z":"ec7c6746.2fe518","name":"set-buzzer","func":"if(msg.socketid) {\n    return {\n        \"payload\": {\n            \"cmd\": \"set\",\n            \"attribute\": \"buzzer\",\n            \"value\": msg.payload\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1080,"y":2140,"wires":[["d7672c0e.d7477"]]},{"id":"58e764c4.45bb0c","type":"ui_switch","z":"ec7c6746.2fe518","name":"","label":"童锁","group":"6eac6248.a6ddac","order":12,"width":"3","height":"1","passthru":true,"decouple":"false","topic":"","style":"","onvalue":"on","onvalueType":"str","onicon":"fa-lock","oncolor":"yellow","offvalue":"off","offvalueType":"str","officon":"fa-unlock","offcolor":"black","x":920,"y":2190,"wires":[["276ccda0.fa95e2"]]},{"id":"b35afa04.d529c8","type":"function","z":"ec7c6746.2fe518","name":"report-child_lock","func":"var attribute = \"child_lock\";\n\nreturn {\n    'payload': msg.payload.newValues[attribute]\n};","outputs":1,"noerr":0,"x":640,"y":2190,"wires":[["58e764c4.45bb0c"]]},{"id":"276ccda0.fa95e2","type":"function","z":"ec7c6746.2fe518","name":"set-child_lock","func":"if(msg.socketid) {\n    return {\n        \"payload\": {\n            \"cmd\": \"set\",\n            \"attribute\": \"child_lock\",\n            \"value\": msg.payload\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1090,"y":2190,"wires":[["d7672c0e.d7477"]]},{"id":"d7001267.94c03","type":"function","z":"ec7c6746.2fe518","name":"report-natural_level&speed_level-1","func":"var attribute1 = \"natural_level\";\nvar attribute2 = \"speed_level\";\n\nflow.set(attribute1, msg.payload.newValues[attribute1]);\nflow.set(attribute2, msg.payload.newValues[attribute2]);\n\nvar result = \"\";\nif(msg.payload.newValues[attribute1] > 0) {\n    result = msg.payload.newValues[attribute1];\n} else {\n    result = msg.payload.newValues[attribute2];\n}\n\nreturn {\n    'payload': result,\n    'enabled': msg.payload.newValues[\"power\"] == \"on\" ? true : false\n};\n","outputs":1,"noerr":0,"x":700,"y":1740,"wires":[["fc0716d3.039d48"]]},{"id":"fc0716d3.039d48","type":"ui_slider","z":"ec7c6746.2fe518","name":"","label":"风速","group":"6eac6248.a6ddac","order":3,"width":"6","height":"1","passthru":true,"outs":"all","topic":"","min":"1","max":"100","step":1,"x":920,"y":1740,"wires":[["9e9c2120.81937"]]},{"id":"88e384e1.dd3998","type":"ui_numeric","z":"ec7c6746.2fe518","name":"","label":"LED指示灯亮度","group":"6eac6248.a6ddac","order":13,"width":"6","height":"1","passthru":true,"topic":"","format":"{{value}}","min":0,"max":"2","step":1,"x":950,"y":2240,"wires":[["72e97460.66403c"]]},{"id":"e62d9cce.4403a","type":"function","z":"ec7c6746.2fe518","name":"report-led_b","func":"var attribute = \"led_b\";\n\nvar value = 2;\nswitch(msg.payload.newValues[attribute]) {\n    case 0:\n        value = 2;\n        break;\n    case 1:\n        value = 1;\n        break;\n    case 2:\n        value = 0;\n        break;\n    default:\n        break;\n}\nreturn {\n    'payload': value\n};","outputs":1,"noerr":0,"x":630,"y":2240,"wires":[["88e384e1.dd3998"]]},{"id":"72e97460.66403c","type":"function","z":"ec7c6746.2fe518","name":"set-led_b","func":"if(msg.socketid) {\n    var value = 2;\n    switch(msg.payload) {\n        case 0:\n            value = 2;\n            break;\n        case 1:\n            value = 1;\n            break;\n        case 2:\n            value = 0;\n            break;\n        default:\n            break;\n    }\n    \n    return {\n        \"payload\": {\n            \"cmd\": \"set\",\n            \"attribute\": \"led_b\",\n            \"value\": value\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1120,"y":2240,"wires":[["d7672c0e.d7477"]]},{"id":"d5eec84b.77d558","type":"ui_text","z":"ec7c6746.2fe518","group":"6eac6248.a6ddac","order":2,"width":"4","height":"1","name":"电量","label":"","format":"{{msg.payload}}","layout":"row-right","x":920,"y":1690,"wires":[]},{"id":"72aaeb4f.0fdb94","type":"function","z":"ec7c6746.2fe518","name":"report-ac_power&battery","func":"var attribute1 = \"ac_power\";\nvar attribute2 = \"battery\";\n\nvar result = \"\";\nif(msg.payload.newValues[attribute1] == \"on\" && msg.payload.newValues[attribute2] == \"100\") {\n    result = \"电源供电\";\n} else if(msg.payload.newValues[attribute1] == \"off\") {\n    result = \"电池供电(电量\" + msg.payload.newValues[attribute2] + \"%)\";\n} else if(msg.payload.newValues[attribute1] == \"on\" && msg.payload.newValues[attribute2] != \"100\") {\n    result = \"充电中(电量\" + msg.payload.newValues[attribute2] + \"%)\";\n}\n\nreturn {\n    'payload': result\n};","outputs":1,"noerr":0,"x":670,"y":1690,"wires":[["d5eec84b.77d558"]]},{"id":"f990fb4d.2623e8","type":"function","z":"ec7c6746.2fe518","name":"report-angle_enable","func":"var attribute = \"angle_enable\";\n\nreturn {\n    'payload': msg.payload.newValues[attribute],\n    'enabled': msg.payload.newValues[\"power\"] == \"on\" ? true : false\n};","outputs":1,"noerr":0,"x":660,"y":1840,"wires":[["c38c2da9.a09d2"]]},{"id":"c38c2da9.a09d2","type":"ui_switch","z":"ec7c6746.2fe518","name":"","label":"摇头","group":"6eac6248.a6ddac","order":5,"width":"3","height":"1","passthru":true,"decouple":"false","topic":"","style":"","onvalue":"on","onvalueType":"str","onicon":"","oncolor":"","offvalue":"off","offvalueType":"str","officon":"","offcolor":"","x":920,"y":1840,"wires":[["6dfe6fb9.a46b4"]]},{"id":"6dfe6fb9.a46b4","type":"function","z":"ec7c6746.2fe518","name":"set-angle_enable","func":"if(msg.socketid) {\n    return {\n        \"payload\": {\n            \"cmd\": \"set\",\n            \"attribute\": \"angle_enable\",\n            \"value\": msg.payload\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1090,"y":1840,"wires":[["d7672c0e.d7477"]]},{"id":"d8a8a203.9d00b","type":"ui_button","z":"ec7c6746.2fe518","name":"左转","group":"6eac6248.a6ddac","order":6,"width":"3","height":"1","passthru":false,"label":"左转","color":"","bgcolor":"","icon":"fa-arrow-left","payload":"left","payloadType":"str","topic":"","x":920,"y":1890,"wires":[["aca4b565.99b0b8"]]},{"id":"ddd4be71.e4a81","type":"ui_button","z":"ec7c6746.2fe518","name":"","group":"6eac6248.a6ddac","order":7,"width":"3","height":"1","passthru":false,"label":"右转","color":"","bgcolor":"","icon":"fa-arrow-right","payload":"right","payloadType":"str","topic":"","x":920,"y":1930,"wires":[["aca4b565.99b0b8"]]},{"id":"51a1f10a.99562","type":"function","z":"ec7c6746.2fe518","name":"set-angle","func":"if(msg.socketid) {\n    return {\n        \"payload\": {\n            \"cmd\": \"set\",\n            \"attribute\": \"angle\",\n            \"value\": msg.payload\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1090,"y":2010,"wires":[["d7672c0e.d7477"]]},{"id":"2a617ba6.784ec4","type":"ui_slider","z":"ec7c6746.2fe518","name":"","label":"摇头角度","group":"6eac6248.a6ddac","order":8,"width":"5","height":"1","passthru":true,"outs":"all","topic":"","min":"30","max":"118","step":1,"x":930,"y":1990,"wires":[["51a1f10a.99562","9021790c.dceb98"]]},{"id":"2a417260.eac1ae","type":"function","z":"ec7c6746.2fe518","name":"report-angle","func":"var power = msg.payload.newValues[\"power\"];\nvar angle = msg.payload.newValues[\"angle_enable\"];\nvar attribute = \"angle\";\n\nvar enable = false;\nif(power == \"on\" && angle == \"on\") {\n    enable = true;\n}\n\nreturn {\n    \"enabled\": enable,\n    'payload': msg.payload.newValues[attribute]\n};","outputs":1,"noerr":0,"x":630,"y":1940,"wires":[["2a617ba6.784ec4","9021790c.dceb98"]]},{"id":"9021790c.dceb98","type":"ui_text","z":"ec7c6746.2fe518","group":"6eac6248.a6ddac","order":14,"width":"1","height":"1","name":"摇头角度显示","label":"","format":"{{msg.payload}}","layout":"row-center","x":1110,"y":1970,"wires":[]},{"id":"db4894fe.f75968","type":"ui_switch","z":"ec7c6746.2fe518","name":"自然风","label":"{{msg.label}}","group":"6eac6248.a6ddac","order":4,"width":"3","height":"1","passthru":true,"decouple":"false","topic":"","style":"","onvalue":"true","onvalueType":"bool","onicon":"fa-leaf","oncolor":"green","offvalue":"false","offvalueType":"bool","officon":"fa-bars","offcolor":"blue","x":920,"y":1790,"wires":[["839781ab.eb099"]]},{"id":"9d87aaa7.d71cd8","type":"function","z":"ec7c6746.2fe518","name":"report-natural_level&speed_level-2","func":"var attribute = \"natural_level\";\n\nif(msg.payload.newValues[attribute] > 0) {\n    return {\n        'payload': true,\n        'label': \"自然风\",\n        'enabled': msg.payload.newValues[\"power\"] == \"on\" ? true : false\n    };\n} else {\n    return {\n        'payload': false,\n        'label': \"直吹风\",\n        'enabled': msg.payload.newValues[\"power\"] == \"on\" ? true : false\n    };\n}","outputs":1,"noerr":0,"x":700,"y":1790,"wires":[["db4894fe.f75968"]]},{"id":"19f3bf5b.56f711","type":"switch","z":"ec7c6746.2fe518","name":"switch cmd","property":"payload.cmd","propertyType":"msg","rules":[{"t":"eq","v":"report","vt":"str"},{"t":"eq","v":"list_response","vt":"str"},{"t":"eq","v":"get_response","vt":"str"},{"t":"eq","v":"set_response","vt":"str"},{"t":"else"}],"checkall":"false","repair":false,"outputs":5,"x":400,"y":1920,"wires":[["cae11a76.1c69d8","72aaeb4f.0fdb94","f990fb4d.2623e8","2a417260.eac1ae","1a361fc0.bd788","c3bbb01b.f1f73","1ad1f0f2.1a16cf","b35afa04.d529c8","e62d9cce.4403a","b5c23bb6.a6ee58","9d87aaa7.d71cd8","d7001267.94c03"],[],[],[],[]]},{"id":"38c95806.988b68","type":"link in","z":"ec7c6746.2fe518","name":"智米直流变频落地扇set","links":["d7672c0e.d7477"],"x":65,"y":2250,"wires":[["2deacc39.7c3574"]]},{"id":"d7672c0e.d7477","type":"link out","z":"ec7c6746.2fe518","name":"智米直流变频落地扇set","links":["38c95806.988b68"],"x":1285,"y":1890,"wires":[]},{"id":"b5c23bb6.a6ee58","type":"function","z":"ec7c6746.2fe518","name":"report-angle","func":"var power = msg.payload.newValues[\"power\"];\nvar angle = msg.payload.newValues[\"angle_enable\"];\n\nvar enable = false;\nif(power == \"on\" && angle == \"off\") {\n    enable = true;\n}\n\nreturn {\n    \"enabled\": enable\n};","outputs":1,"noerr":0,"x":630,"y":1890,"wires":[["d8a8a203.9d00b","ddd4be71.e4a81"]]},{"id":"9e9c2120.81937","type":"function","z":"ec7c6746.2fe518","name":"set-natural_level/speed_level-1","func":"if(msg.socketid) {\n    var natural_level = flow.get(\"natural_level\");\n    var speed_level = flow.get(\"speed_level\");\n    \n    if(natural_level > 0) {\n        return {\n            \"payload\": {\n                \"cmd\": \"set\",\n                \"attribute\": \"natural_level\",\n                \"value\": msg.payload\n            }\n        }\n    } else {\n        return {\n            \"payload\": {\n                \"cmd\": \"set\",\n                \"attribute\": \"speed_level\",\n                \"value\": msg.payload\n            }\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1130,"y":1740,"wires":[["d7672c0e.d7477"]]},{"id":"839781ab.eb099","type":"function","z":"ec7c6746.2fe518","name":"set-natural_level/speed_level-2","func":"if(msg.socketid) {\n    var natural_level = flow.get(\"natural_level\");\n    var speed_level = flow.get(\"speed_level\");\n    \n    if(natural_level > 0) {\n        if(msg.payload) {\n            return;\n        } else {\n            return {\n                \"payload\": {\n                    \"cmd\": \"set\",\n                    \"attribute\": \"speed_level\",\n                    \"value\": natural_level\n                }\n            }\n        }\n    } else {\n        if(msg.payload) {\n            return {\n                \"payload\": {\n                    \"cmd\": \"set\",\n                    \"attribute\": \"natural_level\",\n                    \"value\": speed_level\n                }\n            };\n        } else {\n            return;\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1130,"y":1790,"wires":[["d7672c0e.d7477"]]},{"id":"aca4b565.99b0b8","type":"function","z":"ec7c6746.2fe518","name":"set-move","func":"if(msg.socketid) {\n    return {\n        \"payload\": {\n            \"cmd\": \"miio\",\n            \"method\": \"set_move\",\n            \"value\": [msg.payload]\n        }\n    }\n}\n\nreturn;","outputs":1,"noerr":0,"x":1060,"y":1910,"wires":[["d7672c0e.d7477"]]},{"id":"631357c0.f44fe8","type":"Mi-ZhiMiDCVariableFrequencyFan-Device","z":"","deviceName":"智米直流变频落地扇","deviceIP":"10.3.6.1","deviceToken":"672fa1b99c6e71234fd23d7350149cdf","deviceSyncInterval":"3000"},{"id":"6eac6248.a6ddac","type":"ui_group","z":"","name":"智米直流变频落地扇","tab":"e32679b8.928d88","order":3,"disp":true,"width":"6","collapse":true},{"id":"e32679b8.928d88","type":"ui_tab","z":"","name":"主卧","icon":"dashboard","order":1}]
```
   
## 版本说明
### 0.0.2 (2018-11-15)
1. 增加支持智米直流变频落地扇。   
2. 因为某些情况下需要多个属性同时计算，不得不修改report的格式规则。   
### 0.0.1 (2018-11-13)
1. 增加支持米家智能插线板。   
2. 增加支持Yeelight彩光灯带。   