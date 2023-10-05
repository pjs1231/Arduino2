1번 창의공학을 통해서 학생들이 학습해야할 내용에 대해 5가지를 적으시오.


문제 해결 능력,협력과 커뮤니케이션,디자인과 창의성,정보 기술과 프로그래밍,지속 가능성과 윤리





2번 스마트폰으로 집의 전등을 켜고 끄는 시스템을 설계하고 제작하는 과정을 유무선 통신을 포함한 과정을 자세히 설명하시오.


1. 하드웨어 구성

먼저, 아래와 같은 하드웨어를 구성합니다.

아두이노 보드: 아두이노 UNO 또는 ESP8266, ESP32와 같은 보드를 사용합니다.
릴레이 모듈: 전등을 켜고 끄기 위한 릴레이 모듈을 연결합니다.
Wi-Fi 모듈: Wi-Fi 연결을 제공하는 모듈을 사용합니다.
전등 및 전원 공급 장치: 전등을 연결하고 전원을 제공합니다.
2. 아두이노 프로그래밍

아두이노에 아래와 같이 아두이노 코드를 업로드합니다. 이 코드는 Wi-Fi 모듈을 통해 스마트폰과 통신하고, 릴레이를 제어합니다.

3. 스마트폰 애플리케이션

스마트폰 애플리케이션을 개발하여 아두이노와 통신하고 사용자에게 전등 제어를 제공합니다. 이를 위해 Android 또는 iOS 애플리케이션을 개발해야 합니다. 앱에서 사용자가 전등을 켜고 끌 수 있는 버튼을 추가합니다.

4. 무선 통신 설정

스마트폰 애플리케이션에서 Wi-Fi 모듈로 전등을 제어할 수 있도록 통신을 설정합니다. 아두이노와 스마트폰 애플리케이션 간의 통신은 HTTP GET 요청을 사용하여 전등을 켜고 끄는 기능을 활성화합니다.

5. 웹 서버 호스팅 또는 로컬 서버

스마트폰 애플리케이션은 아두이노의 IP 주소 또는 도메인을 통해 아두이노에 연결합니다. 이를 위해 아두이노 코드에서 서버를 호스팅하거나, 외부 웹 서버를 사용하여 통신할 수 있습니다.

6. 애플리케이션 테스트 및 배포

스마트폰 애플리케이션을 테스트하고, 사용자에게 배포합니다. 사용자는 애플리케이션을 통해 집의 전등을 켜고 끌 수 있게 됩니다.



3번 아두이노의 스위치를 읽고 서버로 1초에 1회씩 전송하는 코드와 서버에서 수신하여 1이면 초록색, 2이면 검은색으로 바꾸는 프로세싱 코드를 작성해라 



아두이노 코드
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>

const char* ssid = "Your_SSID";         // Wi-Fi 네트워크 이름
const char* password = "Your_Password"; // Wi-Fi 비밀번호
const char* serverAddress = "Your_Server_Address"; // 서버 주소

const int switchPin = D2; // 스위치 연결 핀 (D2 핀 사용)

void setup() {
  pinMode(switchPin, INPUT_PULLUP); // 스위치 핀을 입력으로 설정하고 풀업 저항 사용
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
}

void loop() {
  int switchState = digitalRead(switchPin);

  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;

    String data = "switchState=" + String(switchState);

    http.begin(serverAddress);
    http.addHeader("Content-Type", "application/x-www-form-urlencoded");

    int httpCode = http.POST(data);

    if (httpCode > 0) {
      String response = http.getString();
      Serial.println(response);
    } else {
      Serial.println("Error on HTTP request");
    }

    http.end();
  }

  delay(1000); // 1초마다 스위치 상태를 서버로 전송
}


프로세싱 코드

import processing.net.*;

int ledPin = 13; // LED 연결 핀 (13번 핀 사용)
int switchState = 0; // 스위치 상태

Server server;
Client client;

void setup() {
  size(200, 100);
  background(0);
  noStroke();
  fill(255);

  pinMode(ledPin, OUTPUT);
  server = new Server(this, 12345); // 포트 번호를 적절하게 변경

  println("Server is running...");
}

void draw() {
  client = server.available();
  if (client != null) {
    String data = client.readStringUntil('\n');
    if (data != null) {
      switchState = int(data.trim());

      if (switchState == 1) {
        digitalWrite(ledPin, HIGH); // 초록색 LED를 켭니다.
        background(0, 255, 0); // 배경을 초록색으로 변경합니다.
      } else if (switchState == 2) {
        digitalWrite(ledPin, LOW); // LED를 끕니다.
        background(0); // 배경을 검은색으로 변경합니다.
      }

      println("Received switch state: " + switchState);
    }
    client.stop();
  }
}

4번 서버에서 스마트폰으로 1을 보내면 스마트폰의 화면 푸른색으로, 0을 보내면 붉은 색으로 보내는 코드를 적으시오. 앱 인벤터의 컴포넌트와 내용을 적으시오

import processing.net.*;

Client client; // 클라이언트 객체
color bgColor = color(255); // 기본 배경 색상 (흰색)

void setup() {
  size(400, 400); // 화면 크기 설정
  background(bgColor); // 배경 색상 설정
  client = new Client(this, "서버의_IP_주소", 서버의_포트_번호); // 서버에 연결
}

