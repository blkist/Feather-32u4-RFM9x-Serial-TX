#include <SPI.h>
#include <RH_RF95.h>

// for feather32u4
#define RFM95_CS 8
#define RFM95_RST 4
#define RFM95_INT 7
#define LED_BUILTIN 13

// must match RX's frequency!
#define RF95_FREQ 433.0
 
// singleton instance of the radio driver
RH_RF95 rf95(RFM95_CS, RFM95_INT);

char inData[100]; // Allocate some space for the string
char inChar=-1; // Where to store the character read
byte index = 0; // Index into array; where to store the character

void setup() {
  Serial.begin(115200);
  Serial1.begin(9600);
  pinMode(LED_BUILTIN, OUTPUT);
  pinMode(RFM95_RST, OUTPUT);
  digitalWrite(RFM95_RST, HIGH);

  // manual reset
  digitalWrite(RFM95_RST, LOW);
  delay(10);
  digitalWrite(RFM95_RST, HIGH);
  delay(10);

  while (!rf95.init()) {
    Serial.println("LoRa radio init failed");
    while (1);
  }
  Serial.println("LoRa radio init OK!");
 
  // Defaults after init are 434.0MHz, modulation GFSK_Rb250Fd250, +13dbM
  if (!rf95.setFrequency(RF95_FREQ)) {
    Serial.println("setFrequency failed");
    while (1);
  }
  Serial.print("Set Freq to: "); Serial.println(RF95_FREQ);
  
  // Defaults after init are 434.0MHz, 13dBm, Bw = 125 kHz, Cr = 4/5, Sf = 128chips/symbol, CRC on
 
  // The default transmitter power is 13dBm, using PA_BOOST.
  // If you are using RFM95/96/97/98 modules which uses the PA_BOOST transmitter pin, then 
  // you can set transmitter powers from 5 to 23 dBm:
  rf95.setTxPower(23, false);
}

void loop() {  

  
  // READ FROM SERIAL
  while (Serial.available() > 0) { 
    if(index < 99) { // one less than the size of the array 
      inChar = Serial.read(); // read a character
      inData[index] = inChar; // store it
      index++; // increment where to write next
      inData[index] = '\0'; // null terminate the string  
    }
  }

  // RECEIVE FROM RADIO
  if (rf95.available()) {
    uint8_t buf[RH_RF95_MAX_MESSAGE_LEN];
    uint8_t len = sizeof(buf);
    
    if (rf95.recv(buf, &len)) { // receive data 
      Serial.print((char*)buf); // print that data in serial console
    }
  }

  // SEND TO RADIO
  if (index > 0){ // Only send data if it hasn't been flushed
    rf95.send((uint8_t*)inData,100); // send the data
    index=0; // Flush indexed data
  }
}
