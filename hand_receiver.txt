#include <WiFi.h>
#include <ESP32Servo.h>
#include <WebServer.h>

// Wi-Fi Credentials
const char* ssid = "SciServos";  
const char* password = "123456789";  

// Servo Pin Definitions
#define SERVO1_PIN 2
#define SERVO2_PIN 4
#define SERVO3_PIN 16
#define SERVO4_PIN 17
#define SERVO5_PIN 5

// Servo Objects
Servo servo1, servo2, servo3, servo4, servo5;

// Create WebServer on Port 80
WebServer server(80);

// Function to Control Servos
void handleControl() {
    if (server.hasArg("pot1")) servo1.write(server.arg("pot1").toInt());
    if (server.hasArg("pot2")) servo2.write(server.arg("pot2").toInt());
    if (server.hasArg("pot3")) servo3.write(server.arg("pot3").toInt());
    if (server.hasArg("pot4")) servo4.write(server.arg("pot4").toInt());
    if (server.hasArg("pot5")) servo5.write(server.arg("pot5").toInt());

    server.send(200, "text/plain", "Servos updated");
}

void setup() {
    Serial.begin(115200);

    // Start Wi-Fi as AP
    WiFi.softAP(ssid, password);
    Serial.println("Access Point Started");
    Serial.print("IP Address: ");
    Serial.println(WiFi.softAPIP());

    // Attach Servos
    servo1.attach(SERVO1_PIN);
    servo2.attach(SERVO2_PIN);
    servo3.attach(SERVO3_PIN);
    servo4.attach(SERVO4_PIN);
    servo5.attach(SERVO5_PIN);

    // WebServer Route for Servo Control
    server.on("/control", handleControl);

    server.begin();
    Serial.println("Server started!");
}

void loop() {
    server.handleClient();
}