# lab-four-2019
# Wi-Fi
## Introduction

The objective of this part is to explain how to get started using the WiFi functionalities of the ESP32, more precisely how to scan surrounding WiFi networks and how to connect to a specific WiFi network.
This part will also cover getting some parameters, such as the local IP of the ESP32 when connected to the WiFi network, and also its MAC address. We will also cover how to disconnect from the WiFi network.

> Your ESP32 has a wifi module. 
## Setup
> The `WiFi.h` library allows an Arduino board to connect to the internet. It can serve as either a server accepting incoming connections or a client making outgoing ones. The library supports WEP and WPA2 Personal encryption, but not WPA2 Enterprise. Also note, if the SSID is not broadcast, the shield cannot connect.

The first thing we are going to do is including the WiFi.h library
```C
#include <WiFi.h>
```

Next, we will declare two global variables to hold our WiFi network credentials, more precisely the network name (SSID) and the password. 

```C
const char* ssid = "yourNetworkName";
const char* password = "yourNetworkPassword";
```

## Scanning for WiFi
```C
void scanNetworks() {
 
  int numberOfNetworks = WiFi.scanNetworks();
 
  Serial.print("Number of networks found: ");
  Serial.println(numberOfNetworks);
 
  for (int i = 0; i < numberOfNetworks; i++) {
 
    Serial.print("Network name: ");
    Serial.println(WiFi.SSID(i));
 
    Serial.print("Signal strength: ");
    Serial.println(WiFi.RSSI(i));
 
    Serial.print("MAC address: ");
    Serial.println(WiFi.BSSIDstr(i));
 
    //Serial.print("Encryption type: ");
    //Serial.print(WiFi.encryptionType(i)); the type is  wifi_auth_mode_t
   
    Serial.println("-----------------------");
 
  }
}
```

## Connecting to a Network
```C
void connectToNetwork() {
  WiFi.begin(ssid, password);
 
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Establishing connection to WiFi..");
  }
  
  Serial.println("Connected to network");
}
```
## Print WiFi information
```C
void printWiFiInfo() {
  Serial.println(WiFi.macAddress());
  Serial.println(WiFi.localIP());
 
  WiFi.disconnect(true);
  Serial.println(WiFi.localIP());
}
```


# Json 
Json is an open-standard file format that uses human-readable text to transmit data objects consisting of attribute–value pairs and array data types (or any other serializable value). It is a very common data format, with a diverse range of applications. [More info](https://en.wikipedia.org/wiki/JSON)

To use json parser in Arduino you should search for  ArduinoJSON v6. [More info](https://arduinojson.org/v6/doc/)
## Write Json
```C
  DynamicJsonDocument  doc(200);
  doc["sensor"] = "gps";
  doc["time"] = 1351824120;
  JsonArray data = doc.createNestedArray("data");
  data.add(48.756080);
  data.add(2.302038);
  String output;
  serializeJsonPretty(doc, output);// You can also use serializeJson(doc, Serial);
  Serial.println(output);
  // The above line prints:
  // {
  //   "sensor": "gps",
  //   "time": 1351824120,
  //   "data": [
  //     48.756080,
  //     2.302038
  //   ]
  // }
```
## Read Json
```C
  DynamicJsonDocument doc(1024);
  deserializeJson(doc, input);
  JsonObject obj = doc.as<JsonObject>();
  long time = obj["time"];
  Serial.println(time);
  int firstdata = obj["data"][0]; 
  Serial.println(firstdata);
```

# Http Request
The objective of this part is to explain how to send HTTP requests
First, You need to include this header.
```C
#include <HTTPClient.h>
```

## HTTP Get
To send a HTTP Get request, you can use the following code. [More Info](https://techtutorialsx.com/2017/05/19/esp32-http-get-requests/)
```C
void SendGetRequest()
 if ((WiFi.status() == WL_CONNECTED)) { //Check the current connection status
 
    HTTPClient http;
 
    http.begin("https://jsonplaceholder.typicode.com/posts/1"); //Specify the URL 
    //(A test json server: https://jsonplaceholder.typicode.com/guide.html)
    
    int httpCode = http.GET();     //Make the request
 
    if (httpCode > 0) { //Check for the returning code
 
        String payload = http.getString();
        Serial.println(httpCode);
        Serial.println(payload);
        
        DynamicJsonDocument doc(1024);
        deserializeJson(doc, payload);
        JsonObject obj = doc.as<JsonObject>();
        int userId = obj["userId"];
        Serial.println(userId);
      }
 
    else {
      Serial.println("Error on HTTP request Code:"+httpCode);
    }
 
    http.end(); //Free the resources
  }
```

## HTTP Post
[More Info](https://techtutorialsx.com/2017/05/20/esp32-http-post-requests/)
```C
void SendPostRequest()
  if ((WiFi.status() == WL_CONNECTED)) { //Check the current connection status
 
    http.begin("http://jsonplaceholder.typicode.com/posts"); //Specify destination for HTTP request
    http.addHeader("Content-Type", "application/json");
    
    DynamicJsonDocument  doc(200);
    doc["title"] = "title";
    doc["body"] = "body";
    doc["userId"] = "1";
    
    String output;
    serializeJsonPretty(doc, output);// You can also use serializeJson(doc, Serial);
    Serial.println(output);
    
    int httpCode = http.POST(output); //Send the actual POST request
    if(httpCode>0){
      String response = http.getString(); //Get the response to the request
      Serial.println(httpCode);   //Print return code
      Serial.println(response);           //Print request answer
    }else{
      Serial.print("Error on sending POST: ");
      Serial.println(httpCode);
    }
  }
}
```

## HTTPS
Please Follow the instruction [here](https://techtutorialsx.com/2017/11/18/esp32-arduino-https-get-request/)


# Practice 0
- All member of a team should read completly the lab instruction and participate in doing the lab. 

# Practice 1
1- Create a hotspot from your mobile phone.
2- Connect your ESP32 to the hotspot.
3- Get free API key from: [Open Weather Map](https://openweathermap.org/price)
4- Once the activation time ends, you can do a quick test to check if you can reach the API with your key. To do it, simply access the following URL on your browser, changing {yourAPIkey} by the key you have obtained:
```http://api.openweathermap.org/data/2.5/weather?q=Paris,fr&units=metric&APPID={yourAPIkey}```
5- Read Weather information from Arduino and print current tempereture in Serial
6- Save your codes into `lab/4/report/1/code.ino`, Save a screenshot of your serial console into `lab/4/report/1/screenshot.png`, Create a README.md file which contains a short description, your code, and the image.


# Practice 2
Do Part 4 from lab-three-2019



