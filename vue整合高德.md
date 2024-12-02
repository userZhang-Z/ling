# A准备工作

## 获取key

前往高德api控制台获取securityJsCode和key

## 创建项目

**Vite**仅可用于Vue3;
**Vue-cli**: v4.5以下的版本对应的是*Vue2*; v4.5及其以上的版本对应的是*Vue3*,当然在创建项目时也可选择Vue2

获取vite版本号，只有下面列出的版本才能指定创建vite版本

```
npm view create-vite versions
```



```
npm create vite@3.0.2
```

安装依赖

```
"element-plus": "^2.2.15",
"less": "^4.1.3",
```



```
"@amap/amap-jsapi-loader": "^1.0.1",
```

使用绝对路径报错

```
npm install --save-dev @types/node
```

修改vite.config.ts

```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  server: {
    host: '0.0.0.0'
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src')
    }
  }
})
```

# 初始化地图

```vue
<template>
  <div class="map-wrapper">
    <div id="amap"></div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";

window._AMapSecurityConfig = {
  securityJsCode: "a72dc774a9944d604edefa19ebf60db5",
};
let map: any = null
onMounted(() => {
  initMap();
});

async function initMap() {
  await AMapLoader.load({
    key: 'af470b63cde6d5c76a4652811aab6445',
    version: '2.0',
    plugins:
        [
        ],
  }).then(AMap => {
        map = new AMap.Map('amap', {
          zoom: 12,
          resizeEnable: true,
          center: [103.841856, 36.053549],
          viewMode: "3D",
        })
      }
  );
}

onUnmounted(() => {
  map?.destroy();
});
</script>


<style scoped lang="less">
.map-wrapper {
  #amap {
    width: 1920px;
    height: 1080px;
  }
}
</style>

```



## 安装

```
npm install @amap/amap-jsapi-loader --save
```

## 初始化

```vue
<template>
  <div class="map-wrapper">
    <div id="amap"></div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";

window._AMapSecurityConfig = {
  securityJsCode: "你的securityJsCode",
};
let map: any = null
let placeSearch,
    AMapObj: any,
    geocoder: { getAddress: (arg0: any, arg1: (status: any, result: any) => void) => void; }
let mouseTool: any = null
let circleId = 1
let auto = ref('')
let autoOptions = {
  input: 'searchInputId',
}
let searchText = ref('')
onMounted(() => {
  initMap();
});

async function initMap() {
  await AMapLoader.load({
    key: '你的key',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
      AMapObj = AMap
      map = new AMap.Map('amap', {
        zoom: 12,
        resizeEnable: true,
        /**
         * 创建AMap.Map对象时如果没有传入center参数
         * 地图也会自动定位到您所在城市并显示，这就是JS API的初始加载定位：无需传入对应参数就能获取大致的定位信息
         * 但是当写了center参数，那么地图也会显示到您传入的center参数对应的位置
         */
        center: [103.841856, 36.053549],
        // center: [116.397428, 39.90923],
        viewMode: "3D",
      })
      // 图层切换
      const mapType = new AMap.MapType()
      // 鹰眼
      const hawkEye = new AMap.HawkEye()
      // 缩放条
      const toolbar = new AMap.ToolBar({
        position: 'LT'
      })
      // 比例尺
      const scale = new AMap.Scale()
      // 定位
      const geolocation = new AMap.Geolocation({
        enableHighAccuracy: true,//是否使用高精度定位，默认:true
        timeout: 10000,          //超过10秒后停止定位，默认：无穷大
        maximumAge: 0,           //定位结果缓存0毫秒，默认：0
        convert: true,           //自动偏移坐标，偏移后的坐标为高德坐标，默认：true
        showButton: true,        //显示定位按钮，默认：true
        buttonPosition: 'LB',    //定位按钮停靠位置，默认：'LB'，左下角
        buttonOffset: new AMap.Pixel(10, 20),//定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
        showMarker: true,        //定位成功后在定位到的位置显示点标记，默认：true
        showCircle: true,        //定位成功后用圆圈表示定位精度范围，默认：true
        panToLocation: true,     //定位成功后将定位到的位置作为地图中心点，默认：true
        zoomToAccuracy:true      //定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
      });
      geocoder = new AMap.Geocoder({
        city: '全国',
      });
      map.addControl(geolocation)  // 定位组件
      map.addControl(toolbar)  // 放大缩小组件
      map.addControl(scale)  // 比例尺
      map.addControl(hawkEye)  // 鹰眼
      map.addControl(mapType)  // 图层切换
    }
  );
}

onUnmounted(() => {
  map?.destroy();
});
</script>


<style scoped lang="less">
.map-wrapper {
  #amap {
    width: 100%;
    height: 100vh;
  }
}
</style>
```

![](./assets/地图初始化)

# 图层

## 使用

```js
async function initMap() {
  await AMapLoader.load({
    key: '你的key',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
      AMapObj = AMap
      map = new AMap.Map('amap', {
        zoom: 12,
        resizeEnable: true,
        /**
         * 创建AMap.Map对象时如果没有传入center参数
         * 地图也会自动定位到您所在城市并显示，这就是JS API的初始加载定位：无需传入对应参数就能获取大致的定位信息
         * 但是当写了center参数，那么地图也会显示到您传入的center参数对应的位置
         */
        center: [103.841856, 36.053549],
        // center: [116.397428, 39.90923],
        viewMode: "3D",
      })
      // 图层切换
      const mapType = new AMap.MapType()
      // 鹰眼
      const hawkEye = new AMap.HawkEye({
        position: 'RB'
      })
      // 缩放条
      const toolbar = new AMap.ToolBar({
        position: 'LT'
      })
      // 比例尺
      const scale = new AMap.Scale()
      // 定位
      const geolocation = new AMap.Geolocation({
        enableHighAccuracy: true,//是否使用高精度定位，默认:true
        timeout: 10000,          //超过10秒后停止定位，默认：无穷大
        maximumAge: 0,           //定位结果缓存0毫秒，默认：0
        convert: true,           //自动偏移坐标，偏移后的坐标为高德坐标，默认：true
        showButton: true,        //显示定位按钮，默认：true
        buttonPosition: 'LT',    //定位按钮停靠位置，默认：'LB'，左下角
        buttonOffset: new AMap.Pixel(10, 20),//定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
        showMarker: true,        //定位成功后在定位到的位置显示点标记，默认：true
        showCircle: true,        //定位成功后用圆圈表示定位精度范围，默认：true
        panToLocation: true,     //定位成功后将定位到的位置作为地图中心点，默认：true
        zoomToAccuracy:true      //定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
      });
      geocoder = new AMap.Geocoder({
        city: '全国',
      });
      map.addControl(geolocation)  // 定位组件
      map.addControl(toolbar)  // 放大缩小组件
      map.addControl(scale)  // 比例尺
      map.addControl(hawkEye)  // 鹰眼
      map.addControl(mapType)  // 图层切换



     /**
      * 添加图层： 官方卫星、路网图层
      * 上面如果添加了组件：map.addControl(mapType)
      * 这里就没必要手动添加图层了
      */
      const layer1 = new AMap.TileLayer.Satellite();
      const layer2 =  new AMap.TileLayer.RoadNet();
      const layers = [
        layer1,
        layer2
      ]
      // 添加到地图上
      map.add(layers);
    }
  );
}
```

