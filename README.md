# GRG_Subway_Check_in_System
This is description for subway check-in system progress
go to [here](https://github.com/tuananhvip/GRG_Subway_Check_in_System) for newest detail

# 1. Data transfer detail
When send control command to serial port of running machine, system will send data to embedded system. Here is commands

    . Start receiving data: `Start;`
    . Stop receiving data:  `Stop;`
    . Data structure: [STX][FrameID]-[Ntotal]-[Ncurrent]-(ID0x0y0 ID1x1y1 ... IDnXnYn)[CRC16][END]
        where:
            - STX:0x02: 1 byte, start frame
            - Ntotal:   4 chars: uint16_t:  index of data frame
            - Ncurrent: 2 chars: uint8_t:   total number of detected people
            - IDi:      3 chars: uint16_t: ID tracking person i-th
            - xi,yi:    3 chars: uint16_t: center point position of detected people, i=0,..n-1            
            - CRC16:    4 char:  uint16_t: calculate CRC-16 bit for content
            - END: 0x03:1 byte:  end of frame
        note: in data frame, NO space, all char number in HEX format
        
        Examples:       
            [2] 37-0064-01(0020771FD)E9AA [3]
            [2] 38-0065-01(0020771FB)B73C [3]
            [2] 39-0066-01(0020771F8)0761 [3]
            [2] 3A-0067-01(0020771F7)850F [3]
            [2] 3B-0068-01(0020771F6)2205 [3]
            [2] 3C-006A-02(0020771F50030C9232)F7D2 [3]
            [2] 3D-006C-02(0020771F30030C8233)4009 [3]
            [2] 3E-006E-02(0020771F20030C7234)3350 [3]
            [2] 3F-0070-02(0020731F00030C6235)8D3F [3]
            [2] 40-0072-02(0020721F00030C6235)8602 [3]
            
            
<p>&nbsp;</p>

# 2. How to run?
## Prepare program
1. Copy `subway.tar.gz` to "Running machine"
2. extract here: `tar -xzf subway.tar.gz` or using mouse to extract

## Run program
Run: `bash grg-subway.sh` to run program, requring password: __system signin password__


<p>&nbsp;</p>


# 3. What's New?
## 2019-07-14: verson 1.1
### Feature
    - Add tracking people feature, now each frame, people in one half right area will be track (go from right to left)
    - People pass: it will create 'pass' person when a person go through the center of the doorway
    - Change training mathod: load previous training weight to continues training.
    


## 2019-07-05: verson 1.0, first time submit program
### Feature

    - Support 1 to 4 webcam running in one frame
    - Support change webcam position
    - support rotate webcam 180 degree
    - support send & receive data through serial port
    - support change serial port parametter
    
### Data transfer detail
When send control command to serial port of running machine, system will send data to embedded system. Here is commands

    . Start receiving data: `Start;`
    . Stop receiving data:  `Stop;`
    . Data structure: `:I:N:x0,y0:x1,y1: ... :x(n-1),y(n-1);`
        where:
            - I: index of data frame
            - N: total number of detected people
            - xi,yi: center point position of detected people, i=0,..n-1
        note: in data frame, contain space for easy debug.
        
      examples:       
        :192:5: 114, 366: 116, 120: 387, 353: 546, 153: 269, 113;\n
        :193:6: 116, 122: 114, 366: 383, 353: 546, 153: 263, 112: 529, 379;\n
        :194:5: 116, 122: 110, 368: 383, 353: 546, 151: 265, 112;\n
        :195:4: 117, 124: 110, 367: 383, 353: 545, 151;\n
        :196:5: 116, 125: 110, 369: 379, 353: 532, 374: 252, 113;\n
        :197:5: 115, 127: 110, 369: 379, 353: 532, 374: 241, 113;\n
        :198:6: 115, 127: 108, 371: 368, 350: 546, 136: 241, 113: 549, 371;\n
        :199:6: 115, 127: 108, 371: 368, 350: 546, 136: 241, 113: 549, 371;\n
        :200:6: 114, 130: 546, 135: 105, 374: 362, 350: 549, 370: 237, 113;\n
        :201:6: 113, 133: 546, 135: 362, 350: 105, 374: 549, 370: 232, 112;\n
        :202:7: 113, 133: 551, 369: 546, 132:  90, 378: 358, 354: 232, 112: 186, 360;\n    



# 4. How to build?
This part for deverloper only:
All things to do: `bash 2-run-build.sh`

Contents:
1. Run normally: `python subway.py` to ensure every things ok
2. Run `pyinstaller subway.py` to build
3. Copy folder `data` paste inside folder `dist/subway`
    results:
    
    ```
    ~/subway/data/
                  ├── labels
                  ├── obj
                  ├── obj.data
                  ├── obj.names
                  ├── yolov3-tiny-videos.cfg
                  └── yolov3-tiny-videos.weights
    ```
4. Create running script: `grg-subway.sh`
5. Compress `subway` folder, copy to running machine


# 5. Arduino part: How to receive?

```c++

#define LCD_CS A3 // Chip Select goes to Analog 3
#define LCD_CD A2 // Command/Data goes to Analog 2
#define LCD_WR A1 // LCD Write goes to Analog 1
#define LCD_RD A0 // LCD Read goes to Analog 0
#define LCD_RESET A4 // Can alternately just connect to Arduino's reset pin

//#include <SPI.h>          // f.k. for Arduino-1.5.2
#include "Adafruit_GFX.h"// Hardware-specific library
#include <MCUFRIEND_kbv.h>
MCUFRIEND_kbv tft;

// Assign human-readable names to some common 16-bit color values:
#define	BLACK   0x0000
#define	BLUE    0x001F
#define	RED     0xF800
#define	GREEN   0x07E0
#define CYAN    0x07FF
#define MAGENTA 0xF81F
#define YELLOW  0xFFE0
#define WHITE   0xFFFF

#ifndef min
#define min(a, b) (((a) < (b)) ? (a) : (b))
#endif

void setup(void);
void loop(void);
uint16_t crc16(const uint8_t* buff, size_t size);
uint16_t crc16_from_string(String buff, size_t size);
//-------------------------------------------------------------
//-------------------------------------------------------------
String inputString = "Bat dau!!!";         // a String to hold incoming data
String inputString_old = "";
bool stringComplete = true;  // whether the string is complete
bool FirstTimeRun = true;

//-------------------------------------------------------------
//-------------------------------------------------------------
void init_screen() {
  tft.fillScreen(BLACK);
  tft.setTextColor(CYAN); tft.setTextSize(2);
  tft.setCursor(0, 10);
  tft.println(" GRG Subway In-Out System");
  tft.setCursor(92, 40);
  tft.println("Version 1.1");

  tft.setCursor(115, 60); tft.setTextSize(1);
  tft.println("Nguyen Tuan Anh");
  tft.setTextColor(YELLOW); tft.setTextSize(2);

}
#define btAuto 53
#define btMenu 51
#define btOnOF 49
#define btMenu 51
#define btOnOF 49
#define btLED  30
int vLED=1;

void setup(void) {
  Serial.begin(115200);//57600);
  uint32_t when = millis();
  if (!Serial) delay(5000);           //allow some time for Leonardo
  Serial.println("GRG_subway_TFT24inch v1.1\n\r;");
  tft.begin(0x9341);
  pinMode(btAuto, INPUT_PULLUP);
  pinMode(btMenu, INPUT_PULLUP);
  pinMode(btOnOF, INPUT_PULLUP);
  pinMode(btLED,  OUTPUT);
  tft.setRotation(1);
  init_screen();
  inputString.reserve(200);
}

//-------------------------------------------------------------
//-------------------------------------------------------------
//-------------------------------------------------------------
//-------------------------------------------------------------
int vAuto0 = HIGH;
int vMenu0 = HIGH;
int vOnOF0 = HIGH;
int waitsending = 0;
const int WSt = 10;


#define cSTART 2
#define cSTOP  3
uint8_t  rcvFrameID;
uint16_t rcvNtotal;
uint8_t  rcvNcurrent;
String   rcvData;
uint16_t rcvCRC16=0,rcvCRC16_old;
String   sCRC16,sCRC16_old;
char     MGS[200],pt=0,cnt=0;
#define TOP 100
 
uint16_t crc16vl,crc16vl_old;
void taPrintTFT(int x,int y,uint16_t mau, String txt){
    tft.setCursor(x, y);
    tft.setTextColor(mau);
    tft.print(txt);
}
void taPrintTFT_hex(int x,int y,uint16_t mau, uint16_t NUM){
    tft.setCursor(x, y);
    tft.setTextColor(mau);
    tft.print(NUM,HEX);    
}

int StrToHex(String str){
  return strtol(str.c_str(), 0, 16);
}

int iCRC16,iCRC16_old;
void loop(void) {
  // Button:=======================================
  int vAuto = digitalRead(btAuto);
  int vMenu = digitalRead(btMenu);
  int vOnOF = digitalRead(btOnOF);

  if (waitsending > 0) {
    waitsending--;
  }
  if (waitsending == 0) {
    if ((vAuto0 == HIGH) && (vAuto == LOW)) {
      Serial.print("Start;"); waitsending = WSt;
    }
    if ((vMenu0 == HIGH) && (vMenu == LOW)) {
      Serial.print("Stop;"); waitsending = WSt;
    }
    if ((vOnOF0 == HIGH) && (vOnOF == LOW)) {
      init_screen();
    }    
  }
  vAuto0 = vAuto;
  vMenu0 = vMenu;
  vOnOF0 = vOnOF;
  // END Button:==================================
  
  if (stringComplete) {
    int oline=20;
    int cot1=150;
    taPrintTFT(0, TOP-oline,YELLOW, "String Receive:");
    taPrintTFT(0, TOP,BLACK , inputString_old);
    taPrintTFT(0, TOP,YELLOW, inputString);

    taPrintTFT(0,    4*oline+TOP,YELLOW, "CRC Recv:");
    taPrintTFT(cot1, 4*oline+TOP,BLACK , sCRC16_old);
    taPrintTFT(cot1, 4*oline+TOP,YELLOW, sCRC16);
    
    taPrintTFT(0,        5*oline+TOP,YELLOW, "CRC Calc:");
    taPrintTFT_hex(cot1, 5*oline+TOP,BLACK , crc16vl_old);
    taPrintTFT_hex(cot1, 5*oline+TOP,YELLOW, crc16vl);
    
    
    iCRC16= StrToHex(sCRC16);
    taPrintTFT(0,        6*oline+TOP,YELLOW, "CRC Number:");
    taPrintTFT_hex(cot1, 6*oline+TOP,BLACK, iCRC16_old);
    taPrintTFT_hex(cot1, 6*oline+TOP,GREEN, iCRC16);
    iCRC16_old=iCRC16;
    
    inputString_old = inputString;
    crc16vl_old=crc16vl;
    rcvCRC16_old=rcvCRC16;
    sCRC16_old=sCRC16;
    
    inputString = "";
    stringComplete = false;
    digitalWrite(btLED,vLED);vLED=~vLED;
  }
}
//-------------------------------------------------------------
//-------------------------------------------------------------
//-------------------------------------------------------------
//-------------------------------------------------------------
void serialEvent() {
  while (Serial.available()) {
    char inChar = (char)Serial.read();
    if (inChar == 0x02) {
      inputString="";
      stringComplete=false;
      continue;
    }
    if (inChar == 0x03) {
      stringComplete = true;
      uint16_t DatLen=inputString.length()-4;
      crc16vl =crc16_from_string(inputString, DatLen); 
      sCRC16  =inputString.substring(DatLen,DatLen+4);
      continue;
    }
    inputString += inChar;
  }
}
   

//-------------------------------------------------------------
//-------------------------------------------------------------
//-------------------------------------------------------------
//-------------------------------------------------------------


//#include <iostream>

//using namespace std;
//uint8_t Ser_Msg[]={0x9, 0x0, 0x1, 0x1, 0x30, 0x30, 0x30, 0x30, 0x42, 0x34, 0x32, 0x31, 0x37};
uint16_t crc16(const uint8_t* buff, size_t size)
{
    // Check: http://www.sunshine2k.de/coding/javascript/crc/crc_js.html
    uint8_t* data = (uint8_t*)buff;
    uint16_t result = 0xFFFF;
    for (size_t i = 0; i < size; ++i){
        result ^= data[i];
        for (size_t j = 0; j < 8; ++j){
            if (result & 0x01) result = (result >> 1) ^ 0xA001;
            else result >>= 1;
        }
    }
    return result;
}
uint16_t crc16_from_string(String buff, size_t size)
{
    // Check: http://www.sunshine2k.de/coding/javascript/crc/crc_js.html
    uint16_t result = 0xFFFF;
    for (size_t i = 0; i < size; ++i){
        result ^= buff[i];
        for (size_t j = 0; j < 8; ++j){
            if (result & 0x01) result = (result >> 1) ^ 0xA001;
            else result >>= 1;
        }
    }
    return result;
}
//
//int main(){
//    uint16_t crc16vl=crc16(Ser_Msg, sizeof(Ser_Msg));    
//    cout << std::hex << crc16vl;
//    return 0;
//}


```