void draw() {
  // 클라이언트로부터 데이터를 받아옴
  while (client.available() > 0) {
    char data = client.readChar();
    if (data == '1') {
      bgColor = color(0, 0, 255); // 푸른색
    } else if (data == '0') {
      bgColor = color(255, 0, 0); // 붉은색
    }
    background(bgColor); // 화면 색상 업데이트
  }
}

void keyPressed() {
  // 키를 누를 때 1 또는 0을 서버로 보냄
  if (key == '1' || key == '0') {
    client.write(key);
  }
}

5번 스마트폰에서 on 버튼을 누르면 서버를 거쳐 아두이노의 7번 핀의 LED 켜지고, off 버튼을 누르면 7번 핀의 LED가 꺼진다. 스마트폰의 텍스트 박스에 숫자 1부터 8 중에 하나를 넣고, Send 버튼을 누르면 아두이노의 4번 핀에 달린 피에조 스피커가 도,레~시,도가 작동하도록 하시오.

아두이노 코드
#include <SoftwareSerial.h>

SoftwareSerial bluetooth(2, 3); // Bluetooth 모듈의 RX, TX 핀을 2, 3번 핀으로 설정

int speakerPin = 4; // 피에조 스피커 연결 핀
int noteDuration = 1000;
int notes[] = {262, 294, 330, 349, 392, 440, 494, 523}; // 도, 레, 미, 파, 솔, 라, 시, 도

void setup() {
  bluetooth.begin(9600); // Bluetooth 통신 시작
  pinMode(speakerPin, OUTPUT);
}

void loop() {
  if (bluetooth.available() > 0) {
    char command = bluetooth.read();
    if (command >= '1' && command <= '8') {
      int note = notes[command - '1'];
      tone(speakerPin, note, noteDuration);
      delay(noteDuration);
      noTone(speakerPin);
    }
  }
}

프로레싱 코드

import android.content.Context;
import android.os.Bundle;
import android.app.Activity;
import android.bluetooth.BluetoothAdapter;
import android.bluetooth.BluetoothDevice;
import android.bluetooth.BluetoothSocket;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import java.io.IOException;
import java.io.OutputStream;
import java.util.UUID;

public class MainActivity extends Activity {
    Button sendButton;
    EditText textBox;
    private static final UUID MY_UUID =
        UUID.fromString("00001101-0000-1000-8000-00805F9B34FB"); // Bluetooth SPP UUID
    private static String address = "YOUR_BLUETOOTH_DEVICE_ADDRESS"; // 아두이노 Bluetooth 모듈의 MAC 주소
    private BluetoothAdapter btAdapter = null;
    private BluetoothSocket btSocket = null;
    private OutputStream outStream = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        sendButton = (Button) findViewById(R.id.sendButton);
        textBox = (EditText) findViewById(R.id.textBox);

        btAdapter = BluetoothAdapter.getDefaultAdapter();
        checkBTState();

        sendButton.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                sendData(textBox.getText().toString());
            }
        });
    }

    private BluetoothSocket createBluetoothSocket(BluetoothDevice device) throws IOException {
        return device.createRfcommSocketToServiceRecord(MY_UUID);
    }

    @Override
    public void onResume() {
        super.onResume();
        BluetoothDevice device = btAdapter.getRemoteDevice(address);
        try {
            btSocket = createBluetoothSocket(device);
        } catch (IOException e) {
            errorExit("Fatal Error", "In onResume() and socket create failed: " + e.getMessage() + ".");
        }
        btAdapter.cancelDiscovery();
        try {
            btSocket.connect();
        } catch (IOException e) {
            try {
                btSocket.close();
            } catch (IOException e2) {
                errorExit("Fatal Error", "In onResume() and unable to close socket during connection failure" + e2.getMessage() + ".");
            }
        }
        try {
            outStream = btSocket.getOutputStream();
        } catch (IOException e) {
            errorExit("Fatal Error", "In onResume() and output stream creation failed:" + e.getMessage() + ".");
        }
    }

    @Override
    public void onPause() {
        super.onPause();
        if (outStream != null) {
            try {
                outStream.flush();
            } catch (IOException e) {
                errorExit("Fatal Error", "In onPause() and failed to flush output stream: " + e.getMessage() + ".");
            }
        }
        try {
            btSocket.close();
        } catch (IOException e2) {
            errorExit("Fatal Error", "In onPause() and failed to close socket." + e2.getMessage() + ".");
        }
    }

    private void checkBTState() {
        if (btAdapter == null) {
            errorExit("Fatal Error", "Bluetooth not supported. Aborting.");
        } else {
            if (!btAdapter.isEnabled()) {
                errorExit("Fatal Error", "Bluetooth is not enabled. Please enable Bluetooth and restart the app.");
            }
        }
    }

    private void sendData(String message) {
        byte[] msgBuffer = message.getBytes();
        try {
            outStream.write(msgBuffer);
        } catch (IOException e) {
            errorExit("Fatal Error", "In sendData() and an exception occurred during write: " + e.getMessage() + ".");
        }
    }

    private void errorExit(String title, String message) {
        Context context = getBaseContext();
        Toast.makeText(context, title + " - " + message, Toast.LENGTH_LONG).show();
        finish();
    }
}
