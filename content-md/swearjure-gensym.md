# The Strange Case of Swearjure and the Gensym

If you haven't heard of Swearjure, it's a subset of the language Clojure, where all alphanumeric characters are banned. It was introduced in [this short talk](https://www.youtube.com/watch?v=WUVBlKPWgmE). 

Firstly, the challenge of returning a data structure is presented, since the only functions you can create are `#()`, which contain a call as the body. Normally, you would call `identity`, but you can't for obvious reasons. The given solution is:
```clojure
#([[1 2 3]] (+)) ;; same as (fn [] [1 2 3])
```
You put the value you want to return in a vector, then call it with `(+)`, which is just `0`, to extract the first element. However, a shorter (and in my opinion much nicer way) is to call an empty map with some key (such as `+`):
```clojure
#({}+ [1 2 3]) ;; same as (fn [] [1 2 3])
```
This syntax exists in three forms:
+ `(<data-structure> <key> <default>?)`
+ `(<atom-key> <data-structure> <default>?)`
+ `(<symbol-key> <data-structure> <default>?)`

With that small improvement out of the way, we can discuss what is probably the strangest Clojure expression I've seen ever:
```clojure
(#('%& '%& '%&))
```
This expression, through what seems like incomprehensible dark magic, gives a gensym. There isn't even a hashtag anywhere!

The first key insight is that, while this should be preserved as the awesome-looking version, a lot of it isn't actually necessary. Let's start with the fact that we're calling `'%&`. This is a symbol, and as mentioned above, when calling a symbol with two arguments, the symbol is looked up in the first, and if this fails, the second value is returned as a fallback. For example:
```clojure
('a {'a 42} 7) ;; returns 42
('a {} 7) ;; returns 7
```
Interestingly, this doesn't fail if the first argument isn't actually an associative data structure. Instead, it just returns the fallback value:
```clojure
('a 443 7) ;; returns 7
('a 'b 7) ;; returns 7
```
Thus, as you can imagine, the gensym expression is actually just equivalent to:
```clojure
(#(identity '%&))
```
In Swearjure, we can write a simpler version of the original using the `{}+` trick from the beginnning:
```clojure
(#({}+ '%&))
```

Now, we just have to figure out why `'%&` gives a gensym. Imagine trying to implement something similar to `#()` functions as a macro. I'd do something like:
```clojure
(defmacro wacky-lambda
  [body]
  `(fn [& args#]
    (let [arguments args#]
      ~body)))

(wacky-lambda (rest arguments)) ;; same as (fn [& args] (rest args))
```

This is exactly what the `#()` reader macro does, just without the `let`. If your `#()` function uses `%&`, you can see that it expands to `fn` with a gensym for the rest parameter. 
```clojure
(macroexpand '#(identity '%&)) ;; expands to...
((fn* [& rest__38#] (identity (quote rest__38#))))
```

That's all there is too it. All that's left is one small point: there's nothing special about `%&`, and `%` works just as well. Putting this together gives what I believe to be the shortest Swearjure gensym known so far:
```clojure
(#({}+ '%))
```