## 函数抽离

我们将图层代码抽离为addLayers函数

```js
const addLayers = () => {
  const layer1 = new AMapObj.TileLayer.Satellite();
  const layer2 =  new AMapObj.TileLayer.RoadNet();
  const layers = [
    layer1,
    layer2
  ]
  // 添加到地图上
  map.add(layers);
}
```

申明完以后，修改initMap

```js
async function initMap() {
  await AMapLoader.load({
    key: '你的key',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
      AMapObj = AMap
      map = new AMap.Map('amap', {
        zoom: 12,
        resizeEnable: true,
        center: [103.841856, 36.053549],
        viewMode: "3D",
      })
 	  
      ......(代码省略)
      
      map.addControl(mapType)  // 图层切换

      // 添加图层(封装为函数)，这里咱们先不用，给它注释掉，大家知道有这个api
      // addLayers()
    }
  );
}
```

# 搜索

## 输入提示

添加搜索组件，这里用了element-ui的输入框

```
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'

createApp(App).use(ElementPlus).mount('#app')
```



```html
<template>
  <div class="map-wrapper">
    <div style="position: absolute;top: 0;height: 60px;left: 100px;width: 400px;z-index: 999">
      <el-input v-model="searchText" placeholder="请输入搜索关键字" id="searchInputId"/>
    </div>
    <div id="amap"></div>
  </div>
</template>
```

注意autoOptions的声明

```js
let autoOptions = {
  input: 'searchInputId', // 这个字符串要和搜索框的id保持一致
}
```

只需要一句代码实现输入提示

```js
async function initMap() {
  await AMapLoader.load({
    key: 'af470b63cde6d5c76a4652811aab6445',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
      AMapObj = AMap
      map = new AMap.Map('amap', {
        zoom: 12,
        resizeEnable: true,
        center: [103.841856, 36.053549],
        viewMode: "3D",
      })

      ......(代码省略)
      
      // 添加图层
      // addLayers()
	  
      // 实现输入提示
      auto.value = new AMap.AutoComplete(autoOptions)
    }
  );
}
```

效果图

![image-20240701112919446](./assets/输入提示)

这样只是有输入提示功能，但是当你点击“天津站”的时候，地图是无法定位过去的

我们继续完善这个功能

## POI搜索

```js
  auto.value = new AMap.AutoComplete(autoOptions)
  const placeSearch = new AMap.PlaceSearch({
    map: map,
    city: '', // 减小搜索范围至某个城市
    pageSize: 5, // 单页显示结果条数
    pageIndex: 1, // 页码
    citylimit: false, // 是否强制限制在设置的城市内搜索
    autoFitView: true,
    panel: "panel", // 结果列表将在此容器中进行展示。出不来时设置样式z-inde: 999
  })
  auto.value.on("select", function (e) {
    //针对选中的poi实现自己的功能
    placeSearch.search(e.poi.name);
  });
```

效果图

![image-20240701112948927](./assets/POI搜索)

## 抽离

声明函数poiSearch

```js
const poiSearch = () => {
  auto.value = new AMapObj.AutoComplete(autoOptions)
  const placeSearch = new AMapObj.PlaceSearch({
    map: map,
    city: '', // 减小搜索范围至某个城市
    pageSize: 5, // 单页显示结果条数
    pageIndex: 1, // 页码
    citylimit: false, // 是否强制限制在设置的城市内搜索
    autoFitView: true,
    panel: "panel", // 结果列表将在此容器中进行展示。出不来时设置样式z-inde: 999
  })
  auto.value.on("select", function (e) {
    //针对选中的poi实现自己的功能
    placeSearch.search(e.poi.name);
  });
}
```

## 搜索结果展示

创建id为panel的div

```html
<template>
  <div class="map-wrapper">
    <div id="panel"></div>
    <div style="position: absolute;top: 0;height: 60px;left: 100px;width: 400px;z-index: 999">
      <el-input v-model="searchText" placeholder="请输入搜索关键字" id="searchInputId"/>
    </div>
    <div id="amap"></div>
  </div>
</template>
```

```css
#panel {
  z-index: 999;
  position: absolute;
  top: 0;
  right: 0;
}
```

![image-20240701140922565](./assets/POI搜索效果图)

## 完整代码

