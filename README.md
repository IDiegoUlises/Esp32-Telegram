# Esp32-Telegram

### Codigo Original Copiado
```c++
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>

// Wifi network station credentials
#define WIFI_SSID "YOUR WIFI SSID"
#define WIFI_PASSWORD "YOUR WIFI PASSWORD"
// Telegram BOT Token (Get from Botfather)
#define BOT_TOKEN "YOUR BOT TOKEN"
#define CHAT_ID "YOUR CHAT ID"
const unsigned long BOT_MTBS = 1000; // mean time between scan messages
WiFiClientSecure secured_client;
UniversalTelegramBot bot(BOT_TOKEN, secured_client);
unsigned long bot_lasttime; // last time messages' scan has been done
const int ledPin = 2;
int ledStatus = 0;
void handleNewMessages(int numNewMessages)
{
  Serial.print("handleNewMessages ");
  Serial.println(numNewMessages);
  for (int i = 0; i < numNewMessages; i++)
  {
    String chat_id = String(bot.messages[i].chat_id);
    if (chat_id != CHAT_ID )
    {
      bot.sendMessage(chat_id, "Unauthorized user", "");
    }
    else
    {
      String text = bot.messages[i].text;
      String from_name = bot.messages[i].from_name;
      if (from_name == "")
        from_name = "Guest";
      if (text == "/ledon")
      {
        digitalWrite(ledPin, HIGH); // turn the LED on (HIGH is the voltage level)
        ledStatus = 1;
        bot.sendMessage(chat_id, "Led is ON", "");
      }
      if (text == "/ledoff")
      {
        ledStatus = 0;
        digitalWrite(ledPin, LOW); // turn the LED off (LOW is the voltage level)
        bot.sendMessage(chat_id, "Led is OFF", "");
      }
      if (text == "/status")
      {
        if (ledStatus)
        {
          bot.sendMessage(chat_id, "Led is ON", "");
        }
        else
        {
          bot.sendMessage(chat_id, "Led is OFF", "");
        }
      }
      if (text == "/start")
      {
        String welcome = "Welcome to Universal Arduino Telegram Bot library, " + from_name + ".\n";
        welcome += "This is Flash Led Bot example.\n\n";
        welcome += "/ledon : to switch the Led ON\n";
        welcome += "/ledoff : to switch the Led OFF\n";
        welcome += "/status : Returns current status of LED\n";
        bot.sendMessage(chat_id, welcome, "Markdown");
      }
    }
  }
}
void setup()
{
  Serial.begin(115200);
  Serial.println();
  pinMode(ledPin, OUTPUT); // initialize digital ledPin as an output.
  delay(10);
  digitalWrite(ledPin, LOW); // initialize pin as off (active LOW)
  // attempt to connect to Wifi network:
  Serial.print("Connecting to Wifi SSID ");
  Serial.print(WIFI_SSID);
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  secured_client.setCACert(TELEGRAM_CERTIFICATE_ROOT); // Add root certificate for api.telegram.org
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(500);
  }
  Serial.print("\nWiFi connected. IP address: ");
  Serial.println(WiFi.localIP());
}
void loop()
{
  if (millis() - bot_lasttime > BOT_MTBS)
  {
    int numNewMessages = bot.getUpdates(bot.last_message_received + 1);
    while (numNewMessages)
    {
      Serial.println("got response");
      handleNewMessages(numNewMessages);
      numNewMessages = bot.getUpdates(bot.last_message_received + 1);
    }
    bot_lasttime = millis();
  }
}
```
* Se necesita descarga las librerias ArduinoJson y telegram bot https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot

### Codigo de pruebas
```c++
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>

// Wifi network station credentials
#define WIFI_SSID "Wifi Home"
#define WIFI_PASSWORD "S4m4sw3n0s"
// Telegram BOT Token (Get from Botfather)
#define BOT_TOKEN "6651295482:AAHSOXNTzMyJmrj6nuQi7wskSMFatI8Uyks"
#define CHAT_ID "6615998413"

int led = 2;

WiFiClientSecure secured_client;
UniversalTelegramBot bot(BOT_TOKEN, secured_client);

void setup()
{
  Serial.begin(115200);
  pinMode(led, OUTPUT);

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  secured_client.setCACert(TELEGRAM_CERTIFICATE_ROOT); // Add root certificate for api.telegram.org
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(500);
  }
  Serial.print("\nWiFi connected. IP address: ");
  Serial.println(WiFi.localIP());


}

void loop()
{
  //Espera
  //delay(1000);

  String chat_id = String(bot.messages[0].chat_id);
  
  String text = bot.messages[0].text;
  String from_name = bot.messages[0].from_name;

  boolean newMensaje = bot.getUpdates(bot.last_message_received + 1);
  
  if (chat_id != CHAT_ID )
  {
    bot.sendMessage(chat_id, "No autorizado", "");
    //return; //salir
    Serial.println("No permitido");
  }

  else if (text == "/on")
  {
    digitalWrite(led, HIGH);
    bot.sendMessage(chat_id, "Led ESTA ENCENDIDO", "");
    Serial.println("Encendido");
  }

  else if (text == "/off")
  {
    digitalWrite(led, LOW);
    bot.sendMessage(chat_id, "Led ESTA apagado", "");
    Serial.println("Apagado");
  }

  else
  {
    
  }

  //Espera
  delay(1000);


}

```

### Codigo 2 ahora funciona solamente que cuando ingrese un usuario no autorizado debe no permitir ejecutar comandos
```c++
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>

// Wifi network station credentials
#define WIFI_SSID "Wifi Home"
#define WIFI_PASSWORD "S4m4sw3n0s"
// Telegram BOT Token (Get from Botfather)
#define BOT_TOKEN "6651295482:AAHSOXNTzMyJmrj6nuQi7wskSMFatI8Uyks"
#define CHAT_ID "6615998413"

int led = 2;

WiFiClientSecure secured_client;
UniversalTelegramBot bot(BOT_TOKEN, secured_client);

void setup()
{
  Serial.begin(115200);
  pinMode(led, OUTPUT);

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  secured_client.setCACert(TELEGRAM_CERTIFICATE_ROOT); // Add root certificate for api.telegram.org
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(500);
  }
  Serial.print("\nWiFi connected. IP address: ");
  Serial.println(WiFi.localIP());


}

void loop()
{
  //Espera
  //delay(1000);

  int numNewMessages = bot.getUpdates(bot.last_message_received + 1);

  for (int i = 0; i < numNewMessages; i++)
  {

    String chat_id = String(bot.messages[i].chat_id);

    String text = bot.messages[i].text;
    String from_name = bot.messages[i].from_name;

    Serial.println("Dentro del for");

    while (numNewMessages)
    {
      Serial.println("Dentro del while");

      if (chat_id != CHAT_ID )
      {
        bot.sendMessage(chat_id, "No autorizado", "");
        //return; //salir
        Serial.println("No permitido");
      }

      else if (text == "/on")
      {
        digitalWrite(led, HIGH);
        bot.sendMessage(chat_id, "Led ESTA ENCENDIDO", "");
        Serial.println("Encendido");
      }

      else if (text == "/off")
      {
        digitalWrite(led, LOW);
        bot.sendMessage(chat_id, "Led ESTA apagado", "");
        Serial.println("Apagado");
      }

      else
      {

      }
      //Actualiza el valor importante para salir del bucle while
      numNewMessages = bot.getUpdates(bot.last_message_received + 1);

    }

  }

  //Espera
  delay(1000);


}
```
