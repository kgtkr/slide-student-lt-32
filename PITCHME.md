## RustでWebAssemblyのインタプリタを作った話
2019/12/08  
tkr

---

### スライド資料
<img src="qr.png" width="256" height="256">

https://gitpitch.com/kgtkr/slide-student-lt-32

---

### 自己紹介
* tkr
* @kgtkr
* Web開発・自作言語(最近してない)・競プロ(最近してない)など
* TypeScriptやScalaを書いています
* 学生ではない(来年から大学行きます)
* 初LT

<img src="icon.png" width="128" height="128">

---

### 宣伝
Anontownという掲示板サービスの開発・運営をしています  
https://anontown.com/

<img src="anontown.png" width="128" height="128">

---

### 今回のリポジトリ
https://github.com/kgtkr/wasm-rs

---

### WebAssemblyについて
* Webで動くバイナリフォーマットの言語
* 略称wasm
* スタック領域は静的に型付けされている

---

### wasmはどう実行される
1. バイナリをデコード
2. 検証
3. 実行

<!---
今回は検証フェーズは未実装
ついでに内部表現→バイナリのエンコーダーも作った
小数命令は実装終わっていない
-->

---

### AST定義
仕様書の`Structure`を参考に実装する

---

### valtypeの例

---
#### 定義
```
valtype ::= i32 | i64 | f32 | f64
```

---

#### Rustコード

```rs
pub enum ValType {
    I32,
    I64,
    F32,
    F64,
}
```