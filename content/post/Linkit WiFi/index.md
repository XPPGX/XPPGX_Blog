---
title: Linkit Wifi Connecting
subtitle: 

# Summary for listings and search engines
summary: Use Linkit7697 to connect to WiFi

# Link this post with a project
projects: []

# Date published
date: "2022-04-10T00:00:00Z"

# Date updated
lastmod: "2022-04-10T00:00:00Z"

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: true

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 
  focal_point: "Center"
  placement: 1
  preview_only: true

authors:
- admin

tags:
- Tutorial
- WiFi

categories:
- Linkit

---

![WiFi](featured.jpg)
## 環境
- OS : Windows10
- IDE : Arduino 1.8.19
- 開發板 : Linkit 7697

```
#include <LWiFi.h>

char ssid[] = "your WiFiSSID";
char pass[] = "your WiFipassword";
int status = WL_IDLE_STATUS;

WiFiClient client;

void setup(){
    Serial.begin(9600);
    while(!Serial){
        ;
    }
    /*透過WiFi.begin(ssid,pass)讓網路連上*/
    while(status!=WL_CONNECTED){
        Serial.print("Attempting to connect to SSID：");
        Serial.println(ssid);
        status = WiFi.begin(ssid,pass);
    }
    Serial.println("Connected to wifi");
    printWifiStatus();
    /*至此已讓WiFi連上，後續再利用client物件進行其他HTTP的使用*/
    /*Example : client connect to ThingSpeak.com to upload a data*/
    /*
    char server[] = "api.thingspeak.com"
    int port = 80;
    String GET = "GET /update.json?api_key=<your Write api_key>";
    
    if(client.connect(server,port)){
        Serial.println("connected to server (GET)");
        String str = GET + "&field1=" + String(data);
        client.println(str);
        client.println("Host: api.thingspeak.com");
        client.println("Connection: close\r\n\r\n");
        delay(1000);
        }
    while (client.available()) {
            char c = client.read();
            Serial.write(c);
        }
    */
}

void printWifiStatus() {
    // print the SSID of the network you're attached to:
    Serial.print("SSID: ");
    Serial.println(WiFi.SSID());

    // print your WiFi shield's IP address:
    IPAddress ip = WiFi.localIP();
    Serial.print("IP Address: ");
    Serial.println(ip);

    // print the received signal strength:
    long rssi = WiFi.RSSI();
    Serial.print("signal strength (RSSI):");
    Serial.print(rssi);
    Serial.println(" dBm");
}
```