---
# layout: posts
title: "[Swift Project] Smoking/Nonsmoking Area"
date: 2021-09-05 15:28:33 +0900
categories:
  - Swift
  - Project
tags:
  - iOS
  - Swift
  - Public API
  - API

excerpt: "Develop the iOS Application(Swift) that mark the Smoking/Non-smoking Areas in the map."
toc: true


frontend-plan-draft:
  - url: /assets/images/fronted-draft.png
    image_path: assets/images/fronted-draft.png
    alt: "Frontend plan draft"

v1.0.0_screenshots:
  - url: /assets/images/Home_page.png
    image_path: assets/images/Home_page.png
    alt: "Home page"
  - url: /assets/images/Home_page_with_sidebar_open.png
    image_path: assets/images/Home_page_with_sidebar_open.png
    alt: "Home page with sidebar"
  - url: /assets/images/Post_page.png
    image_path: assets/images/Post_page.png
    alt: "Post page"
  - url: /assets/images/Article_page.png
    image_path: assets/images/Article_page.png
    alt: "Article page"

storyboard:
  - url: /assets/images/smokingArea/smokingArea_mainstoryboard_with_constraints_v1.png
    image_path: assets/images/smokingArea/smokingArea_mainstoryboard_with_constraints_v1.png
    alt: "storyboard"
---
# 1. Requirements
## Project Objectives
Produce iOS apps that display 🚬smoking areas for smokers and 🚭 non-smoking areas for non-smokers in South Korea.  

## Specification
### version 1.0.0
  - Mark smoking areas in the map. (YongSan-Gu)
  - Mark non-smoking areas in the map. 
  - The map screen can be zoomed.
  - Can move to the current position on the map screen.

### Next version
  - Precise way to mark 'near' positioin in non-smoking area. (current: same administrative & locality)
  - Mark other localities' smoking area API. (current: smoking area 1, non-smoking area: all South Korea except API error area.)
  - Show information of the selected area.
  - Optional: people density 

<br />
<br />


# 2. Structure/Function
## version 1.0.0
: one view, one controller, one model.

(1) View(main storyboard)
- Apple MapView
- two switch(smoking area, nonsmoking area on/off) with each label → stack view
- stepper(zoom)
- button(current location)

{% include gallery id="storyboard" caption="Smoking Area v1.0.0 storyboard with constraints" %}


(2) Main Controller

- `moveToCurrentLocation()`: move to current location.
- `getCoordinateToAddress()`: transfer coordinate(latitude & longitude) to Korean address.
- `addAnnotations()`: add/remove multiple Annotations(markers) in the Apple MapView.
- `mapScaleStepper()`: zoom in/out.
- `callJsonApi()`: call public API in JSON format.
- `callXmlApi()`: get public API in XML format.
- `smokingAreaSwitchAction()`: smoking area annotations on/off.
- `nonsmokingAreaSwitchAction()`: nonsmoking area annotations on/off.


(3) API Model
- nonsmoking area API model
- smoking area API model
- address model

## Next version
- (main map) MVVC + (additional swipe window) MVC


<br />
<br />


# 3. Implementation: Focus on the Problem-Solving
: Among the implementations, parts that take a long time or still have a room for improvement are summarized with the source.

## version 1.0.0
In version 1.0.0, there are main 4 features.

➀ Move to current location.   
➁ Zoom in/out map.   
➂ Display the non-smoking area data near the user on the map.   
➃ Display the smoking area data of Yonsan-Gu on the map.   
   
The codes below are the main functions needed to implement these four features.

### Main problems
- call XML/JSON API
- synchronize with/without loop

### Functions
#### ➀ Move to current locations.
- basic settings in `viewDidLoad()`
{% highlight swift %}
{% raw %}
locationManager = CLLocationManager()
locationManager.delegate = self

locationManager.requestWhenInUseAuthorization()

locationManager.desiredAccuracy = kCLLocationAccuracyBest
locationManager.startUpdatingLocation()

moveToCurrentLocation()
currentDisplayCenter = mapView.centerCoordinate

mapView.showsUserLocation = true
self.mapView.delegate = self
{% endraw %}
{% endhighlight %}