```vue
<template>
  <div class="map-wrapper">
    <div id="panel"></div>
    <div style="position: absolute;top: 0;height: 60px;left: 100px;width: 400px;z-index: 999">
      <el-input v-model="searchText" placeholder="请输入搜索关键字" id="searchInputId"/>
    </div>
    <div id="amap"></div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";

window._AMapSecurityConfig = {
  securityJsCode: "你的securityJsCode",
};
let map: any = null
let placeSearch,
    AMapObj: any,
    geocoder: { getAddress: (arg0: any, arg1: (status: any, result: any) => void) => void; }
let mouseTool: any = null
let circleId = 1
let auto = ref('')
let autoOptions = {
  input: 'searchInputId', // 这个字符串要和搜索框的id保持一致
}
let searchText = ref('')
onMounted(() => {
  initMap();
});

async function initMap() {
  await AMapLoader.load({
    key: '你的key',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
      AMapObj = AMap
      map = new AMap.Map('amap', {
        zoom: 12,
        resizeEnable: true,
        /**
         * 创建AMap.Map对象时如果没有传入center参数
         * 地图也会自动定位到您所在城市并显示，这就是JS API的初始加载定位：无需传入对应参数就能获取大致的定位信息
         * 但是当写了center参数，那么地图也会显示到您传入的center参数对应的位置
         */
        center: [103.841856, 36.053549],
        // center: [116.397428, 39.90923],
        viewMode: "3D",
      })
      // 图层切换
      const mapType = new AMap.MapType()
      // 鹰眼
      const hawkEye = new AMap.HawkEye({
        position: 'RB'
      })
      // 缩放条
      const toolbar = new AMap.ToolBar({
        position: 'LT'
      })
      // 比例尺
      const scale = new AMap.Scale()
      // 定位
      const geolocation = new AMap.Geolocation({
        enableHighAccuracy: true,//是否使用高精度定位，默认:true
        timeout: 10000,          //超过10秒后停止定位，默认：无穷大
        maximumAge: 0,           //定位结果缓存0毫秒，默认：0
        convert: true,           //自动偏移坐标，偏移后的坐标为高德坐标，默认：true
        showButton: true,        //显示定位按钮，默认：true
        buttonPosition: 'LT',    //定位按钮停靠位置，默认：'LB'，左下角
        buttonOffset: new AMap.Pixel(10, 20),//定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
        showMarker: true,        //定位成功后在定位到的位置显示点标记，默认：true
        showCircle: true,        //定位成功后用圆圈表示定位精度范围，默认：true
        panToLocation: true,     //定位成功后将定位到的位置作为地图中心点，默认：true
        zoomToAccuracy:true      //定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
      });
      geocoder = new AMap.Geocoder({
        city: '全国',
      });
      map.addControl(geolocation)  // 定位组件
      map.addControl(toolbar)  // 放大缩小组件
      map.addControl(scale)  // 比例尺
      map.addControl(hawkEye)  // 鹰眼
      map.addControl(mapType)  // 图层切换

      // 添加图层
      // addLayers()

      // POI搜索
      poiSearch()
    }
  );
}

// 添加图层
const addLayers = () => {
  const layer1 = new AMapObj.TileLayer.Satellite();
  const layer2 =  new AMapObj.TileLayer.RoadNet();
  const layers = [
    layer1,
    layer2
  ]
  // 添加到地图上
  map.add(layers);
}

// poi搜索
const poiSearch = () => {
  auto.value = new AMapObj.AutoComplete(autoOptions)
  // 搜索结果页的配置项
  const placeSearch = new AMapObj.PlaceSearch({
    map: map,
    city: '', // 减小搜索范围至某个城市
    pageSize: 5, // 单页显示结果条数
    pageIndex: 1, // 页码
    citylimit: false, // 是否强制限制在设置的城市内搜索
    autoFitView: true,
    panel: "panel", // 结果列表将在此容器中进行展示。出不来时设置样式z-inde: 999
  })
  auto.value.on("select", function (e) {
    //针对选中的poi实现自己的功能
    placeSearch.search(e.poi.name);
  });
}

onUnmounted(() => {
  map?.destroy();
});
</script>


<style scoped lang="less">
.map-wrapper {
  #amap {
    width: 100%;
    height: 100vh;
  }
}
    
#panel {
  z-index: 999;
  position: absolute;
  top: 0;
  right: 0;
}
</style>
```

# 信息窗体

在讲信息窗体之前，先来看下地图事件的监听，包含鼠标单击、鼠标双击、鼠标移动

## 地图事件

声明三个函数，分别处理事件监听的逻辑

```js
// 鼠标双击事件
const doubleClick = (e) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置双击了地图！')
}

// 鼠标移动事件
const showInfoMove = () => {
  console.log('您移动了您的鼠标！')
}

// 鼠标点击事件
const signClick = (e) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置单击了地图！')
}
```

initMap中使用

```js
// 为地图绑定点击事件
bindEvent()
```

打开浏览器F12调试事件是否绑定成功，成功的话进行下一步

## 信窗体弹出

### 封装信息窗体函数

```js
const showInfo = (e: any, x: any, y: any, ) => {
  geocoder.getAddress(e.lnglat,(status, result) => {
    if (status === 'complete' && result.regeocode) {
      const address = result.regeocode.formattedAddress
      // 如果信息窗体有图片的话，需要注意下写法
      const infoContent = `
        <div style="padding:7px 0px 0px 0px;">
          <div class="global-header">当前位置</div>
          <p class='input-item'>经度: ${x}</p>
          <p class='input-item'>纬度: ${y}</p>
          <p class='input-item'>地址: ${address}</p>
          <img src="${logo}" alt="Vue Logo" width="100px;height=100px"/>
        </div>
      `
      const infoWindow = new AMapObj.InfoWindow({
        content: infoContent
      })
      infoWindow.open(map, new AMapObj.LngLat(x, y))
    } else {
      console.error('获取地址失败')
    }
  })
}
```

### 图片处理

按照下面这种写法是肯定没问题的

```js
import logo from "../assets/vue.svg";
<img src="${logo}" alt="Vue Logo" width="100px;height=100px"/>
```

### 全局样式

通过这种方式声明的HTML结构，没法在`<style></style>`标签中修改样式

- 可以使用内联样式去修改
- 使用全局样式

这里我们介绍下使用全局样式：

首先在，assets目录下新建styles文件夹，新建`global.css`文件

```css
/* src/assets/styles/global.css */
* .amap-info-content{
    padding: 0;
    margin: 0;
}

.input-item {
    font-size: 14px;
    color: #333;
    margin: 5px 0;
    line-height: 1.5;
}

#info-window-img {
    width: 100px;
    height: auto;
    margin-top: 10px;
}

.global-header {
    height: 40px;
    width: 100%;
    background-color: #1890ff;
    display: flex;
    align-items: center;
}

.amap-info-close {
    margin-top: 12px;
    font-size: 20px;
}
```

然后在`main.ts`中引入

```js
import './style.css'
```

这样就可以修改信息窗体的样式了

### 使用

假设我们现在讲信息窗体弹出的触发事件定位**双击**

当然你可以把信息窗体的触发放到其他的地图事件

```js
// 鼠标双击事件
const doubleClick = (e: any) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置双击了地图！')
  const x = e.lnglat.getLng()
  const y = e.lnglat.getLat()
  showInfo(e, x, y)
}
```

## 完整代码

此时的完整代码

