---
title: Vue3食用百度地图api
data: 2022-03-21 12:00
tags:
  - 三方库
categories: 插件、依赖
---

难点：地图自定义标记点，每个标记点自定义点击事件

<!-- more -->
#### **1.动态script标签引入**
```js
// vue3百度地图适配
export function MP(ak) {
  return new Promise(function (resolve, reject) {
    window.init = function () {
      resolve(window.BMap)
    }
    var hasInsert = document.getElementById('bdMap')
    if (!hasInsert) {
      var script = document.createElement('script')
      script.id = 'bdMap'
      script.type = 'text/javascript'
      // BMap引入版本是2.0  <script src="http://api.map.baidu.com/api?v=2.0&ak=您的秘钥"></script>
      // BMapGL引入版本是1.0  <script src="http://api.map.baidu.com/api?type=webgl&v=1.0&ak=您的密钥"></script>
      // new BMap()和new BMapGL()
      script.src = 'http://api.map.baidu.com/api?v=1.0&type=webgl&ak=' + ak + '&callback=init'
      // onerror图片加载失败触发此事件
      // <img src="images/logo.png" onerror="javascript:this.src='images/logoError.png';">
      // 可以使用一张默认图片代替
      script.onerror = reject()
      document.head.appendChild(script)
    }
  })
}
```
#### **2.标签**
```html
<div id="container" style="width: 100%; height: 100%"></div>
```
#### **3.百度api方法**
```js
// 导入方法
import { MP } from '@/utils/baiduMap'
onMounted(() => {
  // 获取浏览器定位
  // 动态引入较大类库避免影响页面展示
  nextTick(() => {
    // 调用方法动态导入百度地图api
    const ak = 'WXWS0hfkwMqsjFEGs3y6FvnZGWVqwizP'
    MP(ak).then(BMap => {
      mapVm = new BMapGL.Map('container') // 创建Map实例绑定元素
      initMap(mapVm)
    })
  })
})
// 初始化百度地图渲染
const initMap = map => {
  // 创建地图实例
  var point = new BMapGL.Point(116.404, 39.915)
  // 创建点坐标
  map.centerAndZoom(point, 13)
  var scaleCtrl = new BMapGL.ScaleControl() // 比例尺控件
  map.addControl(scaleCtrl)
  var zoomCtrl = new BMapGL.ZoomControl() // 缩放控件
  map.addControl(zoomCtrl)
  // 创建城市选择控件
  var cityControl = new BMapGL.CityListControl({
    anchor: 'BMAP_ANCHOR_TOP_LEFT', // 控件的停靠位置（可选，默认左上角）
    offset: new BMapGL.Size(10, 5), // 控件基于停靠位置的偏移量（可选）
  })
  // 创建定位控件
  var locationControl = new BMapGL.LocationControl({
    anchor: 'BMAP_ANCHOR_BOTTOM_LEFT', // 控件的停靠位置（可选，默认左上角）
    offset: new BMapGL.Size(20, 60), // 控件基于停靠位置的偏移量（可选）
  })
  // 将控件添加到地图上
  map.addControl(cityControl)
  map.addControl(locationControl)
  // 定位控件：定位成功事件
  locationControl.addEventListener('locationSuccess', function (e) {
    var address = ''
    address += e.addressComponent.province
    address += e.addressComponent.city
    address += e.addressComponent.district
    address += e.addressComponent.street
    address += e.addressComponent.streetNumber
    console.log('当前定位地址为：' + address)
  })
  // 定位控件：定位失败事件,不清楚怎么用
  locationControl.addEventListener('locationError', function (e) {
    console.log(e.message)
  })
  // 地图拖拽停止事件
  map.addEventListener('dragend', function () {
    console.log('地图拖拽停止')
  })
}
/**
 * Marker标记点
 * @param point 百度坐标
 * @param info_html 信息窗样式html
 **/
const createMarker = (point, info_html) => {
  // 创建小车图标
  var myIcon = new BMapGL.Icon(
    'https://img.ejiayou.com/upload/2021/1/671fdd0b-98ee-4bbc-b465-054e91ec09bd-1611049838910.png',
    new BMapGL.Size(26, 40)
  )
  // 创建Marker标注，使用小车图标
  var marker = new BMapGL.Marker(point, {
    // icon: myIcon,
  })
  // marker标记点事件
  marker.addEventListener('click', function (e) {
    // 打开对应的信息窗
    marker.openInfoWindow(new BMapGL.InfoWindow(info_html), point)
  })
  // 必须return marker，不然marker点击事件只会展示最后一个信息窗
  return marker
}
/**
 * Marker标记点对应的文本标注
 * @param point 百度坐标
 * @param labelName 文本标注展示
 **/
const createLabel = (point, labelName) => {
  // 指定文本标注所在的地理位置
  var opts = {
    position: point,
  }
  // 将标注添加到地图
  var label = new BMapGL.Label(labelName, opts)
  label.setStyle({
    border: 'none',
    padding: 0,
    transform: 'translateX(-50%) translateY(-200%)',
    backgroundColor: '0.000000000001', //通过这个方法，去掉背景色
    backgroundSize: '100% 100%',
    color: '#333',
    fontSize: '0.44rem',
    fontWeight: 'bold',
    textShadow: '-0.05rem 0 white,0 0.05rem white,0.05rem 0 white,0 -0.05rem white',
  })
  mapVm.addOverlay(label)
}
/**
 * 信息窗html模板
 * @param data 数据
 * @param list 数据展示的字段名，字段值
 **/
const createHtml = (data, list) => {
  let context
  list.forEach((item)=>{
      context += `
        <div>
            <span style='white-space:nowrap'>${[item.key]}:</span>
            <span>${data[item.value]}</span>
        </div>
      `
  })
  let html = `<div style='display:flex;flex-flow:column;font-size:0.8vw'>${context}</div>`
  return html
}
// 请求接口之后调用这个方法，渲染返回的数据展示，多个标记点
const addStationTag = () => {
  var list = listDataByMap.list || [] //接口数据
  var listKeyValue = [
    { key: '油站名'; value: 'stationName' },
    { key: '地址'; value: 'address' },
    { key: '价格'; value: 'price' },
  ] //接口数据对应key和value值
  if (list && list.length > 0) {
    for (let i = 0; i < list.length; i++) {
      var point = new BMapGL.Point(list[i]['longitude'], list[i]['latitude'])
      createLabel(point, list[i]['stationName'])
      var html = createHtml(list[i], listKeyValue)
      var marker = createMarker(point, html)
      mapVm.addOverlay(marker)
    }
  }
}
// 获取定位
const geolocation = () => {
    var geolocation = new BMapGL.Geolocation(); //创建定位对象
    geolocation.getCurrentPosition(function (r) { //通过定位对象调用定位函数,回调函数形参r表示定位结果
    // this = geolocation这里this指向本身
    if (this.getStatus() == 'BMAP_STATUS_SUCCESS') { //如果定位成功
        var mk = new BMapGL.Marker(r.point); //创建标记,r是定位结果,r.point就是当前定位的地点
        mapVm.addOverlay(mk); //将标记对象添加到地图上
        mapVm.panTo(r.point); //将地图中心点移动到定位地点
    }
}
```
```js
// vite.config.ts文件配置
export default defineConfig(() => {
  // 插件
  const plugins: (Plugin | Plugin[] | any)[] = [
    resolveExternalsPlugin({
      BMap: "BMap",
    }),
  ];
```

<!-- more -->
