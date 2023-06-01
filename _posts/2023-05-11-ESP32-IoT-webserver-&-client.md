### IOT Webserver&Client
![](https://github.com/GaryHSU16/MCU-course/blob/main/images/webserver.jpg?raw=true)
### code of client

    #include <WiFi.h>
    #include <WiFiClient.h>
    #include <HTTPClient.h>
    #include <Wire.h>
    #include <Adafruit_HTU21DF.h>

    // Connect Vin to 3-5VDC
    // Connect GND to ground
    // Connect SCL to I2C clock pin (A5 on UNO, D1 on NodeMCU)
    // Connect SDA to I2C data pin (A4 on UNO, D2 on NodeMCU)

    Adafruit_HTU21DF htu = Adafruit_HTU21DF();

    const char* ssid     = "your_ssid";
    const char* password = "your_password";
    String      webserverIP = "http://192.168.1.12"; // Your Webserver IP address

    void setup() {
    Serial.begin(115200);
      Serial.println("HTU21D-F test");  
      if (!htu.begin()) {
        Serial.println("Couldn't find HTU21DF sensor!");
        while (1);
      }
  
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
      delay(5000);

      float temp  = htu.readTemperature();
      float humid = htu.readHumidity();
      Serial.print(temp);
      Serial.print(" ");
      Serial.print(humid);
      Serial.println();
  
      String url = webserverIP + "/htu21?";
      url += "T=";
      url += String(temp);
      url += "&H=";
      url += String(humid);
  
        if(WiFi.status()== WL_CONNECTED){   //Check WiFi connection status

     WiFiClient client;

     HTTPClient http;    //Declare object of class HTTPClient
 
     http.begin(client, url);    //Specify request destination
     http.addHeader("Content-Type", "text/plain");  //Specify content-type header
 
     int httpCode = http.POST("Message from ESP32");   //Send the request
     String payload = http.getString();                //Get the response payload
 
     Serial.println(httpCode);   //Print HTTP return code
     Serial.println(payload);    //Print request response payload

     http.end();  //Close connection
 
        }else{
             Serial.println("Error in WiFi connection");   
        }
  
        Serial.println();
        Serial.println("closing connection. going to sleep...");
        // go to deepsleep for 1 minutes
        //system_deep_sleep_set_option(0);
        //system_deep_sleep(1 * 60 * 1000000);
        delay(1*60*1000);
        }
	
	

### code of server


        #include <WiFi.h> 
		#include <WebServer.h>

		const char* ssid     = "your_ssid";
		const char* password = "your_password";

		WebServer server(80);

		const String HTTP_PAGE_HEAD  = "<!DOCTYPE html><html lang=\"en\"><head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1, 			user-scalable=no\"/><title>{v}</title>";
		const String HTTP_PAGE_STYLE = "<style>.c{text-align: center;} div,input{padding:5px;font-size:1em;}  input{width:90%;}  body{text-align: 		center;font-family:verdana;} button{border:0;border-radius:0.6rem;background-color:#1fb3ec;color:#fdd;line-height:2.4rem;font-size:1.2rem;width:100%;} 		.q{float: right;width: 64px;text-align: right;} .button2 {background-color: #008CBA;} .button3 {background-color: #f44336;} .button4 {background-color: 		#e7e7e7; color: black;} .button5 {background-color: #555555;} .button6 {background-color: #4CAF50;} </style>";
		const String HTTP_PAGE_SCRIPT= "<script>function c(l){document.getElementById('s').value=l.innerText||l.textContent;document.getElementById('p').focus();}</script>";
		const String HTTP_PAGE_BODY  = "</head><body><div style='text-align:left;display:inline-block;min-width:260px;'>";
		const String HTTP_WEBPAGE = HTTP_PAGE_HEAD + HTTP_PAGE_STYLE + HTTP_PAGE_SCRIPT + HTTP_PAGE_BODY;

		const String HTTP_PAGE_END = "</div></body></html>";

		// DHT22 sensor data
		String dht22_name0 = "Temperature";
		String dht22_name1 = "Humidity";
		String dht22_value0= "0";
		String dht22_value1= "0";
		// HTU21DF sensor data
		String htu21_name0 = "Temperature";
		String htu21_name1 = "Humidity";
		String htu21_value0= "0 ";
		String htu21_value1= "0 ";
		// PM5003 sensor data
		String pm_name0 = "PM1.0";
		String pm_name1 = "PM2.5";
		String pm_name2 = "PM10.0";
		String pm_value0= "0 ug/m3";
		String pm_value1= "0 ug/m3";
		String pm_value2= "0 ug/m3";

		void handleRoot() {
  		// Display Sensor Status
  		String s  = HTTP_WEBPAGE;
		         s += "<table border=\"1\"";
		         s += "<tr><th align='center'>DHT22 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+dht22_name0+"</td><td align='center'>"+dht22_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+dht22_name1+"</td><td align='center'>"+dht22_value1+"</td></tr>";
		         s += "<tr><th align='center'>HTU21 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+htu21_name0+"</td><td align='center'>"+htu21_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+htu21_name1+"</td><td align='center'>"+htu21_value1+"</td></tr>";
		         s += "<tr><th align='center'>PM5003 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+pm_name0+"</td><td align='center'>"+pm_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+pm_name1+"</td><td align='center'>"+pm_value1+"</td></tr>";
		         s += "<tr><td align='center'>"+pm_name2+"</td><td align='center'>"+pm_value2+"</td></tr>";               
		         s += "</tr></table>";
		         s += HTTP_PAGE_END;
         
		  server.send(200, "text/html", s);
		}

		// http://192.168.1.12/dht22?T=28&H=50 emulate data from Webclient_DHT22
		//(you can open a browser to test it, too)
		void dht22() {
		  String message = "Number of args received:";
		  message += server.args();                   //Get number of parameters
		  message += "\n";                            //Add a new line

		  for (int i = 0; i < server.args(); i++) {
		    message += "Arg "+(String)i + " –> "; //Include the current iteration value
		    message += server.argName(i) + ": ";      //Get the name of the parameter
		    message += server.arg(i) + "\n";          //Get the value of the parameter
		  }
		  Serial.print(message);

		  dht22_value0=server.arg(0);
		  dht22_value1=server.arg(1);
  
		  String s  = HTTP_WEBPAGE;
		         s += "<table border=\"1\"";
		         s += "<tr><th align='center'>DHT22 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+dht22_name0+"</td><td align='center'>"+dht22_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+dht22_name1+"</td><td align='center'>"+dht22_value1+"</td></tr>";
		         s += "<tr><th align='center'>HTU21 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+htu21_name0+"</td><td align='center'>"+htu21_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+htu21_name1+"</td><td align='center'>"+htu21_value1+"</td></tr>";
		         s += "<tr><th align='center'>PM5003 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+pm_name0+"</td><td align='center'>"+pm_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+pm_name1+"</td><td align='center'>"+pm_value1+"</td></tr>";
		         s += "<tr><td align='center'>"+pm_name2+"</td><td align='center'>"+pm_value2+"</td></tr>";          
		         s += "</tr></table>";
		         s += HTTP_PAGE_END; 
	 
		  server.send(200, "text/html", s);
		}

		// http://192.168.1.12/htu21?T=28&H=50 emulate data from Webclient_HTU21
		//(you can open a browser to test it, too)
		void htu21() {
		  String message = "Number of args received:";
		  message += server.args();                   //Get number of parameters
		  message += "\n";                            //Add a new line
	
		  for (int i = 0; i < server.args(); i++) {
		    message += "Arg "+(String)i + " –> "; //Include the current iteration value
		    message += server.argName(i) + ": ";      //Get the name of the parameter
		    message += server.arg(i) + "\n";          //Get the value of the parameter
		  }
		  Serial.print(message);

		  htu21_value0=server.arg(0);
		  htu21_value1=server.arg(1);
  
		  String s  = HTTP_WEBPAGE;
		         s += "<table border=\"1\"";
		         s += "<tr><th align='center'>DHT22 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+dht22_name0+"</td><td align='center'>"+dht22_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+dht22_name1+"</td><td align='center'>"+dht22_value1+"</td></tr>";
		         s += "<tr><th align='center'>HTU21 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+htu21_name0+"</td><td align='center'>"+htu21_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+htu21_name1+"</td><td align='center'>"+htu21_value1+"</td></tr>";
		         s += "<tr><th align='center'>PM5003 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+pm_name0+"</td><td align='center'>"+pm_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+pm_name1+"</td><td align='center'>"+pm_value1+"</td></tr>";
		         s += "<tr><td align='center'>"+pm_name2+"</td><td align='center'>"+pm_value2+"</td></tr>";          
		         s += "</tr></table>";
		         s += HTTP_PAGE_END; 
   
		  server.send(200, "text/html", s);
		}

		// http://192.168.1.12/pm25?pm10=25&pm100=40&pm100=50 emulate data from Webclient_PM5003
		//(you can open a browser to test it, too)
		void pm25() {
		  String message = "Number of args received:";
		  message += server.args();                   //Get number of parameters
		  message += "\n";                            //Add a new line

		  for (int i = 0; i < server.args(); i++) {
		    message += "Arg "+(String)i + " –> "; //Include the current iteration value
		    message += server.argName(i) + ": ";      //Get the name of the parameter
		    message += server.arg(i) + "\n";          //Get the value of the parameter
		  }
		  Serial.print(message);

		  pm_value0=server.arg(0)+" ug/m3";
		  pm_value1=server.arg(1)+" ug/m3";
		  pm_value2=server.arg(2)+" ug/m3";
		    
		  String s  = HTTP_WEBPAGE;
		         s += "<table border=\"1\"";
		         s += "<tr><th align='center'>DHT22 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+dht22_name0+"</td><td align='center'>"+dht22_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+dht22_name1+"</td><td align='center'>"+dht22_value1+"</td></tr>";
		         s += "<tr><th align='center'>HTU21 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+htu21_name0+"</td><td align='center'>"+htu21_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+htu21_name1+"</td><td align='center'>"+htu21_value1+"</td></tr>";
		         s += "<tr><th align='center'>PM5003 Sensor</th><th align='cener'>value</th></tr>";
		         s += "<tr><td align='center'>"+pm_name0+"</td><td align='center'>"+pm_value0+"</td></tr>";
		         s += "<tr><td align='center'>"+pm_name1+"</td><td align='center'>"+pm_value1+"</td></tr>";
		         s += "<tr><td align='center'>"+pm_name2+"</td><td align='center'>"+pm_value2+"</td></tr>";          
		         s += "</tr></table>";
		         s += HTTP_PAGE_END; 
   
		  server.send(200, "text/html", s);
		}

		void setup() {
 		 Serial.begin(115200);
  
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

		  server.on("/", handleRoot);
		  server.on("/dht22", dht22);
		  server.on("/htu21", htu21);
		  server.on("/pm25", pm25);  
  
		  Serial.println("HTTP server started");
		  server.begin();  
		}

		void loop() {
		  server.handleClient();
		}

![](https://github.com/GaryHSU16/MCU-course/blob/main/images/1790.jpg?raw=true)
<iframe width="1280" height="720" src="https://www.youtube.com/embed/gBMwBVS6LMQ" title="server client" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
