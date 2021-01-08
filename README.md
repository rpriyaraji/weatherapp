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
```
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

```
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
We use the [geolocator](https://pub.dev/packages/geolocator) Flutter package. 
```
void getLocation() async{  
  Position position = await Geolocator.getCurrentPosition(desiredAccuracy: LocationAccuracy.low);  
  print(position.latitude);  
  print(position.longitude);  
}
```

