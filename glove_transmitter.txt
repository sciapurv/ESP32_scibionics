#include <WiFi.h>
#include <HTTPClient.h>

// Wi-Fi Credentials
const char* ssid = "SciServos";
const char* password = "123456789";

// Potentiometer Pin Definitions
#define POT1_PIN 34
#define POT2_PIN 35
#define POT3_PIN 32
#define POT4_PIN 33
#define POT5_PIN 36

void setup() {
    Serial.begin(115200);

    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }
    Serial.println("\nConnected to AP!");
}

void loop() {
    if (WiFi.status() == WL_CONNECTED) {
        HTTPClient http;

        String url = "http://192.168.4.1/control?";
        url += "pot1=" + String(map(analogRead(POT1_PIN), 0, 4095, 0, 180)) + "&";
        url += "pot2=" + String(map(analogRead(POT2_PIN), 0, 4095, 0, 180)) + "&";
        url += "pot3=" + String(map(analogRead(POT3_PIN), 0, 4095, 0, 180)) + "&";
        url += "pot4=" + String(map(analogRead(POT4_PIN), 0, 4095, 0, 180)) + "&";
        url += "pot5=" + String(map(analogRead(POT5_PIN), 0, 4095, 0, 180));

        http.begin(url);
        int httpResponseCode = http.GET();

        if (httpResponseCode == 200) {
            Serial.println("Servos updated successfully.");
        } else {
            Serial.print("Error: ");
            Serial.println(httpResponseCode);
        }

        http.end();
    }

    delay(100);  // Smooth movement delay
}