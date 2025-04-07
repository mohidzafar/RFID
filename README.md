#include <MFRC522.h>
#include <SPI.h>

#define RST_PIN 9
#define SS_PIN 10

#define RELAY_PIN 8

#define BUZZER_PIN 7

// #define GREEN_LED 0
// #define RED_LED 1

String card_uid = "";

String allowed_uid_1 = "8380562A";

MFRC522 mfrc522(RST_PIN, SS_PIN);

void setup() {
    Serial.begin(9600);
    pinMode(RELAY_PIN, OUTPUT);
    pinMode(GREEN_LED, OUTPUT);
    pinMode(RED_LED, OUTPUT);
    SPI.begin();
    mfrc522.PCD_Init();

    Serial.println("Scan your Card......");
}

void loop() {
    if (!mfrc522.PCC_IsNewCardPresent()) {
        return;
    }

    if (!mfrc522.PCC_ReadCardSerial()) {
        return;
    }

    card_uid = "";

    
    for (byte i = 0; i < mfrc522.uid.size; i++) {
        if(mfrc522.uid.uidByte[i] < 0x10) {
            card_uid += "0";
        }
        card_uid += String(mfrc522.uid.uidByte[i], HEX);
        card_uid.toUpperCase();

        Serial.println(card_uid);
    }

    if (card_uid == allowed_uid_1) {
        digitalWrite(RELAY_PIN, HIGH);
        delay(500);
        digitalWrite(RELAY_PIN, LOW);
        delay(500);
        delay(500);
    }
    else {
        digitalWrite(BUZZER_PIN, HIGH);
        delay(1000);
        digitalWrite(BUZZER_PIN,LOW);
        delay(1000);
    }

    mfrc522.PICC_HaltA();
    mfrc522.PCC_StopCrypto1();
}
