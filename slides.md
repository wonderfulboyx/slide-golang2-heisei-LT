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

2019年5月1日がなんの日か知っていますか？

---

GoチームがGo2に追加する機能について

最終意志決定する日です！！

![](images/final-decision.png)

https://blog.golang.org/go2-here-we-come

---

## Goのエラーハンドリングが<br>令和で変わる!!(かも)


まだドラフトの話です

---

## 平成(Go1)までのエラーハンドリング

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
		- エラーをreturnして処理してもらう
		- `err`が`nil`かどうかでハンドリングする

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
	  - どこでミスったかわかりやすい
	- レビューしてて読みやすい

---

### よくないところ
- 冗長

---

## 令和(Go2)のエラーハンドリング

---

Go2では`check式` / `handle構文` が導入されます！！(多分)

---

見た目の比較

---

Go1

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

Go2

```go
func addStrAsInt(a, b string) (int, error) {
	handle err { return 0, err }
	x := check strconv.Atoi(a)
	y := check strconv.Atoi(b)
	return x + y, nil
}
```

---

## 🎉
すごい!!めちゃスッキリしてる!!

```go
func addStrAsInt(a, b string) (int, error) {
	handle err { return 0, err }
	x := check strconv.Atoi(a)
	y := check strconv.Atoi(b)
	return x + y, nil
}
```
---

## どう動くのか

戻り値の末尾のerrorがnilかどうか判定してくれる
	
- これだけで
```go
handle err { return 0. err }
x := check strconv.Atoi(a)
```
- 以下に相当することをやってくれる
```go
x, err := strconv.Atoi(a)
if err != nil {
	return <handle構文内の処理>
}
```

---

もしhandleが複数あったら？

```go
func process(user string, files chan string) (n int, err error) {
    handle err { return 0, fmt.Errorf("process: %v", err)  }      // handler A
    for i := 0; i < 3; i++ {
        handle err { err = fmt.Errorf("attempt %d: %v", i, err) } // handler B
        handle err { err = moreWrapping(err) }                    // handler C

        check do(something())  // check 1: C, B, A の順にhandleが評価される
    }
    check do(somethingElse())  // check 2: Aのhandleが評価される
}
```

---

## さらに嬉しいところ

checkは式なので評価結果を直接関数に渡せる

---

これが
```go
x, err := strconv.Atoi(a)
if err != nil {
	return 0, err
}
y, err := strconv.Atoi(b)
if err != nil {
	return 0, err
}
add(x, y) // intを2つとる関数


// こうは書けない！！
// add(strconv.Atoi(a), strconv.Atoi(b))
```

---

こうなる！
```go
handle err { return 0, err }
add(check strconv.Atoi(a), check strconv.Atoi(b))
```

---

簡潔！！

---

まとめ
- 礼和元年5月1日はGo2の最終意思決定の日
- エラーハンドリングが簡潔になるかも！！