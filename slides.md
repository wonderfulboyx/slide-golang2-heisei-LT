# 令和のGo

---
<img src='images/icon.jpg' width='25%'>
- 名前: 片栗粉とろみ
- twitter: [@wonderfulboy_x](https://twitter.com/wonderfulboy_x)
- blog: [片栗粉とろみ](http://wonderfulboyx.hatenadiary.com/)
- 技術スタック
  - サーバー: golang
  - フロント: React/Redux

---

golang結構好きです

---

## goといえば

```go
if err != nil {
  
}
```

---

## このエラーハンドリングが<br>令和で変わる!!(かも)


Go2のドラフトの話です

---

## 平成までのGo

---

2つの文字列をIntに変換して足すコード

```go
func addStrAsInt(a, b string) (int, error) {
	x, _ := strconv.Atoi(a)
	y, _ := strconv.Atoi(b)
	return x + y, nil
}
```
---

エラーハンドリングしようとすると…

---

```go
func addStrAsInt(a, b string) (int, error) {
	x, err := strconv.Atoi(a)
	if err != nil {
		return 0, err
	}
	y, err := strconv.Atoi(b)
	if err != nil {
		return 0, err
	}
	return x + y, nil
}
```
---

半分if文じゃねえか！

---

いちいちif文コピペすんのかよ？

---

いろいろなソリューション

<img src='images/err-switch.gif' height='450px'>

https://imgur.com/gZUFqbc

---

## なんでこうなるか

---

### Goに例外機構はない

- throw/try/catchみたいな便利なものはない
  - じゃあどうするか
    - 戻り値として返却して処理してもらう

```go
x, err := strconv.Atoi(a)
if err != nil {
  return err
}
```

---

### いいところ
- 可読性が高い=読めば何やってるかわかる
  - 戻り値を見ればエラーの可能性がわかる
  - エラーをどうハンドリングしてるか明確

---

### よくないところ
- 冗長

--- 