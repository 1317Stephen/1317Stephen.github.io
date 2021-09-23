---
# layout: posts
title: "Swift Basic: Only in Swift"
date: 2021-09-09 16:18:40 +0900
categories:
  - Swift
tags:
  - Swift
  - iOS
excerpt: ""
toc: true
---

## Underscore: _

> "ignore this"   
> Underscroe is used to define the parameter is not named.   
> In for-loop, "no index, just repeat."   

### reference
  - [What is _: in Swift telling me?](https://stackoverflow.com/questions/30876068/what-is-in-swift-telling-me)
  - [Swift underscore(_)](https://medium.com/@codenamehong/swift-underscore-90dcbec5072f)
  - [Why do I need underscores in swift?](https://stackoverflow.com/questions/39627106/why-do-i-need-underscores-in-swift)


## Struct vs Class
: In Swift, `struct` and `class` have a lot in common.  
- `init()`
- extension
- protocol
- subscript
- property & method

Then, when should I use `struct` or `class`? 
I learned this from the example below.

- In my project [Somking Area v1.0.0](https://1317stephen.github.io/swift/project/iOS-Application/), there are 2 variables and 1 function related to loaction.
  - `Address` struct: has the information about locality and administrative area name.
  - `CLLocationCoordinate2D`: store the latitude & longitude of the location.
  - `getCoordinateToAddress(coordinate: CLLocationCoordinate2D)`: get locality and adminitrative area name from the coordinate.

  Therefore, I decided to combine the above three things as a single data structure. Location information is the `Model` in the project, so I tried to implement these as a `struct`.

{% highlight swift %}
{% raw %}
struct Location {
    internal init(coordinate: CLLocationCoordinate2D, administrativeArea: String, locality: String) {
        self.coordinate = coordinate
        self.administrativeArea = administrativeArea
        self.locality = locality
    }
    init(coordinate: CLLocationCoordinate2D) {
        self.coordinate = coordinate
        self.administrativeArea = ""
        self.locality = ""
        
        exchangeCoordinateToAddress(coordinate: coordinate) {
          (address) in
            self.administrativeArea = address.administrativeArea
            self.locality = address.locality   
        }
    }

    var coordinate: CLLocationCoordinate2D
    var administrativeArea: String
    var locality: String
    
    func exchangeCoordinateToAddress(coordinate: CLLocationCoordinate2D, completion: @escaping ((Address)->()))  {
      ......
    }
}
{% endraw %}
{% endhighlight %}

  Above code did not work([Swift Error Note](https://1317stephen.github.io/swift/Swift-Error-Note/#self-parameter-inside-struct-init)) because the `struct` is the value type, so `exchangeCoordinateToAddress()` method called in `init()` did not work.

  So, `class` should be used in these cases,
  - When you want to change the value. (call by reference)
  - inheritance
  - type casting

  Most of other cases, there would be no problem to use `struct`.

### reference
- [Struct, Class](https://medium.com/@jgj455/오늘의-swift-상식-struct-class-60fa5fd2218d)
