/**The MIT License (MIT)
Copyright (c) 2015 by Daniel Eichhorn
Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
See more at http://blog.squix.ch
*/

// Modified to use DHTesp instead of DHT library

#include <ESP8266WiFi.h>
#include "DHTesp.h"


/***************************
 * Begin Settings
 **************************/


const char* ssid     = "YOUR-SSID";
const char* password = "YOUR-PASS";

const char* host = "api.thingspeak.com";

const char* THINGSPEAK_API_KEY = " ";

// DHT Settings
#define LED 2        //On board LED
#define DHTpin 14    //D5 of NodeMCU is GPIO14

DHTesp dht;

// Uncomment whatever type you're using!
#define DHTTYPE DHT11   // DHT 11
//#define DHTTYPE DHT22   // DHT 22  (AM2302), AM2321
//#define DHTTYPE DHT21   // DHT 21 (AM2301)

const boolean IS_METRIC = true;

// Update every 600 seconds = 10 minutes. Min with Thingspeak is ~20 seconds ONLY USED IF YOU COMMENT OUT THE DEEP SLEEP!
const int UPDATE_INTERVAL_SECONDS = 60;

/***************************
 * End Settings
 **************************/
 
// Initialize the temperature/ humidity sensor
//dht dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  delay(10);

  dht.setup(DHTpin, DHTesp::DHT11);

  pinMode(LED,OUTPUT); 
  

  // We start by connecting to a WiFi network

  Serial.println();
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
  
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected");  
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void loop() {      
    Serial.print("connecting to ");
    Serial.println(host);
    
    // Use WiFiClient class to create TCP connections
    WiFiClient client;
    const int httpPort = 80;
    if (!client.connect(host, httpPort)) {
      Serial.println("connection failed");
      return;
    }

float humidity, temperature,  tempf;
    // read values from the sensor
   // float humidity = dht.readHumidity();
    //float temperature = dht.readTemperature(!IS_METRIC);
     delay(dht.getMinimumSamplingPeriod());

  humidity = dht.getHumidity();
  temperature = dht.getTemperature();
  tempf = dht.toFahrenheit(temperature);

    Serial.print("Humidity ");
    Serial.println(humidity, 1);
    Serial.print("Temperature ");
    Serial.println(temperature, 1);
    
    // We now create a URI for the request
    String url = "/update?api_key=";
    url += THINGSPEAK_API_KEY;
    url += "&field1=";
    url += String(tempf);
    url += "&field2=";
    url += String(humidity);

    digitalWrite(LED,!digitalRead(LED));
    
    Serial.print("Requesting URL: ");
    Serial.println(url);
    
    // This will send the request to the server
    client.print(String("GET ") + url + " HTTP/1.1\r\n" +
                 "Host: " + host + "\r\n" + 
                 "Connection: close\r\n\r\n");
    delay(10);
    while(!client.available()){
      delay(100);
      Serial.print(".");
    }
    // Read all the lines of the reply from server and print them to Serial
    while(client.available()){
      String line = client.readStringUntil('\r');
      Serial.print(line);
    }
    
    Serial.println();
    Serial.println("closing connection");


  // Go back to sleep. If your sensor is battery powered you might
  // want to use deep sleep here. If you want to use regular delay, put  two slash behind ESP.deepsleep and remove from delay
  //delay(1000 * UPDATE_INTERVAL_SECONDS);
    Serial.println("Going into deep sleep for 60 seconds");
  ESP.deepSleep(60*1000000);
}
