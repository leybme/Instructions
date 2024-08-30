*** Pin define for M5 Dial module ***
```cpp
//Define pin for M5 Dial module
// Created by: Nguyen Le Y

#ifndef _M5DIAL_H_
#define _M5DIAL_H_

#define GI 1   //[PORT B]
#define GO 2 //[PORT B]
#define beep 3
#define LCD_RS 4 // DC
#define LCD_MOSI 5
#define LCD_SCK 6
#define LCD_CS 7
#define LCD_RST 8
#define LCD_BL 9 // Backlight. HIGH = on, LOW = off
#define RC522_INT 10
#define TP_SDA 11
#define TP_SCL 12
#define SDA 13 //[PORT A]
#define TP_INT 14
#define SCL 15//[PORT A]
#define HOLD 46
#define TX 43
#define RX 44
#define ENCODERA 41
#define ENCODERB 40
#define WAKE_UP 42 // Encoder button
/*
#M5 Dial use 
***Encoder via A B
***RTC8563 via TP_SCL SDA RTC8563 is a CMOS real-time clock/calendar optimized for low power consumption
***WS1850S RFID2 connect to TP_SCL SDA
***GC9A01 LCD driver and FT3237 touch driver
*/
#endif
```