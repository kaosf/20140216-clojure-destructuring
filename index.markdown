# Destructuring
## 分配束縛

* author: ka
  * Homepage: [kaosfield.net](http://kaosfield.net)
  * Twitter: [@ka_](https://twitter.com/ka_)
  * GitHub: [kaosf](https://github.com/kaosf)
* date: 2014-02-16


Clojure における関数の引数の妙技

(Common Lisp でもあるはず)


### 配列を引数に渡す場合

```clj
(def p1 [10 20])
(def p2 [30 40])
```

座標を表すものをこのように管理して

```clj
(defn distance [p1 p2]
  (let [dx (- (nth p1 0) (nth p2 0))
        dy (- (nth p1 1) (nth p2 1))]
    (Math/sqrt (+ (* dx dx) (* dy dy)))))

(distance p1 p2) ;-> 28.284271247461902
                 ; 20√2
```

としたりすることがある


## めんどくさくね？

```clj
(- (nth p1 0) (nth p2 0))
(- (nth p1 1) (nth p2 1))
```

* `nth` 4 回
* `0` `1` というマジックナンバー 2 回ずつ


## 分配束縛で解消出来る

```clj
(defn distance [[x1 y1] [x2 y2]]
  (let [dx (- x1 x2)
        dy (- y1 y2)]
    (Math/sqrt (+ (* dx dx) (* dy dy)))))
```


## イメージ

```clj
(defn f [x y] ...)

(f  1 2) ; call
;   ~ ~
;   | |
;   V V
(f [x y])
```


```clj
(defn f [x y] ...)

(f  [1 2] [3 4]) ; call
;   ~~~~~ ~~~~~
;   |     |
;   V     V
(f [x     y])
```


## 分配束縛だと

```clj
(defn f [[x1 x2] [y1 y2]] ...)

(f  [1  2]  [3  4]) ; call
;    ~  ~    ~  ~
;    |  |    |  |
;    V  V    V  V
(f [[x1 x2] [y1 y2]])
```

そのままの形で受け取って名前も決められる


## 他にもあるよ

マップを受け取る

```clj
(defn f [{x :a y :b}] ...)

(f {:a 1    :b 2}) ; call
;      ~       ~
;      |       |
;      V       V
(f [{  x :a    y :b}])
```


マップのキーがキーワードの場合

キーワードから `:` を除いたシンボル名を変数にする

```clj
(defn f [{:keys [x y]}] ...)

(f {     :x 1 :y 2}) ; call
;           ~    ~
;           |    |
;           V    V
(f [{:keys [x    y]}] ...)
```

`:keys` という特別な指定が出来る


マップのキーがキーワードの場合は `:keys`

シンボルの場合は `:syms`

文字列の場合は `:strs`


## 補足: let について

`let` は

```clj
(let [x ( ...(1)... )
      y ( ...(2)... )]
  ...(3)... )
```

`...(1)...` の値に `x` が束縛され  
`...(2)...` の値に `y` が束縛され  
`...(3)...` の中で `x` `y` が使える

`(let ...)` 自体の評価結果は `...(3)...` の評価結果になる


`let` はローカル変数を扱うような感覚

ここでも変数の束縛を行う

そのルールも関数の引数と同じ


## 例示は理解の試金石

```clj
(let [[x y] [1 2]] [y x]) ;-> [2 1]
(let [{x :a} {:a 1}] x) ;-> 1
(let [{x :a} {:b 1}] x) ;-> nil
(let [{x :a y :a} {:a 1 :b 2 :c 3}] [x y]) ;-> [1 1]
(let [{:keys [x y]} {:a 1 :b 2}] [x y]) ;-> [nil nil]
(let [{:keys [x y]} {:a 1 :b 2 :x 3 :y 4}] [x y]) ;-> [3 4]
```


## 参考サイト

* [GPソフト Wiki - ClojureのDestructuring(分配束縛)](http://gpsoft.dip.jp/hiki/?Clojure%A4%CEDestructuring%28%CA%AC%C7%DB%C2%AB%C7%FB%29)
