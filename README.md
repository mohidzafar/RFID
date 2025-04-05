#include <SPI.h>
#include <MFRC522.h>

#define SS_PIN 10   // SDA
#define RST_PIN 9   // RST

MFRC522 mfrc522(SS_PIN, RST_PIN);  // Create MFRC522 instance

void setup() {
  Serial.begin(9600);   // Start serial communication
  SPI.begin();          // Init SPI bus
  mfrc522.PCD_Init();   // Init RFID reader
  Serial.println("Scan an RFID card/tag...");
}

void loop() {
  // Check if a card is present
  if (!mfrc522.PICC_IsNewCardPresent()) return;

  // Read the card's serial number
  if (!mfrc522.PICC_ReadCardSerial()) return;

  Serial.print("Card UID: ");
  for (byte i = 0; i < mfrc522.uid.size; i++) {
    if (mfrc522.uid.uidByte[i] < 0x10) Serial.print("0");
    Serial.print(mfrc522.uid.uidByte[i], HEX);
    Serial.print(" ");
  }
  Serial.println();

  // Halt the card
  mfrc522.PICC_HaltA();
  mfrc522.PCD_StopCrypto1();
}









#include <MFRC522.h>
#include <SPI.h>

#define RST_PIN
#define SS_PIN 

string card_uid = "";

MFRC522 mfrc522(RST_PIN, SS_PIN);

void setup() {
    Serial.begin(9600);
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

    mfrc522.PICC_HaltA();
    mfrc522.PCC_StopCrypto1();
}



















#include <Adafruit_GFX.h>        
#include <MCUFRIEND_kbv.h>       

MCUFRIEND_kbv tft;

const String allowed_uid = "A0B1C2D3";
String incoming_uid = "";


void setup() {
    Serial.begin(9600);
    uint16_t ID = tft.readID();
    tft.begin(ID);
    tft.setRotation(1);
    tft.fillScreen(BLUE);
    tft.setTextColor(YELLOW);
    tft.setTextSize(10);
    tft.setCursor(200, 120);
    tft.print("SCANIF");
    delay(1500);
    tft.fillScreen(BLACK);
    tft.setTextColor(WHITE);
    tft.setTextSize(3);
    tft.setCursor(20, 50);
    tft.print("Scan Your Card.....");
}    

void loop() {
    if(Serial.available()) {
        incoming_uid = Serial.readStringUntil('\n);
        incoming_uid.trim();
        incoming_uid.toUpperCase();

        tft.fillScreen(BLACK);
        delay(500);

        if (incoming_uid == allowed_uid) {
            tft.drawRect(0, 0, 480, 320, GREEN);
            tft.drawRect(140, 110, 200, 100, GREEN);
            tft.setCursor(20, 130);
            tft.print("ACCESS GRANTED");
        }
        else {
            tft.drawRect(0, 0, 480, 320, RED);
            tft.drawRect(140, 110, 200, 100, RED);
            tft.setCursor(20, 130);
            tft.print("ACCESS DENIED");
        }

        tft.fillScreen(BLACK);
    }
}
