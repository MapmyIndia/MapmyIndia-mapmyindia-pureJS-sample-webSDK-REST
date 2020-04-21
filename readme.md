![MapmyIndia APIs](https://www.mapmyindia.com/api/img/mapmyindia-api.png)
# MapmyIndia Interactive Maps JS SDK & MapmyIndia RESTful APIs for Web in Pure JS

**Easy To Integrate Maps & Location APIs & SDKs For Web & Mobile Applications**

Powered with India's most comprehensive and robust mapping functionalities.
**Now Available**  for Srilanka, Nepal, Bhutan, Myanmar and Bangladesh

You can get your api key to be used in this document here: [https://www.mapmyindia.com/api/signup](https://www.mapmyindia.com/api/signup)

## Pre-requisites & Assumptions

1. Supported frameworks: The sample is prepared in Pure JS.
2. CORS to be ENABLED: You need to ask [API Support](mailto:apisupport@mapmyindia.com) to enable CORS for your credentials.
3. This sample is provided as a reference only and users can adjust and enhance it to suit their requirements.

## Creating project in Pure Javascript for MapmyIndia REST APIs & Interactive Map

*`Sample Example:`*

```html
    <html>
    <head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    // Interactive Map JS
    <script src="https://apis.mapmyindia.com/advancedmaps/v1/<Map_Key>/map_load?v=1.3"></script> 
    // Map Style 
    <style>
        #map{
            height: 70vh;
            width: 100%;
        }
    </style >
    </head>
    <body onload="Auth_token_generate();">
    <div id="map"></div><br> // div for map
    <button onclick="auto();">autosuggest</button>
    <button onclick="geocoding();">geocoding</button>
    <button onclick="rev_geocode();">rev_geocode</button>
    <button onclick="Many_to_Many_distance_matrix();">Many_to_Many_distance_matrix</button>
    <button onclick="distance_matrix();">distance_matrix</button>
    <!-- // logic -->
    <script>
    // Creating Map
    var map = new MapmyIndia.Map('map',
    {
        center: [28.04, 78.2],
        zoom: 12
    });

    var token;
```
#### Jsonp function
```js
    function jsonp(url, callback) {
    const callbackName = 'jsonp_callback_' + Math.round(100000 * Math.random());
    const script = document.createElement('script');
    script.src = url + (url.indexOf('?') >= 0 ? '&' : '?') + 'callback=' + callbackName;
    document.body.appendChild(script);
    window[callbackName] = function(data) {
    delete window[callbackName];
    document.body.removeChild( script );
    callback(data);
    };
    }
```
#### Generating Token

```js
    function Auth_token_generate(){
    $.ajax({
    data: {
            "grant_type": "client_credentials",
            "client_id": "<client_id>",
            "client_secret": "<client_secre>"
        },
    headers: {
                "Content-Type": "application/x-www-form-urlencoded",
            }, method: 'POST',
    crossDomain: true,
    async: true,
    url: 'https://outpost.mapmyindia.com/api/security/oauth/token',
    error: function (data) {
    console.log(data);
    alert('An error has occurred');
            },
            success : function (data) {
            token = data.access_token;
            }}
        );
    }
```
#### Call AutoSuggest API
```js
    function auto(){
            jsonp('https://atlas.mapmyindia.com/api/places/search/json?
            query=delhi&access_token='+token,
            function(data) {
            console.log(data);
            alert(JSON.stringify(data));
        });
    }
```
#### Call Geocoding API
```js
    function geocoding(){
        jsonp('https://atlas.mapmyindia.com/api/places/geocode?address=mapmyindia 237 okhla
        phase 3&access_token='+token,
        function(data) {
        console.log(data);
        alert(JSON.stringify(data));
        });
    }
```
#### Call Reverse Geocoding API
```js
    function rev_geocode(){
        jsonp('http://apis.mapmyindia.com/advancedmaps/v1/'+token+'/rev_geocode?lat=26.5645&lng=85.9914',
        function(data) {
        console.log(data);
        alert(JSON.stringify(data));
        });
    }
```
#### Call Many to Many Distance Matrix API
```js
    function Many_to_Many_distance_matrix(){
        jsonp('https://apis.mapmyindia.com/advancedmaps/v1/<Your_rest_api>/distance_matrix/driving/77.983936,28.255904;77.05993,28.487555;77.15993,28.587555;77.264997,28.554534?sources=0;1&destinations=2;3',
        function(data) {
        console.log(data);
        alert(JSON.stringify(data));
        });
    }
```
#### Call Distance Matrix API
```js
    function distance_matrix(){
        jsonp('https://apis.mapmyindia.com/advancedmaps/v1/<Your_rest_api>/distance_matrix/driving/90.33687,23.470314;90.379249,23.497178;90.497009,23.546286?rtype=1&region=bgd',
        function(data) {
        console.log(data);
        alert(JSON.stringify(data));
        });
    }
```
#### Call Routing API
```js
function route(){
            jsonp('https://apis.mapmyindia.com/advancedmaps/v1/<Key>/route_adv/driving/77.227434,28.610981;77.212021,28.616679;77.252021,28.616879;77.292021,28.616699?alternatives=true&&geometries=polyline&overview=full&exclude=&steps=true&region=ind', function(data) {
                console.log(data);
                pts1 = decode_path(data.routes[0].geometry);
                console.log(pts1);
                polyline = new MapmyIndia.Polyline({
                    map:map,
                    path: pts1,
                    strokeColor: "red",
                    strokeOpacity:1,
                    strokeWeight: 2,
                    lineGap:0,
                    fitbounds:true
                });
            });
        }
```
#### Route Path Decoder
```js
function decode_path(encoded) {
            if (encoded != 'undefined') {
                var pts = [];
                var index = 0, len = encoded.length;
                var lat = 0, lng = 0;
                while (index < len) {
                    var b, shift = 0, result = 0;
                    do {
                        b = encoded.charAt(index++).charCodeAt(0) - 63;
                        result |= (b & 0x1f) << shift;
                        shift += 5;
                    } while (b >= 0x20);

                    var dlat = ((result & 1) != 0 ? ~(result >> 1) : (result >> 1));
                    lat += dlat;
                    shift = 0;
                    result = 0;
                    do {
                        b = encoded.charAt(index++).charCodeAt(0) - 63;
                        result |= (b & 0x1f) << shift;
                        shift += 5;
                    } while (b >= 0x20);
                    var dlng = ((result & 1) != 0 ? ~(result >> 1) : (result >> 1));
                    lng += dlng;
                    pts.push([lat / 1E5, lng / 1E5]);
                }
                return pts;
            } else {
                return '';
            }
        }
```
```html
    </script>
    </body>
    </html>
```
That's it folks !!!

For any queries and support, please contact: 

![Email](https://www.google.com/a/cpanel/mapmyindia.co.in/images/logo.gif?service=google_gsuite)
 
Email us at [apisupport@mapmyindia.com](mailto:apisupport@mapmyindia.com)

![](https://www.mapmyindia.com/api/img/icons/stack-overflow.png)
[Stack Overflow](https://stackoverflow.com/questions/tagged/mapmyindia-api)
Ask a question under the mapmyindia-api

![](https://www.mapmyindia.com/api/img/icons/support.png)
[Support](https://www.mapmyindia.com/api/index.php#f_cont)
Need support? contact us!

![](https://www.mapmyindia.com/api/img/icons/blog.png)
[Blog](http://www.mapmyindia.com/blog/)
Read about the latest updates & customer stories


> © Copyright 2019. CE Info Systems Pvt. Ltd. All Rights Reserved. | [Terms & Conditions](http://www.mapmyindia.com/api/terms-&-conditions)