- `moveToCurrentLocation()`
{% highlight swift %}
{% raw %}
private func moveToCurrentLocation() {
    if let userLocation = locationManager.location?.coordinate {
        let viewRegion = MKCoordinateRegion(center: userLocation, latitudinalMeters: 500, longitudinalMeters: 500)
        mapView.setRegion(viewRegion, animated: false)
    }
}
{% endraw %}
{% endhighlight %}


#### ➁ Zoom map with Stepper
- basic settings in `viewDidLoad()`
{% highlight swift %}
{% raw %}
// zoom in/out stepper setup
mapScaleStepperOulet.wraps = true
mapScaleStepperOulet.autorepeat = true
mapScaleStepperOulet.minimumValue = -1000
mapScaleStepperOulet.maximumValue = 1000
{% endraw %}
{% endhighlight %}


- `mapScaleStepper(_ sender: UIStepper)`
{% highlight swift %}
{% raw %}
@IBAction func mapScaleStepper(_ sender: UIStepper) {
  var region = mapView.region
  
  if sender.value > mapScaleValue {
      region.span.latitudeDelta /= 2.0
      region.span.longitudeDelta /= 2.0
      mapView.setRegion(region, animated: true)
  } else  {
      region.span.latitudeDelta *= 2.0
      region.span.longitudeDelta *= 2.0
      mapView.setRegion(region, animated: true)
      
  }
  mapScaleValue = sender.value
}
{% endraw %}
{% endhighlight %}


#### ➂ Add/Remove multiple Annotations in the map
: `Area` is the structure implemented in the `Model`. 

- Add
{% highlight swift %}
{% raw %}
private func addAnnotations(areas: [Area]){
    let annotations = areas.map { area -> MKAnnotation in
        let annotation = MKPointAnnotation()
        annotation.title = area.placeName
        annotation.coordinate = area.coordinate
        annotation.subtitle = area.placeSubtitle.rawValue
        
        return annotation
    }
    mapView.addAnnotations(annotations)
}
{% endraw %}
{% endhighlight %}

- Remove
{% highlight swift %}
{% raw %}
@IBAction func nonsmokingAreaSwitchAction(_ sender: UISwitch) {
    if sender.isOn {
        callXmlApi(baseUrl: URLs.nonsmokingAreaBaseURL.rawValue, endPage: 4) { nonsmokingAreas in
            self.addAnnotations(areas: nonsmokingAreas)
        }
    } else {
        // Remove the anotations that have 'Nonsmoking' subtitle.
        for annotation in self.mapView.annotations {
            if let subtitle = annotation.subtitle, subtitle == Area.PlaceSubtitle.Nonsmoking.rawValue {
                mapView.removeAnnotation(annotation)
            }
        }
    }
}
{% endraw %}
{% endhighlight %}


#### ➃ Exchange Latitude & Longitude to Address
: This function is used in the `nonsmokingAreaSwitchAction()` function with the flow shown below.
 > Get current location's latitude & longitude    
 > → exchange latitude & longitude to Korean Address    
 > → Extract the specific factor (ex. `administrativeArea`, `locality` of Address)     
 > → get current location's non-smoking areas' API using the factor    

{% highlight swift %}
{% raw %}
private func getCoordinateToAddress(coordinate: CLLocationCoordinate2D) {
    let coordinateLocation = CLLocation(latitude: coordinate.latitude, longitude: coordinate.longitude)
    let geocoder = CLGeocoder()
    let locale = Locale(identifier: "Ko-kr")
    
    geocoder.reverseGeocodeLocation(coordinateLocation, preferredLocale: locale, completionHandler: {(placemarks, error) in
        if let fullAddress: [CLPlacemark] = placemarks {
            if let locality = fullAddress.last?.locality, let administrativeArea = fullAddress.last?.administrativeArea {
                self.currentDisplayAddress = Address(administrativeArea: administrativeArea, locality: locality)
            }
        }
    })
}
{% endraw %}
{% endhighlight %}


#### ➄ API: XML format
: In this project, the `Alamofire`, `CFRunLoop` and `completion` is used to handle the XML type multiple API in the loop.

Calling API with `NSXMLParserDelegate`, handling its data and other ways to synchronize it will be delt with in another post.

