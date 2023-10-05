#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>

const char* ssid = "Your_SSID"; // Wi-Fi 네트워크 이름
const char* password = "Your_Password"; // Wi-Fi 비밀번호

ESP8266WebServer server(80);

void setup() {
  pinMode(D1, OUTPUT); // D1 핀을 출력으로 설정 (릴레이 제어 핀)

  // Wi-Fi 연결
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");

  // 웹 서버 시작
  server.on("/", HTTP_GET, handleRoot);
  server.on("/on", HTTP_GET, handleOn);
  server.on("/off", HTTP_GET, handleOff);
  server.begin();
}

void loop() {
  server.handleClient();
}

void handleRoot() {
  server.send(200, "text/html", "Welcome to the Light Control System");
}

void handleOn() {
  digitalWrite(D1, LOW); // 릴레이를 활성화하여 전등 켜기
  server.send(200, "text/html", "Light is ON");
}

void handleOff() {
  digitalWrite(D1, HIGH); // 릴레이를 비활성화하여 전등 끄기
  server.send(200, "text/html", "Light is OFF");
}
