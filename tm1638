/* 
 tm1638 
 https://ascii.cl/conversion.htm
 
 hex and dex codes for the led's
    led1 = 0xc1 193
    led2 = 0xc3 195
    led3 = 0xc5 197
    led4 = 0xc7 199
    led5 = 0xc9 201
    led6 = 0xcb 203
    led7 = 0xcd 205
    led8 = 0xcf 207

    8digit1 = 0xc0  192
    8digit2 = 0xc2  194
    8digit3 = 0xc4  196
    8digit4 = 0xc6  198
    8digit5 = 0xc8  200
    8digit6 = 0xca  202
    8digit7 = 0xcc  204
    8digit8 = 0xce  206

    button1 = 1
    button2 = 2
    button3 = 4
    button4 = 8
    button5 = 16
    button6 = 32
    button7 = 64
    button8 = 128
    
    first select a led and then tell it what to do. lightup and what to lightup etc
    
    setLed(value, position) 
    setLed(1, 1)  = first led on
    setLed(0, 1)  = first led off

    setDigit(value, position) 
    setDigit(255, 1)  = first digit all on
    setDigit(0, 1)  = first digit all on

*/
// begin define
const int strobe = 7;
const int clock = 9;
const int data = 8;
// end define

void sendCommand(uint8_t value) {
  digitalWrite(strobe, LOW);
  shiftOut(data, clock, LSBFIRST, value);
  digitalWrite(strobe, HIGH);
}

void reset() {
  sendCommand(0x40); // set auto increment mode
  digitalWrite(strobe, LOW);
  shiftOut(data, clock, LSBFIRST, 0xc0);   // set starting address to 0
  for(uint8_t i = 0; i < 16; i++) {
    shiftOut(data, clock, LSBFIRST, 0x00);
  }
  digitalWrite(strobe, HIGH);
}

void setLed(uint8_t value, uint8_t position) {
  // begin value 0xc1 (193), this is the first led
  pinMode(data, OUTPUT);
  sendCommand(0x44);
  digitalWrite(strobe, LOW);                                    // strobe must be low for writing
  shiftOut(data, clock, LSBFIRST, 0xc1 + (position << 1));      // picking the write led
  shiftOut(data, clock, LSBFIRST, value);                       // turn it on or off
  digitalWrite(strobe, HIGH);                                   // strobe high is disable writing
}

void setDigit(uint8_t value, uint8_t position) {
  // begin value 0xc0 (192), this is the first 8digit
  pinMode(data, OUTPUT);
  sendCommand(0x44);
  digitalWrite(strobe, LOW);                                    // strobe must be low for writing
  shiftOut(data, clock, LSBFIRST, 0xc0 + (position << 1));      // select a digit
  shiftOut(data, clock, LSBFIRST, value);                       // which led has to go on or off (225 is all)
  digitalWrite(strobe, HIGH);                                   // strobe high is disable writing
}


void ledloop() {
  // led loop
  for (int i=0;i<8;i++) {
    setLed(1, i);                                               // led on
    delay(100);
    setLed(0, i);                                               // led off
  }

  // digit loop
  for (int i=0;i<8;i++) {
    setDigit(255, i);                                           // digit on
    delay(100);
    setDigit(0, i);                                             // digit off
  }  
}

void showHello() {
  setDigit(0x80, 0);
  setDigit(0x80, 1);
  setDigit(0x76, 2);
  setDigit(0x79, 3);
  setDigit(0x38, 4);
  setDigit(0x38, 5);
  setDigit(0x3f, 6);
  setDigit(0x80, 7);
  delay(1000);
}

uint8_t getButtons(void) {
  uint8_t buttons = 0;
  digitalWrite(strobe, LOW);
  shiftOut(data, clock, LSBFIRST, 0x42);
  pinMode(data, INPUT);

  for (uint8_t i = 0; i < 4; i++) {
    uint8_t v = shiftIn(data, clock, LSBFIRST) << i;
    buttons |= v;
  }

  pinMode(data, OUTPUT);
  digitalWrite(strobe, HIGH);
  return buttons;
}

void setup() {
  Serial.begin(9600);
  //Serial.println();
  pinMode(strobe, OUTPUT);
  pinMode(clock, OUTPUT);
  pinMode(data, OUTPUT);

  sendCommand(0x8f);  // activate
  reset();
  sendCommand(0x44);  // set single address

  ledloop();                            // demo loop
  showHello();                          // demo hello
}

void loop() {
  uint8_t buttons = getButtons();
  for(uint8_t position = 0; position < 8; position++) {
    uint8_t mask = 0x1 << position;

    setLed(buttons & mask ? 1 : 0,position);
    setDigit(buttons & mask ? 255 : 0, position);
  }
}