{% highlight swift %}
{% raw %}
private func callXmlApi(baseUrl: String, endPage: Int, completion: @escaping ([Area])->()){
  var nonsmokingAreas = [Area]()
  var isEndPage = false
  let runLoop = CFRunLoopGetCurrent()

  for pageNo in 0...endPage {
    if isEndPage { break }

    Alamofire.request(url, method: .get).validate().responseString { response in
      CFRunLoopStop(runLoop)
      switch response.result {
      case .success:
        if let responseData = response.data {
          let responseDataEncoding = NSString(data: responseData, encoding: String.Encoding.utf8.rawValue)
          let xml = try! XML.parse(String(responseDataEncoding!))
          
          if let endPageCode = xml["response"]["header"]["code"].text, let endPageCodeName = xml["response"]["header"]["codeNm"].text {
              isEndPage = true
              print("EndCode: \(endPageCode), EndCodeName: \(endPageCodeName)", isEndPage)
              break
          }
          
          for element in xml["response"]["body"]["items"]["item"]{
              if let placeName = element["prhsmkNm"].text, let latitude = element["latitude"].text, let longitude = element["longitude"].text {
                  let latitudeDoube: Double = Double(latitude)!
                  let longitudeDoube: Double = Double(longitude)!

                  nonsmokingAreas.append(Area(placeSubtitle: Area.PlaceSubtitle.Nonsmoking , placeName: placeName, coordinate: CLLocationCoordinate2D(latitude: latitudeDoube, longitude: longitudeDoube)))
              }
          }
      }

      case .failure(_):
      print("fail to get response from \(url)")
      }
    }
    CFRunLoopRun()
  }
  completion(nonsmokingAreas)
}
{% endraw %}
{% endhighlight %}


#### ➅ API: JSON format
{% highlight swift %}
{% raw %}
private func callJsonApi(urlStr: String, completion: @escaping ([Area])->()) {
    var smokingAreas = [Area]()
    guard let url = URL(string: urlStr) else {  return  }
    let session = URLSession(configuration: URLSessionConfiguration.default)
    let task = session.dataTask(with: url, completionHandler: {(data, response, error) in
        guard error == nil else {
            print(" > Error: \(error!)")
            return
        }
        guard let resData = data else { return }
        do {
            let dataDecode = try JSONDecoder().decode(SmokingArea.self, from: resData)
            for row in dataDecode.data {
                if let latitudeDouble: Double = Double(row.latitude),let longitudeDouble: Double = Double(row.longitude) {
                    smokingAreas.append(Area(placeSubtitle: Area.PlaceSubtitle.Smoking, placeName: row.installationLocation, coordinate: CLLocationCoordinate2D(latitude: latitudeDouble, longitude: longitudeDouble)))
                }   
            }           
            completion(smokingAreas)
        } catch let error {
            print("JSONDecoder Error: \(error)")
        }
    })
    task.resume()
}
{% endraw %}
{% endhighlight %}



<!-- #### ➆
{% highlight swift %}
{% raw %}
{% endraw %}
{% endhighlight %}


➀➁➂➃➄➅➆➇➈➉

#### -->


### Further Improvement
#### Refactoring
- guard
- error message (current: console)
- Other ways to synchronize the API function.

#### Calling multiple APIs FAST without CPU, Momory overloading.
: In version 1.0.0, the application is quite slow especially when calling API.



<!-- ## Problems
### 1) Calling API and extract Data.
Since this is the first time of using public API, I faced with problem that how to get (public) API with `.xml` or `json` and use it.

#### XML Format API
: Nonsmoking area API
- `NSXMLParserDelegate`
First time, I tried to call API with just `XMLParserDelegate` in Swift and `parse()` function. But this is not effective way in my project, so I removed `parse()` functions and not used this. Below are the webpages I referred to when I used `XMLParserDelegate`.

[XML parsing: This page has full coding and clear description.]: https://rhammer.tistory.com/131


- `Alamofire`

[Install Alamofire Library]: https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=hoon299&logNo=221589855345

[Cocoapods Repository Update Error]: https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=cicron&logNo=220887524542

[Podfile usage and CocoaPods install]: https://zeddios.tistory.com/25


#### JSON Format API
: Smoking Area in Yong-San Gu send API in JSON format. 



