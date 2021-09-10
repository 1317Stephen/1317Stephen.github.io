---
# layout: posts
title: "Swift Basic"
date: 2021-07-09 09:32:40 +0900
categories:
  - Swift
tags:
  - Swift
  - iOS
excerpt: "Swift Data Structure, Operator, Control Flow."
toc: true
---

## 1. Swift Collection Data Structure

### Array
- Initialization
  {% highlight swift %}
  var arr = [Int]()
  var arr2 : [Int] = []
  var arr3 : Array<Int> = []
 {% endhighlight %}

- Get size
{% highlight swift %}
let n = arr.count
{% endhighlight %}


- Access
{% highlight swift %}
arr[arr.startIndex]
arr.first

arr[arr.endIndex-1]
arr.last
{% endhighlight %}


- Append/Insert
 {% highlight swift %}
  arr.append("something")
  arr.insert("something insert", at:1)
  {% endhighlight %}

- Remove/Pop/Drop
{% highlight swift %}
arr.removeFirst()

arr.popLast()
arr.removeLast()

arr.remove(at:3)

arr.removeAll()

let getArrExceptFirst = arr.dropFirst()
let getArrExceptLast = arr.dropLast()
{% endhighlight %}


- Modify
{% highlight swift %}
arr[1] = "Test"
arr[0..1] = ["First", "Second"]
arr[0..1] = ["First", "Second", "Third"]
{% endhighlight %}

- Sort/Sorted
{% highlight swift %}
arr.sort()
arr.sort(by: >)

let sortedArr = arr.sorted(by:<)
let sortedArrDescending = arr.sorted(by:>)
{% endhighlight %}

- Min/Max
{% highlight swift %}
arr.min()
arr.max()
{% endhighlight %}

- Reverse
{% highlight swift %}
arr.reverse()
{% endhighlight %}

### String/Character
 - Access String's Character
{% highlight swift %}
let str = "Swift String Test"
str[str.startIndex]
str[str.index(before: endIndex)]
str[str.index(str.startIndex, offsetBy: 4)]
str[str.index(str.endIndex, offsetBy: -2)]
{% endhighlight %}

- Append a Character to a String
  - Convert a Character to a String
{% highlight swift %}
var newStr = str + [aCharacter]
{% endhighlight %}

  - Using `insert()`
{% highlight swift %}
str.insert(m[i] ?? "-", at: str.endIndex)
{% endhighlight %}

### Dictionary
- Initialization
{% highlight swift %}
var dic = [Integer: String]()
{% endhighlight %}

- Sort
{% highlight swift %}
let sortedByKey = dic.keys.sorted(by:<)
let sortedByVal = dic.values.sorted(by:<)
let sortedByAll = dic.sorted(by:<)
{% endhighlight %}

: The two above return an Array. So, if you want to access the values of `sortedByKey`, try this
{% highlight swift %}
for key in sortedByKey {
  print(dic[key])
}
{% endhighlight %}

## 2. Control Flow
### For
 {% highlight swift %}
  for i in arr {}
  for i in 0...n {}
  for i in 0..<n {}
  for i in (0...n).reversed() {}
  for i in (0..<n>).reversed() {}
  for i in stride(from: n, to: 0, by: -2) {}
  {% endhighlight %}

## 3. Operator
### Unsupported Operator
- `++`, `--`
{% highlight swift %}
someInt++       //Not working
someInt += 1
{% endhighlight %}

- `&&`
{% highlight swift %}
if someInt>0 && someInt<10 {} //Not working
if someInt>0, someInt<10 {}
{% endhighlight %}


## 4. Optional
### Converting Optional
- Optional Binding
{% highlight swift %}
var something: String? = "Hello!"
if let val = something {
  print("This is not nill.")
}
{% endhighlight %}


- Optional Chaining (+ Optional Binding)
{% highlight swift %}
var dic = [Integer: someClass]
if let val = dic[keyValue]?.someProperty {
  print("This is not nil.")
}
{% endhighlight %}

- Forced Unwrapping
: This is usually used when class initialization or implicitly unwrapped optionals.
{% highlight swift %}
var button: UIButton!
{% endhighlight %}

- Nil-Coalescing Operator
{% highlight swift %}
let intLet = 10 + somethingOptional ?? 0
{% endhighlight %}


{% highlight swift %}

{% endhighlight %}

 
## Referece
- Data Structure
  - [Swift array blog][swift array blog]
  - [Swift for blog][swift for blog]
  - [Swift stride blog][swift stride blog]
  - [Swift data structure][swift data structure]
  - [Swift3 Array Usage][Swift3 Array Usage]
  - [Swift String and Characters][Swift String and Characters]
  - [How to append a character to a string in Swift?][How to append a character to a string in Swift?]
  - [Dictionary Sort][Dictionary Sort]
  - [Sort Dictionary by Key(Example)][Sort Dictionary by Key(Example)]

- Optional
  - [Warning Converting Optional to String][Warning Converting Optional to String]
  - [What is Optional][What is Optional]

[swift array blog]: https://zeddios.tistory.com/114
[swift for blog]: https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=hjleesm&logNo=221222952341
[swift stride blog]: https://zeddios.tistory.com/73
[swift data structure]: https://velog.io/@ssionii/Swift-%EC%8A%A4%EC%9C%84%ED%94%84%ED%8A%B8-%EA%B8%B0%EC%B4%88-%EB%AC%B8%EB%B2%95-1-%EC%9E%90%EB%A3%8C%ED%98%95
[Swift3 Array Usage]: https://zeddios.tistory.com/116

[Swift String and Characters]: http://minsone.github.io/mac/ios/swift-string-and-characters-summary
[How to append a character to a string in Swift?]: https://stackoverflow.com/questions/25457615/how-to-append-a-character-to-a-string-in-swift
[Dictionary Sort]: https://zeddios.tistory.com/14
[Sort Dictionary by Key(Example)]: https://fomaios.tistory.com/entry/Swift-%EB%94%95%EC%85%94%EB%84%88%EB%A6%AC-%EB%B0%B8%EB%A5%98%EA%B0%92%EC%9C%BC%EB%A1%9C-%ED%82%A4%EA%B0%92-%EC%88%9C%EC%84%9C%EB%8C%80%EB%A1%9C-%EC%A0%95%EB%A0%AC%ED%95%98%EA%B8%B0Sort-Dictionary-keys-by-values

[Warning Converting Optional to String]: https://useyourloaf.com/blog/warning-converting-optional-to-string/
[What is Optional]: https://zeddios.tistory.com/16