```vue
<template>
  <div class="map-wrapper">
  	<div id="panel"></div>
    <div style="position: absolute;top: 0;height: 60px;left: 100px;width: 400px;z-index: 999">
      <el-input v-model="searchText" placeholder="请输入搜索关键字" id="searchInputId"/>
    </div>
    <div id="amap"></div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";
import logo from "../assets/vue.svg";

window._AMapSecurityConfig = {
  securityJsCode: "你的securityJsCode",
};
let map: any = null
let placeSearch,
    AMapObj: any,
    geocoder: { getAddress: (arg0: any, arg1: (status: any, result: any) => void) => void; }
let mouseTool: any = null
let circleId = 1
let auto = ref('')
let autoOptions = {
  input: 'searchInputId', // 这个字符串要和搜索框的id保持一致
}
let searchText = ref('')
onMounted(() => {
  initMap();
});

async function initMap() {
  await AMapLoader.load({
    key: '你的key',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
      AMapObj = AMap
      map = new AMap.Map('amap', {
        zoom: 12,
        resizeEnable: true,
        /**
         * 创建AMap.Map对象时如果没有传入center参数
         * 地图也会自动定位到您所在城市并显示，这就是JS API的初始加载定位：无需传入对应参数就能获取大致的定位信息
         * 但是当写了center参数，那么地图也会显示到您传入的center参数对应的位置
         */
        center: [103.841856, 36.053549],
        // center: [116.397428, 39.90923],
        viewMode: "3D",
      })
      // 图层切换
      const mapType = new AMap.MapType()
      // 鹰眼
      const hawkEye = new AMap.HawkEye({
        position: 'RB'
      })
      // 缩放条
      const toolbar = new AMap.ToolBar({
        position: 'LT'
      })
      // 比例尺
      const scale = new AMap.Scale()
      // 定位
      const geolocation = new AMap.Geolocation({
        enableHighAccuracy: true,//是否使用高精度定位，默认:true
        timeout: 10000,          //超过10秒后停止定位，默认：无穷大
        maximumAge: 0,           //定位结果缓存0毫秒，默认：0
        convert: true,           //自动偏移坐标，偏移后的坐标为高德坐标，默认：true
        showButton: true,        //显示定位按钮，默认：true
        buttonPosition: 'LT',    //定位按钮停靠位置，默认：'LB'，左下角
        buttonOffset: new AMap.Pixel(10, 20),//定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
        showMarker: true,        //定位成功后在定位到的位置显示点标记，默认：true
        showCircle: true,        //定位成功后用圆圈表示定位精度范围，默认：true
        panToLocation: true,     //定位成功后将定位到的位置作为地图中心点，默认：true
        zoomToAccuracy:true      //定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
      });
      geocoder = new AMap.Geocoder({
        city: '全国',
      });
      map.addControl(geolocation)  // 定位组件
      map.addControl(toolbar)  // 放大缩小组件
      map.addControl(scale)  // 比例尺
      map.addControl(hawkEye)  // 鹰眼
      map.addControl(mapType)  // 图层切换

      // 添加图层
      // addLayers()

      // POI搜索
      poiSearch()

      // 为地图绑定点击事件
      bindEvent()
    }
  );
}

// 添加图层
const addLayers = () => {
  const layer1 = new AMapObj.TileLayer.Satellite();
  const layer2 =  new AMapObj.TileLayer.RoadNet();
  const layers = [
    layer1,
    layer2
  ]
  // 添加到地图上
  map.add(layers);
}

// poi搜索
const poiSearch = () => {
  auto.value = new AMapObj.AutoComplete(autoOptions)
  const placeSearch = new AMapObj.PlaceSearch({
    map: map,
    city: '', // 减小搜索范围至某个城市
    pageSize: 5, // 单页显示结果条数
    pageIndex: 1, // 页码
    citylimit: false, // 是否强制限制在设置的城市内搜索
    autoFitView: true,
    panel: "panel", // 结果列表将在此容器中进行展示。出不来时设置样式z-inde: 999
  })
  auto.value.on("select", function (e) {
    //针对选中的poi实现自己的功能
    placeSearch.search(e.poi.name);
  });
}

// 鼠标双击事件
const doubleClick = (e: any) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置双击了地图！')
  const x = e.lnglat.getLng()
  const y = e.lnglat.getLat()
  showInfo(e, x, y)
}

// 鼠标移动事件
const showInfoMove = () => {
  console.log('您移动了您的鼠标！')
}

// 鼠标点击事件
const signClick = (e) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置单击了地图！')
}

// 地图事件绑定
const bindEvent = () => {
  console.log("事件绑定成功")
  map.on('click', signClick);
  map.on('dblclick', doubleClick);
  map.on('mousemove', showInfoMove);
}

const showInfo = (e: any, x: any, y: any, ) => {
  geocoder.getAddress(e.lnglat,(status, result) => {
    if (status === 'complete' && result.regeocode) {
      const address = result.regeocode.formattedAddress
      const infoContent = `
        <div style="padding:7px 0px 0px 0px;">
          <div class="global-header">当前位置</div>
          <p class='input-item'>经度: ${x}</p>
          <p class='input-item'>纬度: ${y}</p>
          <p class='input-item'>地址: ${address}</p>
          <img src="${logo}" alt="Vue Logo" width="100px;height=100px"/>
        </div>
      `
      const infoWindow = new AMapObj.InfoWindow({
        content: infoContent
      })
      infoWindow.open(map, new AMapObj.LngLat(x, y))
    } else {
      console.error('获取地址失败')
    }
  })
}

onUnmounted(() => {
  map?.destroy();
});
</script>

<style scoped lang="less">
.map-wrapper {
  #amap {
    width: 100%;
    height: 100vh;
  }
}
    
#panel {
  z-index: 999;
  position: absolute;
  top: 0;
  right: 0;
}
</style>
```

# 点标记

## 函数声明

声明创建点标记和消除点标记函数

```js
// 创建点标记
const createMarker = () => {
  //  点标记
  const markerContent =
      `<div class="custom-content-marker">
            <img src="//a.amap.com/jsapi_demos/static/demo-center/icons/dir-via-marker.png">
            <div class="close-btn">X</div>
          </div>`
  const position = new AMapObj.LngLat(103.841856, 36.053549); //Marker 经纬度
  const marker = new AMapObj.Marker({
    position: position,
    content: markerContent, //将 html 传给 content
    offset: new AMapObj.Pixel(-13, -30), //以 icon 的 [center bottom] 为原点
  });
  // 添加点标记
  map.add(marker);
  // 未关闭按钮添加点击事件
  const closeBtn = document.querySelector(".close-btn")
  if (closeBtn) {
    closeBtn.addEventListener('click', () => {
      clearMarker(marker); // 移除 marker
    });
  }
}

// 移除点标记
const clearMarker = (marker: any) => {
  console.log("移除点标记")
  map.remove(marker); //清除 marker
}
```

## 样式

global.css

```css
.amap-info-close {
    margin-top: 12px;
    font-size: 20px;
}

.custom-content-marker {
    position: relative;
    width: 35px;
    height: 45px;
}

.custom-content-marker img {
    width: 100%;
    height: 100%;
}

.custom-content-marker .close-btn {
    position: absolute;
    top: -6px;
    right: -8px;
    width: 15px;
    height: 15px;
    font-size: 12px;
    background: #ccc;
    border-radius: 50%;
    color: #fff;
    text-align: center;
    line-height: 15px;
    box-shadow: -1px 1px 1px rgba(10, 10, 10, .2);
}

.custom-content-marker .close-btn:hover{
    background: red;
}
```

