# システム

## begin()

**説明:**

LCD、TFカード、シリアルポートの有効化/無効化を設定します。

**構文:**

```arudino
void begin(bool LCDEnable=true, bool SDEnable=true, bool SerialEnable=true);
```

**定義:**

```arduino
void M5Stack::begin(bool LCDEnable, bool SDEnable, bool SerialEnable) {
  if (isInited) return;
  else isInited = true;
  if (SerialEnable) {
    Serial.begin(115200);
    Serial.flush();
    delay(50);
    Serial.print("M5Stack initializing...");
  }
  if (LCDEnable) {
    Lcd.begin();
  }
  if (SDEnable) {
    SD.begin(TFCARD_CS_PIN, SPI, 40000000);
  }
  setWakeupButton(BUTTON_A_PIN);
}
```

**使用例:**

```arduino
#include <M5Stack.h>

void setup() {
  M5.begin();
}
```

## update()

**説明:**

 ボタン A / B / C の読み取り状態を更新します。

**構文:**

```arduino
void update();
```

**定義:**

```arduino
void M5Stack::update() {

  //ボタンアップデート
  BtnA.read();
  BtnB.read();
  BtnC.read();

  //スピーカーアップデート
  Speaker.update();
}
```

**使用例:**

```arduino
#include <M5Stack.h>

void setup() {
  M5.begin();
}

void loop() {
  M5.update();
}
```

## powerOFF()

**構文:**

**説明:**

M5の電源をオフします。

```arduino
void powerOFF();
```

**定義:**

```arduino
void M5Stack::powerOFF() {

#ifdef M5STACK_FIRE
  // 電源ブーストオン
  setPowerBoostKeepOn(true);
#endif

  // 画面オフ
  Lcd.setBrightness(0);
  Lcd.sleep();

  // ESP32をディープスリープモードへ移行
  esp_sleep_enable_ext0_wakeup((gpio_num_t)_wakeupPin , LOW);

  while (digitalRead(_wakeupPin) == LOW) {
    delay(10);
  }
  esp_deep_sleep_start();
}
```

**使用例:**

```arduino
#include <M5Stack.h>

void setup() {
  M5.begin();
  M5.Lcd.println("This is software power off demo");
  M5.Lcd.println("Press the button A to power off.");
  M5.setWakeupButton(BUTTON_A_PIN);
}

void loop() {
  M5.update();
  if (M5.BtnA.wasPressed()) {
    M5.powerOFF();
  }
}
```