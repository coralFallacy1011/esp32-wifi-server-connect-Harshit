#include <WiFi.h>
#include <HTTPClient.h>
#include "soc/soc.h"
#include "soc/rtc_cntl_reg.h"

const char* ssid = "Your_Wifi_Name";
const char* password = "Your_Wifi_Password";

const char* serverName = "http://192.168.0.172:4000/api/test";

const int max_connection_attempts = 15;
int connection_attempts = 0;

void setup()
{
  WRITE_PERI_REG(RTC_CNTL_BROWN_OUT_REG, 0);
  Serial.begin(115200);
  Serial.println("Brownout detector disabled");
  connectToWiFi();
  pinMode(2, INPUT);
  pinMode(4, INPUT);
  pinMode(5, INPUT);
}

void connectToWiFi()
{
  if (WiFi.status() != WL_CONNECTED) {
    if (connection_attempts < max_connection_attempts) {
      connection_attempts++;
      Serial.print("Connecting to WiFi... (attempt: ");
      Serial.print(connection_attempts);
      Serial.println(')');
      WiFi.begin(ssid, password);
    } else {
      Serial.println("Failed to connect to WiFi after multiple attempts. Exiting.");
      while (true) {
        delay(1000);
      }
    }
  }
}

void sendPostRequest()
{
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    http.begin(serverName);
    http.addHeader("Content-Type", "application/json");
  
    int tag_number = random(1000, 9999);
    int v1=digitalRead(2);
    v1=v1*1;
    int v2=digitalRead(4);
    v2=v2*2;
    int v3=digitalRead(5);
    v3=v3*4; 
    int weight_number = v1+v2+v3;

    String jsonPayload = "{\"rfid\": \"" + String(tag_number) + "\", \"weight\": \"" + String(weight_number) + "\"}";

    int httpResponseCode = http.POST(jsonPayload);

    if (httpResponseCode > 0) {
      Serial.print("HTTP Response code: ");
      Serial.println(httpResponseCode);

      String response = http.getString(); // Read the server's response
      Serial.print("Server response: ");
      Serial.println(response);
    } else {
      Serial.print("Error on sending POST: ");
      Serial.println(httpResponseCode);
    }

    http.end();
  } else {
    Serial.println("Error in WiFi connection");
    connectToWiFi();
  }
}

void loop()
{
  sendPostRequest();
  delay(1000); // Wait for 1 seconds before sending the next request
}