## 使用

initMap函数

和之前信息窗体等使用方法一样，这里就不写了

```js
  // 点标记
  createMarker()
```

## 效果图

![image-20240701141447552](./assets/点标记效果图)

# 覆盖物

**注意**：使用vue3的小伙伴声明map的时候千万不要使用ref，不然会出现bug

## 创建鼠标工具插件实例

```js
async function initMap() {
  await AMapLoader.load({
    key: '你的key',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
      AMapObj = AMap
      map = new AMap.Map('amap', {
        zoom: 12,
        resizeEnable: true,
        center: [103.841856, 36.053549],
        viewMode: "3D",
      })

      ......(代码省略)

      // 添加图层
      // addLayers()

      // POI搜索
      poiSearch()

      // 为地图绑定点击事件
      bindEvent()

      // 点标记
      createMarker()

      // 覆盖物
      mouseTool = new AMap.MouseTool(map); //创建鼠标工具插件实例
    }
  );
}
```

## 创建函数

```js
const drawRect = () => {
  console.log('绘制矩形')
  mouseTool.rectangle({
    strokeColor: 'red',
    strokeOpacity: 0.5,
    strokeWeight: 2,
    fillColor: 'blue',
    fillOpacity: 0.2,
    strokeStyle: 'solid',
  })
}

// 绘制圆形
const drawCircle = () => {
  console.log('绘制圆形')
  mouseTool.circle({
    strokeColor: "#FF33FF",
    strokeOpacity: 1,
    strokeWeight: 6,
    strokeOpacity: 0.2,
    fillColor: '#1791fc',
    fillOpacity: 0.4,
    // strokeStyle: 'solid',
    strokeStyle: 'dashed',
    strokeDasharray: [30, 10],
  })
}

// 绘制多边形
const drawOther = () => {
  mouseTool.polygon({
    strokeColor: "#FF33FF",
    strokeOpacity: 1,
    strokeWeight: 4,
    strokeOpacity: 0.2,
    fillColor: '#1791fc',
    fillOpacity: 0.2,
    strokeStyle: "solid",
  })
}


/**
 * 停止绘制
 * 形参为true时，清除所有绘制的覆盖物
 * 形参为false时，仅仅停止绘制，不会清除覆盖物
 */
const stopDrawAndClear = () => {
  mouseTool.close(true)
}
```

## 效果图

![image-20240701134708860](./assets/覆盖物效果图)



## 完整代码