#### Synchronize the Asynchronous function
- Without Loop: Completion



- call multiple Open APIs with Loop: CFRunLoop
: The nonsmoking area Open API has the whole country 


### 2) Calling multiple API pages(upto 900 APIs with 1000 rows) FAST without CPU, Momory overloading.

The URL of Open API consists approximately of th following:
 `http://something_api_name&pageNo=800&numOfRows=100&other_parameters`

The entire data has about more than 800 pages with 100rows. 
There are mainly 

- Calling All APIs in one time when the button pressed.
First, I tried to call the entire data (about 800 APIs in one time) when ths UISwitch turned on. 
This led to a catastrophe for the program. Before a quarter of the entire API could be loaded, the application stopped running!

- Other ways to call APIs
Then I tried to call the APIs with more rows(aboutt 500 rows in one API).
Of course, this makes situation more worse. 

I realized this is the stupid thing, so I treid to think about another options.
Then, I thought about how the map applications in maket show location information. The best example was the residual vaccine reservation service.
Of course, this service was not bringing in information from all places at once. The service was showing only nearby information about location selected by user. Shrinking the map showed more information, but there was a limit. The point is, they did NOT display the all the informations at one time.

- Calling APIs only near the region selected by user.
So I finally tried to call the API more efficient way!
There are mainly 4 processes that I should additionaly develop.

➀ Get current/selected(moved) location → ➁ Get the location information(city name...) → ➂ Call the APIs nearby → ➃ Display the APIs' information

What had to be considered in the process was the definition of 'near'.
The simplest thing is the circle of the pinpoint, but it is impossible to call the APIS with range of latitude and longitude.
The next thing is to 




- Should I call the entire data in the first time when button pressed?
  - This is way too heavy. (Calling the 1/4 of the entire data also takes too long... and don't want to recommended)




http://api.data.go.kr/openapi/tn_pubr_public_prhsmk_zn_api?serviceKey=54PZHQOEMuoKu%2F2qc%2FePaSammRtXkjShRY1JS4dLgXmCr1LOoLA3keN2CApbO65Uj64E6O3oxWkwYypedHf8sA%3D%3D&pageNo=0&numOfRows=100&ctprvnNm=서울특별시&type=xml


### 3) Other problems.
- Settings
➀ HTTP allow for API
[Allow HTTP in iOS]: https://slobell.com/blogs/54

  not helpful bul readable thigs:
  [iOS 9 / OS X 10.11 App Network Security Policy]: http://seorenn.blogspot.com/2015/09/ios-9-os-x-1011.html
  [ATS Errors]: https://y8k.me/article/847

➀➁➂➃➄➅➆➇➈➉


- Show multiple `Annotations` in Apple MapView 
: non-smoking area datas in `areas`struct Array → mapping to annotation → add annotations in mapView. (For security purpose, Variable names and details are slightly different from actual code developed.)

{% highlight swift %}
let annotations  = self.areas.map {
    area -> MKAnnotation in
    let annotation = MKPointAnnotation()
    annotation.title = area.name
    annotation.coordinate = area.coordinate
    annotation.subtitle = AreaSubtitle.nonsmoking
    ...
    return annotation
}
self.map.addAnnotations(annotations)
self.map.showAnnotations(annotations, animated: false)
{% endhighlight %}



(reference: 
Similar problem: 
https://stackoverflow.com/questions/51795862/how-to-add-annotations-in-map-view-only-show-one-pin

Fix: 
https://stackoverflow.com/questions/36666968/how-to-set-up-array-for-multi-annotations-with-swift/36668011#36668011) -->

 
<br />
<br />

# 4. Result
## version 1.0.0
<video height="900" controls="controls">
  <source src="/assets/images/smokingArea/smokingArea_v1_video.mp4" type="video/mp4">
</video>
- period: Aug 9, 2021 ~ Sep 5, 2021



[application.properties file]: /project/Web-Application-SpringBoot-Impementation/#applicationproperties
[Config package implementation]: /project/Web-Application-SpringBoot-Impementation/#config-package

[ApiResponse package implementation]: /project/Web-Application-SpringBoot-Impementation/#apiresponse-package


[Main API packages implementation]: /project/Web-Application-SpringBoot-Impementation/#main-api-packages