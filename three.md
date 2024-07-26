# three.js 学习笔记
三要素
1. 场景(Scene): 可以看作是一个容器，相机、光线、物体、灯光等都会被添加到场景中。
2. 相机(Camera)： 定义观看的视角和位置
3. 渲染器(Renderer)： 将场景中的物体渲染到屏幕上
  


## 场景（Scene）
承载物体 灯光等的容器
### 创建scene
```js
var scene = new THREE.Scene();
// 添加相机的灯管物体的等
scene.add()
```
### 场景背景background
```js
scene.background = new THREE.Color(0x000000);
scene.background = new THREE.Color('rgb()');
scene.background = new THREE.Color('rgba()');
scene.background = new THREE.Color('hsl()');
```


## 相机（Camera）
> 正交相机 透视相机 立方相机 立体相机
> 只有有了相机才能看到创建的物体，也就是视野
### 创建透视相机 PerspectiveCamera
> 视野在near 和 far 中间

```ts
var camera = new THREE.PerspectiveCamera(fov: number, aspect: number, near: number, far: number);
```
::: info 单词
Perspective /pərˈspektɪv/  
n透镜，望远镜; 观点，看法; 远景，景色; 洞察力 
adj（按照）透视画法的; 透视的
:::

- fov: 视野角度
- aspect: 宽高比 一般为容器canvas的宽高比
- near: 近裁剪面
- far: 远裁剪面
> near 到 far 之间的物体才会被渲染，
>视野是一个锥体 视野默认是在中心点（空间坐标系的原点）

### 设置相机位置
```js
var camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
camera.position.x = 0;
camera.position.y = 0;
camera.position.z = 6;
camera.position.set(0,0,6)
```

### 设置相机朝向
```js
camera.lookAt(0,0,0)
```

### 控制相机(相机运动)
>  设置xyz的位置，坐标为半径乘以cos 和 sin
```js
camera.position.x = Math.cos(time) * 3;
camera.position.z = Math.sin(time) * 3;
```



## 渲染器（Renderer）
> 用于将构建好的场景渲染到canvas 上
### 有 dom 元素
不能在生成 renderer 实例时设置 canvas 属性
```js
// 没有canvas时
var renderer = new THREE.WebGLRenderer({
  antialias: true // 边缘抗锯齿，增加边缘像素点
  shadowMap： true // 开启阴影
});


```

### 没有 dom 元素
在生成 renderer 实例时设置 canvas 属性
```vue 
// 有canvas 时
const  renderer = new THREE.WebGLRenderer({ canvas: document.querySelector('canvas'));
```
### 在 vue 中的使用(可以利用没有dom属性的方式生成)
 
```js
const renderer = new THREE.WebGLRenderer({
  antialias: true // 边缘抗锯齿，增加边缘像素点
  shadowMap： true // 开启阴影
});
const container = ref();
onMounted(() => {
   container.value.appendChild(renderer.domElement)
})
```

### 完整实例

```js
<script setup lang="ts">
import { ref, onMounted } from "vue";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import * as THREE from "three";
const container = ref();
const { innerHeight, innerWidth } = window;

const camera = new THREE.PerspectiveCamera(
  70,
  innerWidth / innerHeight,
  0.1,
  10000
);
camera.position.z = 5;

const geometry = new THREE.BoxGeometry(1, 1, 1, 32, 32, 32);
const material = new THREE.MeshBasicMaterial({
  color: "#000",
  wireframe: true,
});
const cube = new THREE.Mesh(geometry, material);

const scene = new THREE.Scene();
scene.background = new THREE.Color("#eaeaea");
scene.add(cube);

const renderer = new THREE.WebGLRenderer({});
const orbitControls = new OrbitControls(camera, renderer.domElement);

function animate() {
  requestAnimationFrame(animate);
  renderer.setSize(innerWidth, innerHeight);
  orbitControls.update();
  renderer.render(scene, camera);
}

onMounted(() => {
  container.value.appendChild(renderer.domElement);
  animate();
});
</script>

<template>
  <div ref="container"></div>
</template>
‰
<style scoped>
html body {
  padding: 0;
  margin: 0;
  overflow: hidden;
}
</style>

```



