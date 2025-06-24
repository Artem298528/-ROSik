#include <Adafruit_NeoPixel.h>

#define LED_PIN 14
#define NUM_RING1 12  // Часы (по часовой стрелке)
#define NUM_RING2 6    // Десятки минут (против часовой)
#define NUM_LEDS (NUM_RING1 + NUM_RING2)

Adafruit_NeoPixel strip(NUM_LEDS, LED_PIN, NEO_GRB + NEO_KHZ800);

// Оранжевый цвет
uint32_t orangeColor = strip.Color(255, 50, 0);

// Текущее и целевое время
int targetHours = 0;
int targetMinutes = 0;
unsigned long startMillis = 0;
bool timeSet = false;

void setup() {
  Serial.begin(115200);
  while (!Serial); // Ждем подключения монитора
  
  strip.begin();
  strip.setBrightness(50);
  strip.show();
  
  Serial.println("\nСистема готова");
  Serial.println("Введите целевое время в формате HH:MM:");
}

void loop() {
  // Обработка ввода времени
  if (Serial.available() > 0) {
    setTargetTime();
  }
  
  // Если время установлено - отслеживаем его
  if (timeSet) {
    updateClock();
  }
  
  delay(100);
}

void setTargetTime() {
  String timeStr = Serial.readStringUntil('\n');
  timeStr.trim();
  
  if (timeStr.length() == 5 && timeStr.charAt(2) == ':') {
    targetHours = timeStr.substring(0, 2).toInt();
    targetMinutes = timeStr.substring(3, 5).toInt();
    
    if (targetHours >= 0 && targetHours < 24 && targetMinutes >= 0 && targetMinutes < 60) {
      startMillis = millis(); // Засекаем время ввода
      timeSet = true;
      
      Serial.print("Установлено время: ");
      printTime(targetHours, targetMinutes);
    } else {
      Serial.println("Ошибка: некорректное время");
    }
  } else {
    Serial.println("Ошибка: используйте формат HH:MM");
  }
}

void updateClock() {
  // Рассчитываем текущее виртуальное время
  unsigned long currentMillis = millis();
  unsigned long elapsedMinutes = (currentMillis - startMillis) / 60000;
  
  int currentHours = (targetHours + (elapsedMinutes + targetMinutes) / 60) % 24;
  int currentMinutes = (targetMinutes + elapsedMinutes) % 60;
  
  // Отображаем текущее время
  displayTime(currentHours, currentMinutes);
  
  // Выводим в монитор раз в секунду
  static unsigned long lastPrint = 0;
  if (currentMillis - lastPrint >= 1000) {
    lastPrint = currentMillis;
    Serial.print("Текущее время: ");
    printTime(currentHours, currentMinutes);
  }
}

void displayTime(int hours, int minutes) {
  strip.clear();
  
  // Часы (по часовой стрелке)
  int hourLed = hours % 12;
  strip.setPixelColor(hourLed, orangeColor);
  
  // Десятки минут (против часовой)
  int tens = minutes / 10;
  int minuteLed = NUM_RING1 + (5 - tens % 6);
  strip.setPixelColor(minuteLed, orangeColor);
  
  strip.show();
}

void printTime(int h, int m) {
  if (h < 10) Serial.print("0");
  Serial.print(h);
  Serial.print(":");
  if (m < 10) Serial.print("0");
  Serial.println(m);
}
