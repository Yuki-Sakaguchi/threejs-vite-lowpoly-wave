# Three.jsでローポリの波みたいなものを作る

![result](https://user-images.githubusercontent.com/16290220/128708316-35353076-2a09-4051-b70f-7c3ddde2cd77.gif)

- [Three.js](https://threejs.org/)
- [Vite](https://vitejs.dev/)

# DEMO
- part01
  - 動画通りにやってみたやつ
  - https://yuki-sakaguchi.github.io/threejs-vite-lowpoly-wave/part01/dist/
- part02
  - リサイズに対応
  - スクロールに対応したアニメーションを対応
  - https://yuki-sakaguchi.github.io/threejs-vite-lowpoly-wave/part02/dist/

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

## スクロールなどのHTMLと連動させる時に気をつけること

ちゃんと数値に応じて場所を決める計算にしたほうが良い  
単純に値を増加、現象させちゃうとレスポンシブやDOMの変更に弱くなるので、元々の値と目指していく値を決めてちゃんと計算で決める  
そうすればリサイズしてもいい感じに場所が決まる