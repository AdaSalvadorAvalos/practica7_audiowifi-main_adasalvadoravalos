# Audiowifi: práctica 7

## Materiales:
- ESP32
- Amplificador de Audio Módulo decodificador I2S Dac sin filtrar
- SPI Reader Lector de Tarjeta SD
- Altavoz 

## Presentación:
En esta práctica también usamos el I2S, pero esta vez nos conectaremos a un host on-line para poder reproducir una emisora de radio.

## Explicación del código (con comentarios que explican el funcionamiento línea a línea):

```
//librerias
#include <Arduino.h>

#include "WiFi.h"
#include "Audio.h"
#include "SD.h"
#include "FS.h"

// pines digitales usados 
#define SD_CS          5
#define SPI_MOSI      23
#define SPI_MISO      19
#define SPI_SCK       18
#define I2S_DOUT      25
#define I2S_BCLK      27
#define I2S_LRC       26
//objeto de Audio
Audio audio;
//strings que definen la contraseña y ssid de la wi-fi que usaremos
String ssid = "Mi Ada";
String password = "telefono4ada";

void setup() {
    pinMode(SD_CS, OUTPUT);      digitalWrite(SD_CS, HIGH);
    SPI.begin(SPI_SCK, SPI_MISO, SPI_MOSI); //inicialice lector de SD
    Serial.begin(115200); //inicialice una comunicación en serie a una velocidad de transmisión de 115200
    SD.begin(SD_CS); //incialice SD
    WiFi.disconnect();
    WiFi.mode(WIFI_STA);
    WiFi.begin(ssid.c_str(), password.c_str()); //activa el wi-fi
    while (WiFi.status() != WL_CONNECTED) delay(1500); //si no se ha conectado sepere 1500
    audio.setPinout(I2S_BCLK, I2S_LRC, I2S_DOUT);
    audio.setVolume(21); //cammbiar el volumen de:  0...21

//    audio.connecttoFS(SD, "/320k_test.mp3"); 
    audio.connecttohost("http://www.wdr.de/wdrlive/media/einslive.m3u"); //conectarse al host de la emisora
   //  audio.connecttohost("http://macslons-irish-pub-radio.com/media.asx");
//    audio.connecttohost("http://mp3.ffh.de/radioffh/hqlivestream.aac"); //  128k aac
//    audio.connecttohost("http://mp3.ffh.de/radioffh/hqlivestream.mp3"); //  128k mp3
//    audio.connecttohost("https://github.com/schreibfaul1/ESP32-audioI2S/raw/master/additional_info/Testfiles/sample1.m4a"); // m4a
//    audio.connecttohost("https://github.com/schreibfaul1/ESP32-audioI2S/raw/master/additional_info/Testfiles/test_16bit_stereo.wav"); // wav
//    audio.connecttospeech("Wenn die Hunde schlafen, kann der Wolf gut Schafe stehlen.", "de");
//    audio.connecttospeech(" Introduzca pasta,  y pulse boton para Jugar, perdedor, jajaja.", "es");
}

void loop()
{
    audio.loop();
}

// optional: aqui tenemos serial.print con información del archivo de la SD y del host
void audio_info(const char *info){
    Serial.print("info        "); Serial.println(info);
}
void audio_id3data(const char *info){  //id3 metadata
    Serial.print("id3data     ");Serial.println(info);
}
void audio_eof_mp3(const char *info){  //end of file
    Serial.print("eof_mp3     ");Serial.println(info);
}
void audio_showstation(const char *info){
    Serial.print("station     ");Serial.println(info);
}
void audio_showstreamtitle(const char *info){
    Serial.print("streamtitle ");Serial.println(info);
}
void audio_bitrate(const char *info){
    Serial.print("bitrate     ");Serial.println(info);
}
void audio_commercial(const char *info){  //duration in sec
    Serial.print("commercial  ");Serial.println(info);
}
void audio_icyurl(const char *info){  //homepage
    Serial.print("icyurl      ");Serial.println(info);
}
void audio_lasthost(const char *info){  //stream URL played
    Serial.print("lasthost    ");Serial.println(info);
}
void audio_eof_speech(const char *info){
    Serial.print("eof_speech  ");Serial.println(info);
}

```

Ver vídeos para observar la salida.

