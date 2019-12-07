## RustでWebAssemblyのインタプリタを作った話
2019/12/08  
tkr

---

### スライド資料
<img src="qr.png" width="256" height="256">

https://gitpitch.com/kgtkr/slide-student-lt-32

---

### 自己紹介
* 名前:tkr
* Twitter:@kgtkr
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

### watについて
* wasm(バイナリフォーマット)と1対1対応の命令を持っているテキストフォーマットの言語
* wasmと相互変換可能
* wasmを機械語とするならwatはアセンブリ

---

### watを読んでみよう

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

---

### デコード・エンコード
仕様書の`Binary Format`を参考に実装する。

---

### valtypeの例

---

#### 定義

```
valtype ::= 0x7F => i32
        |   0x7E => i64
        |   0x7D => f32
        |   0x7C => f64
```

---

#### デコーダー

```rs
impl Decoder for ValType {
    fn decode(input: &[u8]) -> IResult<&[u8], ValType> {
        alt((
            map(parser::token(0x7f), |_| ValType::I32),
            map(parser::token(0x7e), |_| ValType::I64),
            map(parser::token(0x7d), |_| ValType::F32),
            map(parser::token(0x7c), |_| ValType::F64),
        ))(input)
    }
}
```

---

#### エンコーダー

```rs
impl Encoder for ValType {
    fn encode(&self, bytes: &mut Vec<u8>) {
        bytes.push(match self {
            ValType::I32 => 0x7f,
            ValType::I64 => 0x7e,
            ValType::F32 => 0x7d,
            ValType::F64 => 0x7c,
        });
    }
}
```

---

### 実行
* スタックマシンになっている
* 今回は複数のネストしたスタックを使っている

---

### i32.addの例

```rs
let y = self.stack.pop().unwrap().unwrap_i32();
let x = self.stack.pop().unwrap().unwrap_i32();
self.stack.push(Val::I32(x.overflowing_add(y).0));
```

---

### returnの例

```rs
let ret = cur_label.stack.pop();
if self.stack.pop().unwrap().frame.n != 0 {
    self.stack
        .last_mut()
        .unwrap()
        .stack
        .last_mut()
        .unwrap()
        .stack
        .push(ret.unwrap());
}
```

---

### 公式のテストケース
公式がテストケースを用意しているのでそれを使うとテストできる  

https://github.com/WebAssembly/spec/tree/master/test/core

---

### 自作言語を自作インタプリタで動かす
昔wasmにコンパイルする自作言語を作った。  
それを動かしてみよう。

---

### ライブデモ

---

### 自作言語 on 自作インタプリタ on 自作インタプリタ

自作インタプリタはRust製。  
Rustはwasmにコンパイルできる。つまり？

---

### ライブデモ

---

qiitaのwasmアドカレの12/22の枠に投稿するよ
https://qiita.com/advent-calendar/2019/wasm

---

### ご清聴ありがとうございました