```vue
<template>
  <div class="map-wrapper">
    <!--  搜素结果  -->
    <div id="panel"></div>
    <!--  路线规划  -->
    <div id="line-panel"></div>
    <div style="position: absolute;top: 0;height: 60px;left: 60px;width: 200px;z-index: 999">
      <el-input v-model="searchText" placeholder="请输入搜索关键字" id="searchInputId"/>
    </div>
    <div style="position: absolute;top: 0;height: 60px;left: 460px;width: 400px;z-index: 999">
    </div>
    <div id="amap"></div>
    <div class="draw-area">
      <div class="select">
        <div style="margin: 10px 0">
          <el-button @click="drawOther">绘制多边形</el-button>
          <el-button @click="drawRect">绘制矩形</el-button>
          <el-button @click="drawCircle">绘制圆形</el-button>
          <el-button @click="stopDrawAndClear()">清除</el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import {onMounted, onUnmounted, reactive, ref} from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";
import logo from "../assets/vue.svg";

window._AMapSecurityConfig = {
  securityJsCode: "你的securityJsCode",
};
let map: any = null
let placeSearch,
    AMapObj: any,
    geocoder: { getAddress: (arg0: any, arg1: (status: any, result: any) => void) => void; }
let mouseTool: any = null
let circleId = 1
let auto = ref('')
let autoOptions = {
  input: 'searchInputId', // 这个字符串要和搜索框的id保持一致
}
let searchText = ref('')
// let driving: any = null
let startPoint = ref('')
let endPoint = ref('')
// const points: any = []

onMounted(async () => {
  await initMap();
});

onUnmounted(() => {
  map?.destroy();
});

async function initMap() {
  await AMapLoader.load({
    key: '你的key',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
      AMapObj = AMap
      map = new AMap.Map('amap', {
        zoom: 12,
        resizeEnable: true,
        /**
         * 创建AMap.Map对象时如果没有传入center参数
         * 地图也会自动定位到您所在城市并显示，这就是JS API的初始加载定位：无需传入对应参数就能获取大致的定位信息
         * 但是当写了center参数，那么地图也会显示到您传入的center参数对应的位置
         */
        center: [103.841856, 36.053549],
        // center: [116.397428, 39.90923],
        viewMode: "3D",
      })
      // 图层切换
      const mapType = new AMap.MapType()
      // 鹰眼
      const hawkEye = new AMap.HawkEye({
        position: 'RB'
      })
      // 缩放条
      const toolbar = new AMap.ToolBar({
        position: 'LT'
      })
      // 比例尺
      const scale = new AMap.Scale()
      // 定位
      const geolocation = new AMap.Geolocation({
        enableHighAccuracy: true,//是否使用高精度定位，默认:true
        timeout: 10000,          //超过10秒后停止定位，默认：无穷大
        maximumAge: 0,           //定位结果缓存0毫秒，默认：0
        convert: true,           //自动偏移坐标，偏移后的坐标为高德坐标，默认：true
        showButton: true,        //显示定位按钮，默认：true
        buttonPosition: 'LT',    //定位按钮停靠位置，默认：'LB'，左下角
        buttonOffset: new AMap.Pixel(10, 20),//定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
        showMarker: true,        //定位成功后在定位到的位置显示点标记，默认：true
        showCircle: true,        //定位成功后用圆圈表示定位精度范围，默认：true
        panToLocation: true,     //定位成功后将定位到的位置作为地图中心点，默认：true
        zoomToAccuracy:true      //定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
      });
      geocoder = new AMap.Geocoder({
        city: '全国',
      });
      map.addControl(geolocation)  // 定位组件
      map.addControl(toolbar)  // 放大缩小组件
      map.addControl(scale)  // 比例尺
      map.addControl(hawkEye)  // 鹰眼
      map.addControl(mapType)  // 图层切换

      // 添加图层
      // addLayers()

      // POI搜索
      poiSearch()

      // 为地图绑定点击事件
      bindEvent()

      // 点标记
      createMarker()

      // 覆盖物
      mouseTool = new AMap.MouseTool(map); //创建鼠标工具插件实例
    }
  );
}

// 添加图层
const addLayers = () => {
  const layer1 = new AMapObj.TileLayer.Satellite();
  const layer2 =  new AMapObj.TileLayer.RoadNet();
  const layers = [
    layer1,
    layer2
  ]
  // 添加到地图上
  map.add(layers);
}

// poi搜索
const poiSearch = () => {
  auto.value = new AMapObj.AutoComplete(autoOptions)
  const placeSearch = new AMapObj.PlaceSearch({
    map: map,
    city: '', // 减小搜索范围至某个城市
    pageSize: 5, // 单页显示结果条数
    pageIndex: 1, // 页码
    citylimit: false, // 是否强制限制在设置的城市内搜索
    autoFitView: true,
    panel: "panel", // 结果列表将在此容器中进行展示。出不来时设置样式z-inde: 999
  })
  auto.value.on("select", function (e) {
    //针对选中的poi实现自己的功能
    placeSearch.search(e.poi.name);
  });
}

// 鼠标双击事件
const doubleClick = (e: any) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置双击了地图！')
  const x = e.lnglat.getLng()
  const y = e.lnglat.getLat()
  showInfo(e, x, y)
}

// 鼠标移动事件
const showInfoMove = () => {
  console.log('您移动了您的鼠标！')
}

// 鼠标点击事件
const signClick = (e) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置单击了地图！')
}

// 地图事件绑定
const bindEvent = () => {
  console.log("事件绑定成功")
  map.on('click', signClick);
  map.on('dblclick', doubleClick);
  // map.on('mousemove', showInfoMove);
}

const showInfo = (e: any, x: any, y: any, ) => {
  geocoder.getAddress(e.lnglat,(status, result) => {
    if (status === 'complete' && result.regeocode) {
      const address = result.regeocode.formattedAddress
      const infoContent = `
        <div style="padding:7px 0px 0px 0px;">
          <div class="global-header">当前位置</div>
          <p class='input-item'>经度: ${x}</p>
          <p class='input-item'>纬度: ${y}</p>
          <p class='input-item'>地址: ${address}</p>
          <img src="${logo}" alt="Vue Logo" width="100px;height=100px"/>
        </div>
      `
      const infoWindow = new AMapObj.InfoWindow({
        content: infoContent
      })
      infoWindow.open(map, new AMapObj.LngLat(x, y))
    } else {
      console.error('获取地址失败')
    }
  })
}

// 创建点标记
const createMarker = () => {
  //  点标记
  const markerContent =
      `<div class="custom-content-marker">
            <img src="//a.amap.com/jsapi_demos/static/demo-center/icons/dir-via-marker.png">
            <div class="close-btn">X</div>
          </div>`
  const position = new AMapObj.LngLat(103.841856, 36.053549); //Marker 经纬度
  const marker = new AMapObj.Marker({
    position: position,
    content: markerContent, //将 html 传给 content
    offset: new AMapObj.Pixel(-13, -30), //以 icon 的 [center bottom] 为原点
  });
  // 添加点标记
  map.add(marker);
  // 未关闭按钮添加点击事件
  const closeBtn = document.querySelector(".close-btn")
  if (closeBtn) {
    closeBtn.addEventListener('click', () => {
      clearMarker(marker); // 移除 marker
    });
  }
}

// 移除点标记
const clearMarker = (marker: any) => {
  console.log("移除点标记")
  map.remove(marker); //清除 marker
}

const drawRect = () => {
  console.log('绘制矩形')
  mouseTool.rectangle({
    strokeColor: 'red',
    strokeOpacity: 0.5,
    strokeWeight: 2,
    fillColor: 'blue',
    fillOpacity: 0.2,
    strokeStyle: 'solid',
  })
}

// 绘制圆形
const drawCircle = () => {
  console.log('绘制圆形')
  mouseTool.circle({
    strokeColor: "#FF33FF",
    strokeOpacity: 1,
    strokeWeight: 6,
    strokeOpacity: 0.2,
    fillColor: '#1791fc',
    fillOpacity: 0.4,
    // strokeStyle: 'solid',
    strokeStyle: 'dashed',
    strokeDasharray: [30, 10],
  })
}

// 绘制多边形
const drawOther = () => {
  mouseTool.polygon({
    strokeColor: "#FF33FF",
    strokeOpacity: 1,
    strokeWeight: 4,
    strokeOpacity: 0.2,
    fillColor: '#1791fc',
    fillOpacity: 0.2,
    strokeStyle: "solid",
  })
}


/**
 * 停止绘制
 * 形参为true时，清除所有绘制的覆盖物
 * 形参为false时，仅仅停止绘制，不会清除覆盖物
 */
const stopDrawAndClear = () => {
  mouseTool.close(true)
}

onUnmounted(() => {
  map?.destroy();
});
</script>

<style scoped lang="less">
.map-wrapper {
  #amap {
    width: 100%;
    height: 100vh;
  }

  .draw-area {
    position: absolute;
    bottom: 10px;
    right: 200px;
    display: flex;
    justify-content: center;
    .select {
      background-color: white;
      padding: 0 10px;
      margin-right: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    .action {
      display: flex;
      align-items: center;
    }
  }
}

#panel {
  z-index: 999;
  position: absolute;
  top: 0;
  right: 0;
}

#line-panel {
  z-index: 999;
  position: absolute;
  width: 500px;
  min-height: 700px;
  top: 20%;
}
</style>
```

# 路径规划

```html
  <el-input v-model="startName" style="margin-right: 10px" placeholder="起始地点"></el-input>
  <el-input id="end" v-model="endName" style="margin-right: 10px" placeholder="目的地点"></el-input>
  <el-select v-model="rule">
    <el-option
      v-for="item in routeRules"
      :key="item.value"
      :label="item.label"
      :value="item.value"
      placeholder="请选择驾车算路策略"
    />
  </el-select>
```

```js
const routeRules = [
  {
    value: 0,
    label: '速度优先',
  },
  {
    value: 1,
    label: '费用优先',
  },
  {
    value: 2,
    label: '距离优先',
  },
  {
    value: 41,
    label: '躲避拥堵＋少收费',
  },
  {
    value: 32,
    label: '默认',
  },
]

let startName = ref('')
let endName = ref('')
let rule = ref('')
let routeArr = ref([])
let policy = ref('')
```



