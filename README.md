# Weather App - Hands on Session
"Hands-on Session on Flutter" organised by [GirlScript Pathankot](https://www.linkedin.com/in/ACoAADD7RX0B1g8Jt_7VqXogTuLaAMnmFYBL1II)  📱  

## Getting Started
If you haven't worked with Flutter before, consider reading my article on 'Writing your first app in Flutter.' published on Medium [here](https://adityathakurxd.medium.com/writing-your-first-app-in-flutter-8e5a1ec85b70).

To get started, Create a new Flutter project:

    flutter create weatherapp
A `weatherapp` directory is created, containing Flutter’s starter app.

Navigate to this directory:

    cd weatherapp
To launch the app in the Simulator/Emulator, ensure that it is running and enter:

    flutter run
Replace the default code with the code in this repository.
```dart
import 'package:flutter/material.dart';  
  
void main() {  
  runApp(MyApp());  
}  
  
class MyApp extends StatelessWidget {  
  // This widget is the root of your application.  
  @override  
  Widget build(BuildContext context) {  
    return MaterialApp(  
      title: 'Weather App',  
      home: HomePage(),  
    );  
  }  
}
```

## 1. Getting the location of the Device
**Objective:** Create a simple UI with a RaisedButton which when pressed, prints the location of the Device in the Terminal.

```dart
import 'package:flutter/material.dart';  
  
class HomePage extends StatefulWidget {  
  @override  
  _HomePageState createState() => _HomePageState();  
}  
  
class _HomePageState extends State<HomePage> {  
  @override  
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Center(  
        child: RaisedButton(  
          onPressed: (){
          getLocation();
          },  
          child: Text(  
            'Get Location'  
             ),  
        ),  
      ),  
    );  
  }  
}
```
Create a Function getLocation() which is called by onPressed()

 - We use the [geolocator](https://pub.dev/packages/geolocator) Flutter package.  
 - Add the permissions for both Android and iOS.

```dart
void getLocation() async{  
  Position position = await Geolocator.getCurrentPosition(desiredAccuracy: LocationAccuracy.low);  
  print(position.latitude);  
  print(position.longitude);  
}
```
Since, we are using a button to print the location. It isn't very cool :(
So, we'll tap into the Widget lifecycles to getLocation each time our app runs.

```dart
@override  
void initState() {  
  super.initState();  
  getLocation();  
}
```
**Dart Exception Handling**: What if the User denies permission?

Wrap the funtion in a try-catch block and print any exceptions.
```dart
void getLocation() async{  
  try{  
    Position position = await Geolocator.getCurrentPosition(desiredAccuracy: LocationAccuracy.low);  
    print(position.latitude);  
    print(position.longitude);  
  }  
  catch (e) {  
    print(e);  
  }  
}
```
Create local variables and save the Latitude and Longitude to them.

## 2. Use the Location to get Weather Data

We'll use the [**OpenWeather API**](https://openweathermap.org/api). 

 - Create an account to get your API Key.
 - We'll need to make API call from with the app.

**Networking using [HTTP Package](https://pub.dev/packages/http):**

 - Add the package to `pubspec.yaml`
 - Import it to the file.
```dart
import 'package:http/http.dart' as http;  
import 'dart:convert' as convert;

void getLocation() async {  
  Position position = await Geolocator.getCurrentPosition(desiredAccuracy: LocationAccuracy.low);  
  latitude = position.latitude;  
  longitude = position.longitude;  
  await getData();  
}  
  
Future <void> getData() async{  
  http.Response response = await http.get('http://api.openweathermap.org/data/2.5/weather?lat=35&lon=139&appid=$apiKey');  
  if(response.statusCode == 200) {  
    String data = response.body;  
  
    var condition = convert.jsonDecode(data)['weather'][0]['id'];  
	var temperature = weatherData['main']['temp'];  
	var cityName = weatherData['name'];  
  
	 print(condition);  
	 print(cityName);  
	 print(temperature); 
  }  
  else{  
    print(response.statusCode);  
  }  
}  
  
@override  
void initState()  {  
  super.initState();  
  getLocation();  
}
```

 - Decode the data to print specific in console 
 - Call this getData() function in getLocation()

**Error:** 
HTTP connection error [FIX]: https://stackoverflow.com/a/65578828/11690853  
https://flutter.dev/docs/release/breaking-changes/network-policy-ios-android

*So far, we have been able to print the Weather Data to console.*

## 3. Using Data in UI
Instead of showing up the weather in the same file, Lets create a new WeatherScreen().
While we push from the HomePage to WeatherScreen we'll add a spinner. Using [package](https://pub.dev/packages/flutter_spinkit).

Add `Navigator.push(context, MaterialPageRoute(builder: (context) => WeatherScreen())); `
to the getData() method, so the new screen is pushed in when data is loaded.

**Use the spinkit in the Scaffold:**
```dart
@override  
Widget build(BuildContext context) {  
  return Scaffold(  
    backgroundColor: Colors.blue,  
    body: Center(  
      child: SpinKitSpinningCircle(  
        color: Colors.white,  
        size: 50.0,  
      ),  
    ),  
  );  
}
```
**Create a constructor to pass over data and use in the UI.**
```dart
import 'package:flutter/material.dart';  
  
class WeatherScreen extends StatefulWidget {  
  
  String cityName;  
  int temp;  
  WeatherScreen(this.temp,this.cityName);  
  @override  
  _WeatherScreenState createState() => _WeatherScreenState();  
}  
  
class _WeatherScreenState extends State<WeatherScreen> {  
  @override  
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Column(  
        crossAxisAlignment: CrossAxisAlignment.center,  
        mainAxisAlignment: MainAxisAlignment.center,  
        children: [  
          Container(width: MediaQuery.of(context).size.width,),  
          Text(  
            '${widget.cityName}',  
            style: TextStyle(  
              fontSize: 40.0,  
              color: Colors.white,  
            ),  
          ),  
          SizedBox(height: 20.0,)  
          ,  
          Text('${widget.temp}',style: TextStyle(fontSize: 60.0,color: Colors.white,),)  
        ],  
      ),  
    );  
  }  
}
```
Instead of sending over temp and cityName from Navigation lets get WeatherData and **create a UpdateUI method** that updates the interface.
```dart
class WeatherScreen extends StatefulWidget {  
  
  final weatherData;  
  WeatherScreen(this.weatherData);  
  @override  
  _WeatherScreenState createState() => _WeatherScreenState();  
}  
  
class _WeatherScreenState extends State<WeatherScreen> {  
  int temp;  
  int condition;  
  String cityName;  
  
  @override  
  void initState() {  
    // TODO: implement initState  
  super.initState();  
    updateUI(widget.weatherData);  
  }  
  
void updateUI(dynamic weatherData) {  
  setState(() {  
    var condition = weatherData['weather'][0]['id'];  
    double temperature = weatherData['main']['temp'];  
    temp = temperature.toInt();  
    cityName = weatherData['name'];  
  });  
}
  //build method (same as above)
}
```

**Challenge #Building UI with Weather Model**

 - WeatherModel Class
 - UpdateUI Method

## 4. Search a City and get Data
We'll create a Search Screen that'll take in the city name from the user and display Weather.

**Lets first create a App Bar with a button to navigate to this Screen.**
```dart
appBar: AppBar(  
  title: Text("Weather App"),  
  actions: [  
    IconButton(  
      icon: Icon(Icons.location_city),  
      onPressed: () {  
        Navigator.push(context, MaterialPageRoute(builder: (context) => SearchCity()));  
      },  
    ),  
  ],  
),
```

**Create a TextField in Search Screen and a button that pops a value.**
```dart
import 'package:flutter/material.dart';  
  
class SearchCity extends StatefulWidget {  
  @override  
  _SearchCityState createState() => _SearchCityState();  
}  
  
class _SearchCityState extends State<SearchCity> {  
  String typedName;  
  @override  
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Column(  
        mainAxisAlignment: MainAxisAlignment.center,  
        crossAxisAlignment: CrossAxisAlignment.center,  
        children: [  
          Container(  
            width: MediaQuery.of(context).size.width,  
          ),  
          TextField(  
            decoration: InputDecoration(  
              filled: true,  
              hintText: 'Enter a city to search'  
			  ),  
            onChanged: (value){  
              typedName = value;  
            },  
          ),  
          SizedBox(  
            height: 20.0,  
          ),  
          RaisedButton(  
            onPressed: (){  
              Navigator.pop(context,typedName);  
            },  
            child: Text('Get Weather'),  
          ),  
        ],  
      ),  
    );  
  }  
}
```

**Update the appBar code to get weather of entered city.**
```dart
appBar: AppBar(  
  title: Text("Weather App"),  
  actions: [  
    IconButton(  
      icon: Icon(Icons.location_city),  
      onPressed: () async {  
        var searchName = await Navigator.push(context,  
            MaterialPageRoute(builder: (context) => SearchCity()));  
        print(searchName);  
        if (searchName != null) {  
          var url =  
              'https://api.openweathermap.org/data/2.5/weather?q=$searchName&appid=$apiKey';  
          http.Response response = await http.get(url);  
          print('Search Data got');  
          if (response.statusCode == 200) {  
            print('Search Data decode');  
            String data = response.body;  
            var weatherData = convert.jsonDecode(data);  
            updateUI(weatherData);  
          }  
        }  
      },  
    ),  
  ],  
),
```
That is it!
