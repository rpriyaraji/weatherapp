# Weather App - Hands on Session
"Hands-on Session on Flutter" organised by [GirlScript Pathankot](https://www.linkedin.com/in/ACoAADD7RX0B1g8Jt_7VqXogTuLaAMnmFYBL1II)  📱  
  
Speaker for the event: Mr. [Aditya Thakur](https://www.linkedin.com/in/ACoAACNFGvgBncAPhbilcY5Y2MmUNr0X07NN1Ds) .🖥️ He is a  
⚪Flutter developer  
⚪UI/UX designer  
⚪AI/ML Enthusiast  
He also has a [YouTube channel](https://www.youtube.com/channel/UChCAJNpMwoEUYCsE_eSyU4w). ✨  

By the end of it, we'll build a weather app live 🌦️ using Flutter. 
What you'll learn:  
📌Get GPS Location of the phone  
📌Network API Calls from Internet  
📌Futures and Asynchronous Dart Programming  
📌Weather of our location or Search a City.  
  
🤩E-Certificates will be provided to all the attendees.

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
import 'dart:convert'
import 'package:http/http.dart' as http;

void getData() async{  
  var url ='http://api.openweathermap.org/data/2.5/weather?lat=$latitude&lon=$longitude&appid=$apiKey';  
  http.Response response = await http.get(url);  
  if (response.statusCode == 200) {  
    String data = response.body;  
    var decodeData = jsonDecode(data);  
    print(decodeData);  
  } else {  
    print('Request failed with status: ${response.statusCode}.');  
  }  
}
```
Call this getData() function in getLocation()

**Error:** 
HTTP connection error [FIX]: https://stackoverflow.com/a/65578828/11690853  
https://flutter.dev/docs/release/breaking-changes/network-policy-ios-android

*So far, we have been able to print the Weather Data to console.*

## 3. Using Data in UI
