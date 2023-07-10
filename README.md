#include <Keypad.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

const byte ROWS = 4;
const byte COLS = 4;

char keys[ROWS][COLS] =
{
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};

byte rowPins[ROWS] = {0, 1, 2, 3};
byte colPins[COLS] = {4, 5, 6, 7};

Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

void welcome()
{
  lcd.clear();
  lcd.setCursor(4, 0);
  lcd.print("Welcome");
  delay(1000);
  lcd.clear();
}

int mode()
{
  char mode_c = '\0';
  int mode_n = 0;

  lcd.setCursor(0, 0);
  lcd.print("1- auto mode");
  lcd.setCursor(0, 1);
  lcd.print("2- mirror mode");
  while (1)
  {
    mode_c = keypad.getKey();
    if (mode_c == '1' || mode_c == '2')
    {
      String mode_s = String(mode_c);
      mode_n = mode_s.toInt();
      lcd.clear();
      return (mode_n);
    }
    if (keypad.getKey() == 'B')
    {
      break;
    }
  }
}

int flexion_time()
{
  char key1 = '\0';
  int number1 = 0;
  
  lcd.print("Flexion Time: ");
  
  while (1)
  {
    key1 = keypad.getKey();
    
    if (key1)
    {
      String string_one = String(key1);
      number1 = string_one.toInt();
      lcd.setCursor(0, 1);
      lcd.print(key1);
      lcd.setCursor(2, 1);
      lcd.print("second");
      delay(1000);
      lcd.clear();
      return number1;
    }
    if (keypad.getKey() == 'B')
    {
      break;
    }
  }
}

int relaxation_time()
{
  char key2 = '\0';
  int number2 = 0;
  
  lcd.print("Relaxation Time:");
  
  while (1)
  {
    key2 = keypad.getKey();
    
    if (key2)
    {
      String string_two = String(key2);
      number2 = string_two.toInt();
      lcd.setCursor(0, 1);
      lcd.print(key2);
      lcd.setCursor(2, 1);
      lcd.print("second");
      delay(1000);
      lcd.clear();
      return number2;
    }
    if (keypad.getKey() == 'B')
    {
      break;
    }
  }
}

int waiting_time()
{
  char key3 = '\0';
  int number3 = 0;
  
  lcd.print("Total Time:");
  
  while (1)
  {
    key3 = keypad.getKey();
    
    if (key3)
    {
      String string_three = String(key3);
      number3 = string_three.toInt();
      lcd.setCursor(0, 1);
      lcd.print(key3);
      lcd.setCursor(2, 1);
      lcd.print("minute");
      delay(1000);
      lcd.clear();
      return number3 * 60;
    }
    if (keypad.getKey() == 'B')
    {
      break;
    }
  }
}

void motor(int number1, int number2, int number3)
{
  lcd.setCursor(0, 0);
  lcd.print("Please wait:");

  while (number3 >= 0)
  {
    if (number3 < 0)
    {
      break;
    }

    lcd.setCursor(0, 1);
    lcd.print(String(number3 / 60) + "m " + String(number3 % 60) + "s ");

    if (number1 > 0)
    {
      digitalWrite(13, HIGH);
      delay(number1 * 1000);
      number3 -= number1;
    }

    if (number3 < 0)
    {
      break;
    }

    lcd.setCursor(0, 1);
    lcd.print(String(number3 / 60) + "m " + String(number3 % 60) + "s ");

    if (number2 > 0)
    {
      digitalWrite(13, LOW);
      delay(number2 * 1000);
      number3 -= number2;
    }

    if (number3 < 0)
    {
      break;
    }
    
    lcd.setCursor(0, 1);
    lcd.print(String(number3 / 60) + "m " + String(number3 % 60) + "s ");

    if (keypad.getKey() == 'B')
    {
      break;
    }
    
  }

}

void mirror_mode()
{
  lcd.setCursor(0, 0);
  lcd.print("mirror mode");
  pinMode(12, INPUT);
  while (1)
  {
    if (digitalRead(12) == HIGH)
    {
      digitalWrite(13, HIGH);
    }
    if (digitalRead(12) == LOW)
    {
      digitalWrite(13, LOW);
    }
    if (keypad.getKey() == 'B')
    {
      break;
    }
  }
}

void setup()
{
  lcd.init();
  lcd.begin(16, 2);
  lcd.backlight();
  pinMode(13, OUTPUT);
}

void loop()
{
  int mode_n = 0, number1 = 0, number2 = 0, number3 = 0;

  digitalWrite(13, LOW);
  
  welcome();

  mode_n = mode();
  if (mode_n == 1)
  {
    number1 = flexion_time();

    number2 = relaxation_time();

    number3 = waiting_time();

    motor(number1, number2, number3);
  }
  else
  {
    mirror_mode();
  }

  digitalWrite(13, LOW);
}
