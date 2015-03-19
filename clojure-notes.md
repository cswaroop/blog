# My Clojure Nots reading Joy of Clojure, 2nd edition

Amazing book. check it out.

Clojure brings back the fun in programming even to enterprise (J2EE) zombies


##Basics

Therefore, in order to associate a name with the previous function using `def`, you use

``` clojure
   
(def make-set
  (fn [x y]
    (println "Making a set")
    #{x y}))

(make-set 1 2)

```

or you can use `defn`

``` clojure
    
(defn make-test
  "Takes two values and makes a set from them"
  [x y]
  (println "Making a set")
  #{x y})

```

Functions with different bodies depending on number of arguments `arity`(max: 20) 

(Note: Clojure being dynamic-typed cannot support function overloading based on type of arguments. Only hope is arity)

``` clojure
    
(defn make-set
  ([x] #{x})      ;; body-1
  ([x y] #{x y})) ;; body-2


(make-set 33)
(make-set 22 33)
    
````

Functions with variable number of arguments

``` clojure

(defn arity2+ 
  [first second & more]
  (vector first second more))

(arity2+ 1 2)        ; [1 2 nil]
(arity2+ 1 2 3 4)    ; [1 2 [3 4]]
(arity2+ 1)          ; ArityException

``` 

Anonymous function with `#()` reader form and arguments with %

(Note: #_ is another reader form)

``` clojure

(def make-list0 #(list))
(make-list0)             ;()

(def make-list2 #(list %1 %2))
(make-list2 1 2)

(def make-list2+ #(list %1 %2 %&))
(make-list2+ 1 2 3 4 5)

(def make-list1 #(list %))
(make-list1 1)

```

### Blocks

Use the `do` form for evaluation of series of expressions that need to be treated as *one*. One-ness means only the value of  *last* expression is returned

``` clojure
    
    (do
      (def x 5)
      (def y 4)
      (+ x y)
      [x y])  ; [5 4]

```

### Locals

Clojure doesn't have the local variables, but does have locals that can't vary (What!). Welcome to `let` form.  In clojure once a variable is assigned a value, it can't be further changed atleast with `let` form (its like static single assignment).  Hence they are called locals, but not local variables.

``` clojure

(let [a 10,
      b 3.5
      c a*b]
  (println c)
  (c))  ; 35

```

**NOTE** `let` acts like having an implicit `do`.  It also returns *only* the last value
**REMEBER** Locals are *immutable*. Hence you can't use it to accumulate results like sum = sum + x;

### Loops

In functional land, *recursion* is the king and is substituted for variety of things. Here its used as substitute for various forms of go, while, unless.  If clojure's strength is to create DSLs, it better show that strength to create these common control structures  out of mind bending recursion.  Yes there are loop macros `for` and block macros `when` made from `if` form  etc.  But first lets understand `recur`

`recur` is a tail recursion special form.  Lets print 10 to 1 counting backwards

``` clojure

(defn count-backwards [x]
  (when (pos? x))  ; pos? is positive and is used for loop termination condition
    (println x)
    (recur (dec x))) ; recur rebinds x and pass it back to `when`. not to the begining of function

```

This is kind of while loop.

How to achieve ` sum = sum + x` kind of operation?

```clojure

(defn sum-down [sum x]
  (if (pos? x)           ; if form has two parts then and else. then part has recursion and else part has sum here
    (recur (+ sum x) (dec x))  ; This recursion rebinds sum and x and takes it to begining of function call
    sum))

(sum-down 0 10) ; 55

```

*Note* Use `when` instead `if`
* No else part is associated
* You need an implicit do for side-effects


`loop` and `recur`

``` clojure

(defn sum-down-from [x]
  (loop [sum 0
         y   x]
    (if (pos? y)
      (recur (+ sum y) (dec y)) ; this recur to nearest loop
      sum))) 

```

Now some truths about the recur.  Its a tail-recursion operator and is restricted to appear in tail positions.

What is tail-position?

A form is in tail postion of an expression when its value may be the return value of the *entire* expression

``` clojure

(defn abs [x]
  (if (pos? x)
    x
    (- x)))

```

return value of the if expression is the return value of function;

Here if is in tail position for function abs
Only x is in tail position for if expression, but not (- x) as x `-` is a function call for which we need to wait

Don't worry if you ever use recur in wrong position you get compile-time error `java.lang.UnsupportedOperationException`


### Special Forms for Macro-writing

#### Quoting forms

1. Quote
2. Syntax-Quote

Quototing a form has several uses; first to introduce scalar literals and composites *without* evaluating them as code.

1. Remember clojure has a world-view to look everything as list and first element of list is a function call)
   e.g try `cons 1 (2 3)` gives `java.lang.ClassCastException`

