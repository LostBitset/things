# Hello World in Swearjurescript

Hello world in Swearjure was created [here](https://hypirion.com/musings/hello-swearjure), using only five alphanumeric characters (it used the `ns-map` function). However, Clojurescript without alphanumeric characters doesn't have `ns-map`, so Swearjurescript demands another solution. 

Firstly, we'll need a short function that can execute arbitrary code. I used `js/eval`, which executes Javascript as a string. We need to be able to create strings without just using `char` and `str` like previous Swearjure code did. The first trick is that functions can be converted to string when you add them to a character, since the `+` function is just the Javascript `+` operator. 
```clojure
(+\_ +)
```
```
"_function cljs$core$_PLUS_() {\n  var args6946 = [];\n  var len__5181__auto___6952 = arguments.length;\n  var i__5182__auto___6953 = 0;\n  while (true) {\n    if (i__5182__auto___6953 < len__5181__auto___6952) {\n      args6946.push(arguments[i__5182__auto___6953]);\n      var G__6954 = i__5182__auto___6953 + 1;\n      i__5182__auto___6953 = G__6954;\n      continue;\n    } else {\n    }\n    break;\n  }\n  var G__6951 = args6946.length;\n  switch(G__6951) {\n    case 0:\n      return cljs.core._PLUS_.cljs$core$IFn$_invoke$arity$0();\n      break;\n    case 1:\n      return cljs.core._PLUS_.cljs$core$IFn$_invoke$arity$1(arguments[0]);\n      break;\n    case 2:\n      return cljs.core._PLUS_.cljs$core$IFn$_invoke$arity$2(arguments[0], arguments[1]);\n      break;\n    default:\n      var argseq__5200__auto__ = new cljs.core.IndexedSeq(args6946.slice(2), 0);\n      return cljs.core._PLUS_.cljs$core$IFn$_invoke$arity$variadic(arguments[0], arguments[1], argseq__5200__auto__);\n  }\n}"
```
We can turn this into a list of characters as `` `[~@(+\_ +)] `` since strings are `ISeqable`. This gives us almost any lowercase character, but getting arbitrary characters requires being able to write `String.fromCharCode`. We can actually avoid having to get `C` by using `Object.getOwnPropertyNames(String)` to get `"fromCharCode"`, but we now need to get `O` and `N`. 

We can get `O` by converting an object to a string:
```javascript
({}).toString() // gives "[object Object]"
({}).toString()[8] // gives "O"
```

We can get `N` by converting `NaN` to a string:
```javascript
(0/0).toString() // gives "NaN"
(0/0).toString()[0] // gives "N"
```

Now, we can create a Javascript function `$` to get arbitrary characters:
```javascript
var $ = eval("String."+Object.getOwnPropertyNames(String)[0]);
```

This gives us everything we need to write `alert("Hello World!")`:
```javascript
alert($(72)+$(101)+$(108)+$(108)+$(111)+$(32)+$(87)+$(111)+$(114)+$(108)+$(100)+$(33))
```

Using a function table, we can now write:
```clojure
;; 0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15 16 17 18 19 20 21
;; v, a, r, e, l, S, t, i, n, g, b, j, c, w, P, o, p, y, m, s, 0, 8
;; 22 -> space
(
    ;; ft, eval
    #((%1 :+) %1 %2)
    {
        :+ #( ;; ft, eval
            (%1 :-)
            %1
            %2
            [
                (`[~@(+\_ +)]33)
                (`[~@(+\_ +)]34)
                (`[~@(+\_ +)]35)
                (`[~@(+\_ +)]18)
                (`[~@(+\_ +)]11)
                (`[~@(+\_ +)]24)
                (`[~@(+\_ +)]5)
                (`[~@(+\_ +)]6)
                (`[~@(+\_ +)]3)
                (`[~@(+\_ +)]39)
                (`[~@(+\_ +)]384)
                (`[~@(+\_ +)]12)
                (`[~@(+\_ +)]4)
                (`[~@(+\_ +)]135)
                (`[~@(+\_ +)]21)
                (`[~@(+\_ +)]7)
                (`[~@(+\_ +)]222)
                (`[~@(+\_ +)]516)
                (`[~@(+\_ +)]87)
                (`[~@(+\_ +)]40)
                (`[~@(+\_ +)]457)
                (`[~@(+\_ +)]65)
                (`[~@(+\_ +)]9)
                ])
        :- #( ;; ft, eval, lvl1
            (%1 :*)
            %2
            %3
            [ ;; ["N" "O"]
                (%2 (+
                    "("
                    (%3 20) ;; 0
                    "/"
                    (%3 20) ;; 0
                    ")."
                    (%3 6) ;; toString
                    (%3 15)
                    (%3 5)
                    (%3 6)
                    (%3 2)
                    (%3 7)
                    (%3 8)
                    (%3 9)
                    "()["
                    (%3 20) ;; 0
                    "]"
                    ))
                (%2 (+
                    "({})."
                    (%3 6) ;; toString
                    (%3 15)
                    (%3 5)
                    (%3 6)
                    (%3 2)
                    (%3 7)
                    (%3 8)
                    (%3 9)
                    "()["
                    (%3 21) ;; 8
                    "]"
                    ))
                ])
        :* #( ;; eval, lvl1, lvl2
            %1
            (+
                (%2 0) ;; var
                (%2 1)
                (%2 2)
                (%2 22)
                "$"
                (%2 22) ;; %20
                "="
                (%2 22) ;; eval
                (%2 3)
                (%2 0)
                (%2 1)
                (%2 4)
                "(\""
                (%2 5) ;; String
                (%2 6)
                (%2 2)
                (%2 7)
                (%2 8)
                (%2 9)
                ".\"+"
                (%3 1) ;; Object
                (%2 10)
                (%2 11)
                (%2 3)
                (%2 12)
                (%2 6)
                "."
                (%2 9) ;; (get) getOwnPropertyNames
                (%2 3)
                (%2 6)
                (%3 1) ;; (Own)
                (%2 13)
                (%2 8)
                (%2 14) ;; (Property)
                (%2 2)
                (%2 15)
                (%2 16)
                (%2 3)
                (%2 2)
                (%2 6)
                (%2 17)
                (%3 0) ;; (Names)
                (%2 1)
                (%2 18)
                (%2 3)
                (%2 19)
                "("
                (%2 5) ;; String
                (%2 6)
                (%2 2)
                (%2 7)
                (%2 8)
                (%2 9)
                ")["
                (%2 20)
                "]);"
                ;; alert call
                (%2 1) ;; alert
                (%2 4)
                (%2 3)
                (%2 2)
                (%2 6)
                "("
                "$(" (+"" (+(*)(*)(*)(*)(*)(*)(*)) (+(*)(*))) ")+"
                "$(" (+"" (*) (+) (*)) ")+"
                "$(" (+"" (*) (+) (+(*)(*)(*)(*)(*)(*)(*)(*))) ")+"
                "$(" (+"" (*) (+) (+(*)(*)(*)(*)(*)(*)(*)(*))) ")+"
                "$(" (+"" (*) (*) (*)) ")+"
                "$(" (+"" (+(*)(*)(*)) (+(*)(*))) ")+"
                "$("
                (+""
                    (+(*)(*)(*)(*)(*)(*)(*)(*))
                    (+(*)(*)(*)(*)(*)(*)(*)))
                ")+"
                "$(" (+"" (*) (*) (*)) ")+"
                "$(" (+"" (*) (*) (+(*)(*)(*)(*))) ")+"
                "$(" (+"" (*) (+) (+(*)(*)(*)(*)(*)(*)(*)(*))) ")+"
                "$(" (+"" (*) (+) (+)) ")+"
                "$(" (+"" (+(*)(*)(*)) (+(*)(*)(*))) ")"
                ");"
                )
            )}
    js/eval)
```

This actually works!

Here it is without all the comments, numbers, or extra whitespace, in pure Swearjurescript glory:
```clojure
(#(((`[~@%&](+)) :+) (`[~@%&](+)) (`[~@%&](+(*)))){:+ #( ((`[~@%&](+)) :-) (`[~@%&](+)) (`[~@%&](+(*))) [ (`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)))(+""(+(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)))(+""(+(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)))(+""(+(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)))(+""(+(*)(*)(*)(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)))(+""(+(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)))(+""(+(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)))(+""(+(*)(*)(*)(*)(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)))(+""(+(*)(*)(*)(*)(*)(*)(*)(*)))(+""(+(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)))(+""(+(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)))(+""(+(*)(*)(*)))(+""(+(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)))(+""(+(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)))(+""(+(*)(*)))(+""(+(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)))(+""(+(*)))(+""(+(*)(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*)))(+""(+(*)(*)(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)))(+""(+)))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)))(+""(+(*)(*)(*)(*)(*)))(+""(+(*)(*)(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)))(+""(+(*)(*)(*)(*)(*))))))(`[~@(+\_ +)]((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*)(*)))))) ]) :- #( ((`[~@%&](+)) :*) (`[~@%&](+(*))) (`[~@%&](+(*)(*))) [ ((`[~@%&](+(*))) (+ "(" ((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)))(+""(+))))) "/" ((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)))(+""(+))))) ")." ((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)))(+""(+(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*)(*)))))) "()[" ((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)))(+""(+))))) "]" ))((`[~@%&](+(*))) (+ "({})." ((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)))(+""(+(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*)(*)))))) "()[" ((`[~@%&](+(*)(*))) ((`[~@%&](+(*))) (+(+""(+(*)(*)))(+""(+(*)))))) "]" )) ]) :* #( (`[~@%&](+)) (+ ((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+)))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)))(+""(+(*)(*)))))) "$" ((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)))(+""(+(*)(*)))))) "=" ((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)))(+""(+(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+)))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)))))) "(\"" ((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*)(*)))))) ".\"+" ((`[~@%&](+(*)(*))) ((`[~@%&](+)) (+(+""(+(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+)))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)))))) "." ((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+)) (+(+""(+(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*)(*))) ((`[~@%&](+)) (+(+""(+)))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*)(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)))(+""(+(*)(*)(*)(*)(*)(*)(*)(*)(*)))))) "(" ((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)(*)(*)(*)))))) ")[" ((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)))(+""(+))))) "]);" ((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*))))))((`[~@%&](+(*))) ((`[~@%&](+)) (+(+""(+(*)(*)(*)(*)(*)(*)))))) "(" "$(" (+"" (+(*)(*)(*)(*)(*)(*)(*))(+(*)(*))) ")+" "$(" (+"" (*)(+)(*)) ")+" "$(" (+"" (*)(+)(+(*)(*)(*)(*)(*)(*)(*)(*))) ")+" "$(" (+"" (*)(+)(+(*)(*)(*)(*)(*)(*)(*)(*))) ")+" "$(" (+"" (*)(*)(*)) ")+" "$(" (+"" (+(*)(*)(*))(+(*)(*))) ")+" "$(" (+"" (+(*)(*)(*)(*)(*)(*)(*)(*))(+(*)(*)(*)(*)(*)(*)(*))) ")+" "$(" (+"" (*)(*)(*)) ")+" "$(" (+"" (*)(*)(+(*)(*)(*)(*))) ")+" "$(" (+"" (*)(+)(+(*)(*)(*)(*)(*)(*)(*)(*))) ")+" "$(" (+"" (*)(+)(+)) ")+" "$(" (+"" (+(*)(*)(*))(+(*)(*)(*))) ")" ");"))}js/eval)
```

Feel free to try it out [here](http://clojurescript.net/) (assuming the website is still up and a new version of Clojurescript doesn't break this). 
