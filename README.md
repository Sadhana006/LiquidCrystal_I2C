#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Keypad.h>

// Initialize LCD at address 0x27
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Keypad setup
const byte ROWS = 4;
const byte COLS = 4;
char keys[ROWS][COLS] = {
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};
byte rowPins[ROWS] = {2, 3, 4, 5};
byte colPins[COLS] = {6, 7, 8, 9};
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

// Variables
String totalAmount = "";
String numPeople = "";
float share = 0.0;
int step = 0;

void setup() {
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Expense Splitter");
  delay(2000);
  lcd.clear();
  lcd.print("Total Amount:");
}

void loop() {
  char key = keypad.getKey();
  if (key) {
    if (key == '#') {
      if (step == 0 && totalAmount != "") {
        step = 1;
        lcd.clear();
        lcd.print("People Count:");
      } else if (step == 1 && numPeople != "") {
        float total = totalAmount.toFloat();
        int people = numPeople.toInt();
        if (people == 0) {
          lcd.clear();
          lcd.print("Can't divide by 0");
          delay(2000);
          lcd.clear();
          numPeople = "";
          lcd.print("People Count:");
          return;
        }
        share = total / people;
        lcd.clear();
        lcd.print("Each pays:");
        lcd.setCursor(0, 1);
        lcd.print(share, 2);
      }
    } else if (key == '*') {
      // Reset everything
      totalAmount = "";
      numPeople = "";
      step = 0;
      lcd.clear();
      lcd.print("Total Amount:");
    } else {
      if (step == 0) {
        totalAmount += key;
        lcd.setCursor(0, 1);
        lcd.print(totalAmount);
      } else if (step == 1) {
        numPeople += key;
        lcd.setCursor(0, 1);
        lcd.print(numPeople);
      }
    }
  }
}
