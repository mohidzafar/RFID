#include <SPI.h>
#include <MFRC522.h>

#define RST_PIN 9
#define SS_PIN 10
#define RELAY_PIN 7

MFRC522 mfrc522(SS_PIN, RST_PIN);

// Your RFID card UID
byte allowedUID[] = {0x83, 0x80, 0x56, 0x2A};

void setup() {
  Serial.begin(9600);
  SPI.begin();              
  mfrc522.PCD_Init();       
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, LOW); // Make sure relay is off
  Serial.println("Scan your RFID card...");
}

void loop() {
  // Look for a card
  if (!mfrc522.PICC_IsNewCardPresent() || !mfrc522.PICC_ReadCardSerial())
    return;

  Serial.print("Card UID: ");
  for (byte i = 0; i < mfrc522.uid.size; i++) {
    Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
    Serial.print(mfrc522.uid.uidByte[i], HEX);
  }
  Serial.println();

  // Check UID match
  bool match = true;
  for (byte i = 0; i < 4; i++) {
    if (mfrc522.uid.uidByte[i] != allowedUID[i]) {
      match = false;
      break;
    }
  }

  if (match) {
    Serial.println("✅ Access Granted - Relay ON");
    digitalWrite(RELAY_PIN, HIGH); // Turn relay ON
    delay(5000);                    // Keep it ON for 5 seconds
    digitalWrite(RELAY_PIN, LOW);  // Turn relay OFF
  } else {
    Serial.println("❌ Access Denied");
  }

  // Halt PICC
  mfrc522.PICC_HaltA();
}