```js
// 路径规划
const goView = () => {
  // eslint-disable-next-line no-undef
  const driving = new AMapObj.Driving({
    map,
    // 驾车路线规划策略，AMap.DrivingPolicy.LEAST_TIME是最快捷模式
    // eslint-disable-next-line no-undef
    // policy: AMapObj.DrivingPolicy.LEAST_TIME  // 路线最快
    policy: rule.value  // 路线最快
  })
  const points = [
    { keyword: startName.value, city: '兰州' },
    { keyword: endName.value, city: '兰州' }
  ]

  driving.search(points, (status, result) => {
    // 未出错时，result即是对应的路线规划方案
    console.log('status=', status)
    console.log('result=', result)
    if (status === 'complete') {
      routeArr.value = result.routes[0].steps
      polictText.value = result.routes[0].policy
      console.log(routeArr.value)
    }
  })
}
```

### 完整代码

Map.vue

```vue
<template>
  <div class="map-wrapper">
    <div id="panel"></div>
    <div style="position: absolute;top: 10px;height: 40px;left: 100px;width: 1500px;z-index: 999; display: flex">
      <el-input v-model="searchText" placeholder="请输入搜索关键字" id="searchInputId"/>


      <el-input v-model="startName" style="margin-right: 10px" placeholder="起始地点"></el-input>
      <el-input id="end" v-model="endName" style="margin-right: 10px" placeholder="目的地点"></el-input>
      <el-select v-model="rule">
        <el-option
            v-for="item in routeRules"
            :key="item.value"
            :label="item.label"
            :value="item.value"
            placeholder="请选择驾车算路策略"
        />
      </el-select>
      <el-button @click="goView" style="margin-left: 10px">搜索</el-button>
    </div>
    <div style="height: 400px; width: 600px; position: absolute; top: 100px; z-index: 9999; border: 1px springgreen solid" v-if="routeArr.length > 0">
      路线规划策略： {{policy}}
      <el-row
        v-for="(item, index) in routeArr"
        :key="index"
      >
        {{item.instruction}}
      </el-row>
    </div>
    <div id="amap"></div>

    <div class="draw-area">
      <div class="select">
        <div style="margin: 10px 0">
          <el-button @click="drawOther">绘制多边形</el-button>
          <el-button @click="drawRect">绘制矩形</el-button>
          <el-button @click="drawCircle">绘制圆形</el-button>
          <el-button @click="stopDrawAndClear()">清除</el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";
import logo from "@/assets/vue.svg"
window._AMapSecurityConfig = {
  securityJsCode: "xxx",
};
let map: any = null
let placeSearch,
    AMapObj: any,
    geocoder: { getAddress: (arg0: any, arg1: (status: any, result: any) => void) => void; }
let mouseTool: any = null
let circleId = 1
let auto = ref('')
let autoOptions = {
  input: 'searchInputId',
}
let searchText = ref('')
let startName = ref('')
let endName = ref('')
let rule = ref('')
const routeRules = [
  {
    value: 0,
    label: '速度优先',
  },
  {
    value: 1,
    label: '费用优先',
  },
  {
    value: 2,
    label: '距离优先',
  },
  {
    value: 41,
    label: '躲避拥堵＋少收费',
  },
  {
    value: 32,
    label: '默认',
  },
]

let routeArr = ref([])
let policy = ref('')


onMounted(() => {
  initMap();
});

async function initMap() {
  await AMapLoader.load({
    key: 'xxx',
    version: '2.0',
    plugins:
        [ 'AMap.ToolBar',
          'AMap.Scale',
          'AMap.Geolocation',
          'AMap.PlaceSearch',
          'AMap.Geocoder',
          'AMap.HawkEye',
          'AMap.MapType',
          'AMap.CircleEditor',
          'AMap.MouseTool',
          'AMap.AutoComplete',
          'AMap.PlaceSearch',
          'AMap.Driving'
        ],
  }).then(AMap => {
        AMapObj = AMap
        map = new AMap.Map('amap', {
          zoom: 12,
          resizeEnable: true,
          /**
           * 创建AMap.Map对象时如果没有传入center参数
           * 地图也会自动定位到您所在城市并显示，这就是JS API的初始加载定位：无需传入对应参数就能获取大致的定位信息
           * 但是当写了center参数，那么地图也会显示到您传入的center参数对应的位置
           */
          center: [103.841856, 36.053549],
          // center: [116.397428, 39.90923],
          viewMode: "3D",
        })
        // 图层切换
        const mapType = new AMap.MapType()
        // 鹰眼
        const hawkEye = new AMap.HawkEye()
        // 缩放条
        const toolbar = new AMap.ToolBar({
          position: 'LT'
        })
        // 比例尺
        const scale = new AMap.Scale()
        // 定位
        const geolocation = new AMap.Geolocation({
          enableHighAccuracy: true,//是否使用高精度定位，默认:true
          timeout: 10000,          //超过10秒后停止定位，默认：无穷大
          maximumAge: 0,           //定位结果缓存0毫秒，默认：0
          convert: true,           //自动偏移坐标，偏移后的坐标为高德坐标，默认：true
          showButton: true,        //显示定位按钮，默认：true
          buttonPosition: 'LB',    //定位按钮停靠位置，默认：'LB'，左下角
          buttonOffset: new AMap.Pixel(10, 20),//定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
          showMarker: true,        //定位成功后在定位到的位置显示点标记，默认：true
          showCircle: true,        //定位成功后用圆圈表示定位精度范围，默认：true
          panToLocation: true,     //定位成功后将定位到的位置作为地图中心点，默认：true
          zoomToAccuracy:true      //定位成功后调整地图视野范围使定位位置及精度范围视野内可见，默认：false
        });
        geocoder = new AMap.Geocoder({
          city: '全国',
        });
        map.addControl(geolocation)  // 定位组件
        map.addControl(toolbar)  // 放大缩小组件
        map.addControl(scale)  // 比例尺
        map.addControl(hawkEye)  // 鹰眼
        map.addControl(mapType)  // 图层切换

        poiSearch()

        bindEvent()

        // 点标记
        createMarker()

        // 覆盖物
        mouseTool = new AMap.MouseTool(map); //创建鼠标工具插件实例
      }
  );
}

// 搜索
const poiSearch = () => {
  auto.value = new AMapObj.AutoComplete(autoOptions)
  const placeSearch = new AMapObj.PlaceSearch({
    map: map,
    city: '', // 减小搜索范围至某个城市
    pageSize: 5, // 单页显示结果条数
    pageIndex: 1, // 页码
    citylimit: false, // 是否强制限制在设置的城市内搜索
    autoFitView: true,
    panel: "panel", // 结果列表将在此容器中进行展示。出不来时设置样式z-inde: 999
  })
  auto.value.on("select", function (e) {
    //针对选中的poi实现自己的功能
    placeSearch.search(e.poi.name);
  });
}

// 鼠标双击事件
const doubleClick = (e) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置双击了地图！')
  const x = e.lnglat.getLng()
  const y = e.lnglat.getLat()
  showInfo(e, x, y)
}

// 鼠标移动事件
const showInfoMove = () => {
  console.log('您移动了您的鼠标！')
}

// 鼠标点击事件
const signClick = (e) => {
  console.log('您在 [ '+e.lnglat.getLng()+','+e.lnglat.getLat()+' ] 的位置单击了地图！')
  const x = e.lnglat.getLng()
  const y = e.lnglat.getLat()
  showInfo(e, x, y)
}

// 地图事件绑定
const bindEvent = () => {
  console.log("事件绑定成功")
  map.on('click', signClick);
  // map.on('dblclick', doubleClick);
  // map.on('mousemove', showInfoMove);
}

// 信息窗体
const showInfo = (e: any, x: any, y: any, ) => {
  geocoder.getAddress(e.lnglat,(status, result) => {
    if (status === 'complete' && result.regeocode) {
      const address = result.regeocode.formattedAddress
      const infoContent = `
        <div style="padding:7px 0px 0px 0px;">
          <div class="global-header">当前位置</div>
          <p class='input-item'>经度: ${x}</p>
          <p class='input-item'>纬度: ${y}</p>
          <p class='input-item'>地址: ${address}</p>
          <img src="${logo}" alt="Vue Logo" width="100px;height=100px"/>
        </div>
      `
      const infoWindow = new AMapObj.InfoWindow({
        content: infoContent
      })
      infoWindow.open(map, new AMapObj.LngLat(x, y))
    } else {
      console.error('获取地址失败')
    }
  })
}

// 创建点标记
const createMarker = () => {
  //  点标记
  const markerContent =
      `<div class="custom-content-marker">
            <img src="//a.amap.com/jsapi_demos/static/demo-center/icons/dir-via-marker.png">
            <div class="close-btn">X</div>
          </div>`
  const position = new AMapObj.LngLat(103.841856, 36.053549); //Marker 经纬度
  const marker = new AMapObj.Marker({
    position: position,
    content: markerContent, //将 html 传给 content
    offset: new AMapObj.Pixel(-13, -30), //以 icon 的 [center bottom] 为原点
  });
  // 添加点标记
  map.add(marker);
  // 未关闭按钮添加点击事件
  const closeBtn = document.querySelector(".close-btn")
  if (closeBtn) {
    closeBtn.addEventListener('click', () => {
      clearMarker(marker); // 移除 marker
    });
  }
}

// 移除点标记
const clearMarker = (marker: any) => {
  console.log("移除点标记")
  map.remove(marker); //清除 marker
}


// 绘制矩形
const drawRect = () => {
  console.log('绘制矩形')
  mouseTool.rectangle({
    strokeColor: 'red',
    strokeOpacity: 0.5,
    strokeWeight: 2,
    fillColor: 'blue',
    fillOpacity: 0.2,
    strokeStyle: 'solid',
  })
}

// 绘制圆形
const drawCircle = () => {
  console.log('绘制圆形')
  mouseTool.circle({
    strokeColor: "#FF33FF",
    strokeOpacity: 1,
    strokeWeight: 6,
    strokeOpacity: 0.2,
    fillColor: '#1791fc',
    fillOpacity: 0.4,
    // strokeStyle: 'solid',
    strokeStyle: 'dashed',
    strokeDasharray: [30, 10],
  })
}

// 绘制多边形
const drawOther = () => {
  mouseTool.polygon({
    strokeColor: "#FF33FF",
    strokeOpacity: 1,
    strokeWeight: 4,
    strokeOpacity: 0.2,
    fillColor: '#1791fc',
    fillOpacity: 0.2,
    strokeStyle: "solid",
  })
}

/**
 * 停止绘制
 * 形参为true时，清除所有绘制的覆盖物
 * 形参为false时，仅仅停止绘制，不会清除覆盖物
 */
const stopDrawAndClear = () => {
  mouseTool.close(true)
}

// 路径规划
const goView = () => {
  // eslint-disable-next-line no-undef
  const driving = new AMapObj.Driving({
    map,
    // 驾车路线规划策略，AMap.DrivingPolicy.LEAST_TIME是最快捷模式
    // eslint-disable-next-line no-undef
    // policy: AMapObj.DrivingPolicy.LEAST_TIME  // 路线最快
    policy: rule.value  // 路线最快
  })
  const points = [
    { keyword: startName.value, city: '兰州' },
    { keyword: endName.value, city: '兰州' }
  ]

  driving.search(points, (status, result) => {
    // 未出错时，result即是对应的路线规划方案
    console.log('status=', status)
    console.log('result=', result)
    if (status === 'complete') {
      routeArr.value = result.routes[0].steps
      policy.value = result.routes[0].policy
      console.log(routeArr.value)
    }
  })
}


onUnmounted(() => {
  map?.destroy();
});
</script>


<style scoped lang="less">
.map-wrapper {
  #amap {
    width: 100%;
    height: 100vh;
  }
}

#panel {
  position: absolute;
  top: 0;
  right: 0;
  z-index: 999;
}


.draw-area {
  position: absolute;
  bottom: 10px;
  right: 200px;
  display: flex;
  justify-content: center;
  .select {
    background-color: white;
    padding: 0 10px;
    margin-right: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }
  .action {
    display: flex;
    align-items: center;
  }
}
</style>

```

style.css

```
.input-item {
    color: red;
}


.amap-info-close {
    margin-top: 12px;
    font-size: 20px;
}

.custom-content-marker {
    position: relative;
    width: 35px;
    height: 45px;
}

.custom-content-marker img {
    width: 100%;
    height: 100%;
}

.custom-content-marker .close-btn {
    position: absolute;
    top: -6px;
    right: -8px;
    width: 15px;
    height: 15px;
    font-size: 12px;
    background: #ccc;
    border-radius: 50%;
    color: #fff;
    text-align: center;
    line-height: 15px;
    box-shadow: -1px 1px 1px rgba(10, 10, 10, .2);
}

.custom-content-marker .close-btn:hover{
    background: red;
}

```

main.ts

```
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'

createApp(App).use(ElementPlus).mount('#app')
```

types/index.d.ts

```
export {};

declare global {
    interface Window {
        _AMapSecurityConfig: any;
    }
}
```

vite.config.ts

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from "path"
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src')
    }
  }
})
```

