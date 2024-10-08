#include <WiFi.h>
#include <WebServer.h>

// Replace with your network credentials
const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";

// Define pin numbers for LEDs
const int greenLED = 18;
const int redLED = 19;

// Create a web server object that listens for HTTP requests on port 80
WebServer server(80);

void handleRoot() {
  // HTML content for the web page
  String htmlPage = "<h1>LED Control</h1>";
  htmlPage += "<p><a href=\"/greenOn\">Turn ON Green LED</a></p>";
  htmlPage += "<p><a href=\"/greenOff\">Turn OFF Green LED</a></p>";
  htmlPage += "<p><a href=\"/redOn\">Turn ON Red LED</a></p>";
  htmlPage += "<p><a href=\"/redOff\">Turn OFF Red LED</a></p>";
  server.send(200, "text/html", htmlPage);  // Send the HTML content
}

void setup() {
  // Initialize LEDs
  pinMode(greenLED, OUTPUT);
  pinMode(redLED, OUTPUT);

  // Start Serial monitor
  Serial.begin(115200);

  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi!");
  Serial.println(WiFi.localIP());  // Print ESP32's local IP

  // Define the endpoints for controlling the LEDs
  server.on("/", handleRoot);
  server.on("/greenOn", []() {
    digitalWrite(greenLED, HIGH);
    server.send(200, "text/html", "Green LED is ON. <a href=\"/\">Go Back</a>");
  });
  
  server.on("/greenOff", []() {
    digitalWrite(greenLED, LOW);
    server.send(200, "text/html", "Green LED is OFF. <a href=\"/\">Go Back</a>");
  });
  
  server.on("/redOn", []() {
    digitalWrite(redLED, HIGH);
    server.send(200, "text/html", "Red LED is ON. <a href=\"/\">Go Back</a>");
  });
  
  server.on("/redOff", []() {
    digitalWrite(redLED, LOW);
    server.send(200, "text/html", "Red LED is OFF. <a href=\"/\">Go Back</a>");
  });

  // Start the server
  server.begin();
}

void loop() {
  // Handle client requests
  server.handleClient();
}
