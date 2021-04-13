# PlatformIO library helper to use libqrencode

This is a helper to get [libqrencode](https://github.com/fukuchi/libqrencode) compiled on ESP32s (could work for other platformio boards I just haven't tried).

## Usage

In your [platformio](https://platformio.org)

```sh
pio lib install --save https://github.com/ddlsmurf/esp32_libqrencode.git
```

In your `src/whatever.ino`:

```c
#include <Arduino.h>
#include "qrencode.h"

static void printLine(const char *line, const unsigned int repeat) {
    for (int i = 0; i < repeat; i++)
        Serial.println(line);
}

static void printQRcode(QRcode *q, const unsigned int xMult, const unsigned int yMult) {
    const unsigned int spacer = 3, hSpacer = spacer * xMult, vSpacer = spacer * yMult;
    const char charNotSpace = 'X', charSpace = ' ';
    const unsigned int lineLength = hSpacer * 2 + q->width * xMult;
    char row[lineLength + 1] = {0};
    memset(row, charSpace, lineLength);
    printLine(row, vSpacer);
    for (int y = 0; y < q->width; y++) {
        for (int x = 0; x < q->width; x++)
            memset(row + hSpacer + x * xMult, (q->data[y * q->width + x] & 1) ? charNotSpace : charSpace, xMult);
        printLine(row, yMult);
    }
    memset(row + hSpacer, charSpace, lineLength - hSpacer);
    printLine(row, vSpacer);
}

void setup() {
    Serial.begin(115200);
    delay(2000);
    QRcode *q = QRcode_encodeString("hi there", 0, QR_ECLEVEL_Q, QR_MODE_8, 1);
    printQRcode(q, 3, 2);
    QRcode_free(q);
}

void loop() {
    esp_deep_sleep_start();
}
```

## Update `libqrencode` procedure

- Update the subtree (fix the tag in the following command)

```sh
git subtree pull --prefix libqrencode https://github.com/fukuchi/libqrencode.git v4.1.1 --squash
```

- Update `library.json`
  - a new platformio version
  - the correct version defines for libqrencode
- Update this `README.md`'s git subtree command right above.
- Send me a PR

## Credits

- [libqrencode](https://github.com/fukuchi/libqrencode)