## 几何体(Geometry)
  - BoxGeometry 立方体
  - SphereGeometry 球体
  - TorusGeometry 圆环
  - PlaneGeometry 平面
  - CircleGeometry 圆形
  - ConeGeometry 圆锥
  - CylinderGeometry 圆柱
  - TorusKnotGeometry 圆环结
  - TubeGeometry 管道
  - DodecahedronGeometry 十二面体
  - OctahedronGeometry 八面体
  - TetrahedronGeometry 四面体

::: info geometry /dʒiˈɑmətri/
n 几何学; 几何形状; 形状; 形状学
:::

```js
// 创建结构
const geometry = THREE.PlanGeometry(5, 5, 5, 5);
// 默认值都是1
// with: 宽度
// height: 高度
// widthSegments: 宽度分段数
// heightSegments: 高度分段数
// 分段数越高 越浑圆
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
// 组合结构和材质 
const mesh = new THREE.Mesh(geometry, material);
```
##### BufferGeometry

```js 
//设置定点数据 Float32Array
const vertices = new Float32Array([
  -1.0, -1.0, 1.0,
  1.0, -1.0, 1.0,
  1.0, 1.0, 1.0,

  1.0, 1.0, 1.0,
  -1.0, 1.0, 1.0,
  -1.0, -1.0, 1.0
]);
// 创建缓冲几何体
const geometry  = new BufferGeometry()

// 设置几何体数据
geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));
geometry.setAttribute('color', new THREE.BufferAttribute(vertices, 3));
//设置颜色需要 材质对象允许设置自定义
```

## 材质（Material）
::: info material /məˈtɪriəl/
n 材料; 物质; 物质资料; 物质基础
:::
皮肤设置： 颜色 纹理 光照响应 「漫反射 镜面反射 高光 哑光」

- MeshBasicMaterial 基础材质 不受光源影响
- MeshLambertMaterial Lambert材质
- MeshPhongMaterial Phong材质
- MeshStandardMaterial 标准材质
-   物理材质
- LineBasicMaterial 基础线材质
- LineDashedMaterial 虚线材质
 
```js
// 只能设置一种颜色
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
```
color 反射光
emissive 自发光
map 贴图 

::: info mesh /meʃ/
n. 网孔，网眼，网状物; 陷阱，困境; [机]（齿轮的）啮合
:::

### 贴图（texture）
::: info texture /ˈtekstʃər/
n. 纹理，文理; 贴图; 纹章; 情感，感受
:::
 
```js
// 贴图
// 资源加载器 接收好4个参数 资源类型 成功回调 过程回调 失败回调
const texture = new THREE.TextureLoader().load('texture.jpg');
const material = new THREE.MeshBasicMaterial({ map: texture });
```

 - aoMap 环境光贴图 
 - normal 法线贴图
 - displacementMap 离散贴图
 - roughnessMap 粗糙度贴图
 - metalnessMap 金属度贴图
 - alphaMap 透明贴图
 - envMap 环境贴图
 - lightMap 光照贴图

## 光源（light）

- 环境光（AmbientLight）
- 平行光（DirectionalLight）
- 点光源（PointLight）
- 聚光灯（SpotLight）
- 半球光（HemisphereLight）

```js
const light = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(light);
```

::: info light /laɪt/
n. 光; 光亮; 光线; 照明
:::

#### 光源辅助 （明确给出具体的方向）


#### 阴影效果（光源 物体 渲染器）
光源要支持产生阴影
物体要支持接收阴影
渲染器要支持渲染阴影
材质要接收阴影


```js
// 渲染器
renderer.shadowMap.enabled = true;
// 光源
light.castShadow = true;
// 物体
mesh.castShadow = true;
receiveShadow = true
```
？？？？？？
阴影的分辨率
```js
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

## 加载器loader（加载图片视频音频等） 
```js
var textureLoader = new THREE.TextureLoader();
textureLoader.load('path/to/texture.jpg', function(texture) {
    scene.background = texture;
});
```


::: info 单词
  Texture: /[ˈtɛkstʃɚ]/ 质地; 结构; 本质
:::


```js 
const  renderer = new THREE.WebGLRenderer({ canvas: document.querySelector('canvas'));
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);
// 将相聚和场景渲染的页面上
renderer.render(scene, camera);
```