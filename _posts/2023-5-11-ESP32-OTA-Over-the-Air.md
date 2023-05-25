###OTA
![](https://github.com/GaryHSU16/MCU-course/blob/main/images/OTA.JPG?raw=true)

    #include <Arduino.h>
    #include <WiFi.h>
    #include <AsyncTCP.h>
    #include <ESPAsyncWebServer.h>
    #include <AsyncElegantOTA.h>

        const char* ssid = "YOUR_SSID";
        const char* password = "YOUR_PASSWORD";

        AsyncWebServer server(80);

void setup(void) {
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  Serial.println("");

  // Wait for connection
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.print("Connected to ");
  Serial.println(ssid);
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request) {
    request->send(200, "text/plain", "Hi! I am ESP32.");
  });

  AsyncElegantOTA.begin(&server);    // Start ElegantOTA
  server.begin();
  Serial.println("HTTP server started");
}

void loop(void) {
}
![](https://github.com/GaryHSU16/MCU-course/blob/main/images/1791.jpg?raw=true)
![](https://github.com/GaryHSU16/MCU-course/blob/main/images/1792.jpg?raw=true)
![](https://github.com/GaryHSU16/MCU-course/blob/main/images/%E6%8A%95%E5%BD%B1%E7%89%871.JPG?raw=true)
![](https://github.com/GaryHSU16/MCU-course/blob/main/images/346100347_999025321265349_8138254261851382640_n.jpg?raw=true)
