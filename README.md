#include <SPI.h>
#include <MFRC522.h>

#define RST_PIN 9
#define SS_PIN 10
#define RELAY_PIN 7

MFRC522 mfrc522(SS_PIN, RST_PIN);

// Replace this with your card UID
byte allowedUID[] = {0xDE, 0xAD, 0xBE, 0xEF};

void setup() {
  Serial.begin(9600);
  SPI.begin();
  mfrc522.PCD_Init();
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, LOW); // Relay off
  Serial.println("Place your card to verify...");
}

void loop() {
  // Check for a new card
  if (!mfrc522.PICC_IsNewCardPresent()) return;
  if (!mfrc522.PICC_ReadCardSerial()) return;

  Serial.print("Card UID: ");
  for (byte i = 0; i < mfrc522.uid.size; i++) {
    Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
    Serial.print(mfrc522.uid.uidByte[i], HEX);
  }
  Serial.println();

  // Compare UID
  bool match = true;
  for (byte i = 0; i < mfrc522.uid.size; i++) {
    if (mfrc522.uid.uidByte[i] != allowedUID[i]) {
      match = false;
      break;
    }
  }

  if (match) {
    Serial.println("✅ Access Granted!");
    digitalWrite(RELAY_PIN, HIGH); // Turn ON relay
    delay(5000);                   // Keep it ON for 5 seconds
    digitalWrite(RELAY_PIN, LOW);  // Turn OFF relay
  } else {
    Serial.println("❌ Access Denied!");
  }

  mfrc522.PICC_HaltA();
}