2. When a collection is evaluated, each of its contained items is evaluated first unless that list starts with a macro or special-form
  e.g. ``` (cons 1 [2 3])``` has operands 1,2,3 evaluated first (scalars eval to themselves and so is vector) and passed to `cons`
  

Quote `(quote cons)` special operator, just like every other special operator, has its own rules of evaluation.  Quote doesn't evaluate any of its variable number of operands. Instead entire form evaluates to just the symbol.

`'(1 (+ 2 3))` returns `(1 (+ 2 3))`

`(cons 1 '(2 3))` will return `(1 2 3)`

Syntax-quote (unlike quote which evaluates to a simple form) provides additional features
1. Symbol auto-qualication
   e.g `map
   If the symbol doesn't name a var or class that exists yet, it uses current namespace (in repl it means `user` namespace)


Unquote (~x)

`(+ 10 ~(* 3 2))` Unquote in a Quote form lets the evaluation happen, which is otherwise suppressed!

```clojure

(let [x '(2 3)]
   `(1 ~x))  ;(1 (2 3))

```

Unquite-Splicing (~@x)


```clojure

(let [x '(2 3)]
   `(1 ~@x)) ; (1 2 3)

```

Auto-gensym (#x)

Inside a syntax-quote you might need a way to crate unique symbol such as for a paramter or `let` local name because you need to refer to the same symbol in multiple syntax-quotes or you want to capture a particular unqualified symbol

Append # to symbol

`x#


###  Interoperability with Host Object Oriented Libraries

Clojure allowed some deviations or missing things in its various implmentations (ClojureScript in Javascript environments, clojure in .Net environment) striving to be symbiotic with its host providing rich and powerful features along with host powerful librarites (Java Libs, JavaScript Libs, .Net Libs)

You have to do `import` before you can use or use fully qualified name; clojure imports java.lang by default on JVM.

#### Object Static members in Java

`(Math/sqrt 9)`

JavaScript has no static members

#### Object creation

`(new java.awt.Point 0 1)`
`(new java.util.HashMap {"foo" 42, "bar" true, "baz" "hurray"})`

or

`(java.awt.Point. 0 1)`
`(java.util.HashMap. {"foo" 42, "bar" true, "baz" "hurray"})`

in JavaScript

`(js/Date.)`  ;js is the default namespace given for global environment

#### Instance members

Getter of Public instance fields
(-.x (java.awt.Point. 10 20))

Setter of Public instance fields
(set! (.-x (java.awt.Point. 0 0)) 15)

Invoker of Public instance methods
(.divide (java.math.BigDecimal. "42") 2M)

Chaining instance method calls with  .. macro

(.endsWith (.toString (java.util.Date.)) "2015")

or

(.. (java.util.Date.) toString (endsWith "2015"))

Other thread-first macros (-> and ->>) makes this .. macro redundant.

doto macro to initalize a fresh instance by calling set of mutators

```clojure

(doto (java.util.HashMap.)
  (.put "HOME" "/home/me")
  (.put "SRC" "src")
  (.put "BIN" "classes"))

```

Define classes in clojure side(reify & deftype)
Generate classes in Java (gen-class)
Implement Interfaces and extend base bases (proxy)

Exception Handling (throw & catch)

`(throw (Exception. "Object not found in Db"))`

```clojure

(defn exception-example [f]
  [(try
    (f)
    (catch ArithmeticException e "Dived by zero")
	(catch Exception e (str "Oh no!" (.getMessage e)))
	(finally (println "Done!")))])

(exception-example #(/ 10 5))
(exception-example #(/ 10 0))
(exception-example #(throw (Exception. "Crybaby"))

(try
  (throw (Error. "error from CLJS"))
  (catch js/Error e "Caught an exception from CLJS"))

```
### Namespaces

`(ns joy.core)`
`*ns*`

```clojure

(ns joy.req
  (:require clojure.set :as s))  ; require loads from disk

(s/intersection #{1 2 3} #{3 4 5})

```

namespaces are only qualifier and cannot be referenced

`(:require [clojure.string :refer (capitalize)])`

to bring all public vars (don't use in files) e.g. in REPL
`(:require [clojure.string :refer :all])` ; equivalent of older :use

```clojure

(ns joy.all
  (:refer joy.ch1)  ; already loaded
  (:refer joy.ch2) ; already loaded
  (:refer clojure.set :rename {union onion}))


```

Loading Java classes (:import)

```
(ns joy.core
  (:import [java.util HashMap ArraryMap]
  [java.util.concurrent.atomic AtomicLong]))

(HashMap. {"happy" true})
(AtomicLong. 42)
```

Fully qualified are always available without any import and java.lang package is automatically imported.


### Logical Truth

Anything except `false` and `nil` is truth including 0,[], objects
This clojure rule and Java integration gives some surprises

`(def evil-false (Boolean. "false"))`
`(if evil-false :truthy :falsey)` ; :truthy

`(if (Boolean/valueOf "false") :truthy :falsey)`

nil?
false?
(seq []) ; nil

This simple rule also allows lots of idioms and elegant solutions in variety of situations.

```

(defn print-seq [s]
  (when (seq s)      ;seq as termination condition
    (prn (first s))
    (recur (rest s))))

(print-seq []) ; nil
```

rest vs next.

rest can return a seq that's either empty or not empty but never nil
next returns seq of the rest `(seq (rest s))` thus returns nil and never empty seq

### Destructuring DSL (place-binding)  and core.match (conditional-binding)


Positional destructuring of Vectors, java.util.regex.Matcher and CharSequence & java.util.RandomAccess interfaces

```

(let [[f m l] names
  (str l f m))

(let [[f _ l] names
  (str f l))

(let [[a b c & more] (range 10)]
(println a b c more))

(let [v (vec (range 10))
      [a b c & more :as all] v]
  (println a b c more all)

```

Destructuring of maps

```
(def names {:first "Guy" :middle "Lewis" :last "Steele"})

(let [{:keys [f m l] :as all} names]
  (str l f m all))
      


```

`:keys :strs :syms :as :or` are allowed in any order

Map destructuring also works for lists giving functions keyword argument ability like in python

```

(defn whole-name [& args]
  (let [{:keys [f m l]} args]
    (str l f m)))

(whole-name :f "Guy" :m "Lewis" :l "Steele")

```

Associative destructuring

```

(let [{first-thing 0 , last-thing 3} [1 2 3 4]]
  [first-thing last-thing])

```

Function Parameter destructuring

```
(defn print-last-names [{:keys [l-name]}]
  (println l-name))

(print-last-name {:l-name "Steele" :f-name "Guy" :m-name "S"})


```

### Collections

> Its better to have 100 functions operate on one data structure than 10 functions on 10 data structures -- Alan Perlis

Native clojure collections - Vectors, Lists, Maps, Sets, Queues
Java collections - Arrays, LinkedBlockedQueue


What is persistent data structure means?

Where Immutable collections becomes affordable in practical programming.

```clojure

(def ds (into-array [:a :b :c]))
(seq ds)

(aset ds 1 :q)  ; changes the arrary in-place!

```


But when it comes Clojure native data types like vector

``` 

(def ds [:a :b :c])
(def ds2 (replace {:b :q} ds))


```

Note the `replace` function.  It doesn't really replace in original datastructure; instead creates one; Just like locals are not variables.

#### Seq API

first, rest, nil and () makes up the seq API.

`seq` is an implemention of this API

`(seq [])` returns nil
`(seq [1 2])` returns (1 2)


`(= [1 2 3] '(1 2 3))` returns true


### `vec` vs `vector`

`(doc vec)`
`(doc vector)`


(vec (range 10))

If you already have a vector, but want to pour several values into it then `into` is your friend.

(let [v [:a :b :c]]
  (into v (range 10)))
;[:a :b :c 0 1 2 3 4 5 6 7 8 9]

`into` adds at the begining of vector (o(n) complexity),  where as `cons` adds at the end of vector.


#### Large Vectors

Relative to lists, vectors have the following advantages

* Adding and removing at the right side
* Accessing/changing in the collection by index
* Walking in reverse order


Length of the vector  by

`(count [1 2 3])`

indexed from `[0 count)` and index accessed by `nth` or `get` or by using vector itself as function

`(nth [1 2 3] 1)`
`(get [1 2 3] 1)`
`([1 2 3] 1)`

try the above with `nil` and out-of-range index; a default behaviour can be attached for out-of-range situation

`(nth [] 9 :oops)`
`(get [] 9 :oops)`

Traverse left to right (or) right to left

`(seq [1 2 3])`
`(rseq [1 2 3])`

Update/addition of any one element with `assoc` (O(1) complexity)

`(assoc [1 2 3] 2 100)`
`(assoc [1 2 3] 3 100)`

#### Clojure Libraries

Machine Learning

1. [Incanter](http://incanter.org) is a R-like statistical computing environment
2. 

Big Data Processing

1. [Cascalog] for Batch processing
2. [Storm] for Realtime Data processing


Web Application

1. [om] for building Single Page Apps using Facebook React
2. ClojureScript 

