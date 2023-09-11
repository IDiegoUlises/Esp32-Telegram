# Esp32-Telegram

* Se necesita descarga las librerias ArduinoJson que se puede descargar desde el aide de Arduino y telegram bot https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot parece que telgram bot tambien se puede descargar desde el aide de arduino

### Codigo Optimizado que funciona
```c++
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>

// Wifi Credenciales
#define WIFI_SSID "Wifi Home"
#define WIFI_PASSWORD "S4m4sw3n0s"

//Telegram Bot Token
#define BOT_TOKEN "6651295482:AAHSOXNTzMyJmrj6nuQi7wskSMFatI8Uyks"

//Identificador del usuario que puede utilizar el bot
#define CHAT_ID "6615998413"

//Pin para controlar el Led
int led = 2;

//Objeto WifiClientSecure y Bot
WiFiClientSecure secured_client;
UniversalTelegramBot bot(BOT_TOKEN, secured_client);

void setup()
{
  //Inicia el puerto serial
  Serial.begin(115200);

  //Inicializa el led
  pinMode(led, OUTPUT);

  //Se conecta la a red wifi, en caso de no conectarse quedara en un bucle infinito
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  secured_client.setCACert(TELEGRAM_CERTIFICATE_ROOT); //Agrega el certificado
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(500);
  }

  //Imprime la direccion IP
  Serial.print("\nWiFi connected. IP address: ");
  Serial.println(WiFi.localIP());
}

void loop()
{
  //Obtiene el numero de los mensajes recibidos
  int newMensaje = bot.getUpdates(bot.last_message_received + 1);

  //Bucle que recorre los mensajes
  for (int i = 0; i < newMensaje; i++)
  {
    //Obtiene el CHAT_ID del usuario de telegram
    String chat_id = String(bot.messages[i].chat_id);

    //Obtiene el mensaje que el usuario envia
    String text = bot.messages[i].text;

    //Obtiene el nombre de usuario
    String from_name = bot.messages[i].from_name;

    //Inicia el bucle cuando se recibe un nuevo mensaje
    while (newMensaje)
    {
      //Solo se ejecuta un solo if de todos por eso el primer if es la verificacion de usuario
      //no permite el uso de comandos si el usuario es diferente
      if (chat_id != CHAT_ID )
      {
        bot.sendMessage(chat_id, "No autorizado", "");
        Serial.println("No permitido");
      }

      //Si el usuario envia el comando /on se ejecutara la siguiente accion
      else if (text == "/on")
      {
        digitalWrite(led, HIGH);
        bot.sendMessage(chat_id, "Led Esta ENCENDIDO", "");
        Serial.println("Led Encendido");
      }

      //Si el usuario envia el comando /off se ejecutara la siguiente accion
      else if (text == "/off")
      {
        digitalWrite(led, LOW);
        bot.sendMessage(chat_id, "Led esta APAGADO", "");
        Serial.println("Led Apagado");
      }

      //En caso que no exista el comando
      else
      {
        bot.sendMessage(chat_id, "Comando no disponible", "");
        Serial.println("Comando no disponible");
      }

      //Actualiza el valor de nuevos mensajes es importante para salir del bucle while
      newMensaje = bot.getUpdates(bot.last_message_received + 1);
    }

  }

  //Espera un segundo
  delay(1000);
}
```
