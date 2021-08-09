# Three.jsでローポリの波みたいなものを作る

- Three.js
- [Vite](https://vitejs.dev/)

# 参考
https://www.youtube.com/watch?v=YK1Sw_hnm58


# 開発

```bash
npm run dev
```

# 学んだこと

## viteで環境を構築する

[ViteのGetting Started](https://vitejs.dev/guide/#scaffolding-your-first-vite-project)

```bash
npm init vite@latest
```

これだけでts, vue, reactはもちろんvanillaの環境がすぐにできる。便利


## ジオメトリーの色を頂点ごとに設定する

`vertexColors: true` を追加すると設定することができるようになるみたい  
このとき全体の色を指定していた `color: 0xff0000` は消す   

```js
const planeGeometry = new THREE.PlaneGeometry(5, 5, 10, 10);
const planeMaterial = new THREE.MeshPhongMaterial({ side: THREE.DoubleSide, flatShading: THREE.FlatShading, vertexColors: true });
const planeMesh = new THREE.Mesh(planeGeometry, planeMaterial);
scene.add(planeMesh);
```

そして、頂点ごとに色を設定するので頂点を全部取得して繰り返して、`color` 属性を指定する  
あとで色を変えられるように一旦 `colors` 配列に頂点の数だけ色をプッシュしておいて、それを元に反映する  
positionとかと同じように3つの値ごとに一つの頂点情報になっているっぽい

```js
const colors = [];
for (let i = 0; i < planeMesh.geometry.attributes.position.count; i++) {
  colors.push(0, 1, 1);
}
planeMesh.geometry.setAttribute('color', new THREE.BufferAttribute(new Float32Array(colors), 3));
```

ぱっと見色が違うように見えるけどそれは影になっているからなので全部の頂点の色は同じ


## Three.jsのオブジェクトにホバーでアニメーションするときは `Raycaster` を使う

```js

// mousemoveイベントで値を更新する
const mouse = {
  x: undefined,
  y: undefined,
};

// ぶつかりを検知
raycaster.setFromCamera(mouse, camera);
const intersects = raycaster.intersectObject(planeMesh);
if (intersects.length > 0) {
  // アニメーションさせる
}
```