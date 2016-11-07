Hackathon BlueHack - Ecobox
==============
Time
--------------
- Diego Mendes Moreno
- Julio Cesar dos Santos
- Wilder Roberto Ramos Pereira
- Kesia Vasconcelos Ventura
- Daniel Garcia de Oliveira

# **Transcrição do código Arduino bluehack_ecobox.ino**

```c
/* Projeto    : bluehack_ecohack
 * Board      : Arduino MEGA 2560
 * Versão     : 1.0.0
 * Data	      : 06/11/2016
 * Descrição  : Código do arduino para o projeto Hackblue da IBM/DOW.
 */

/* Include */
#include <Servo.h>

/* Defines */
#define SERVO_TRANCADO     170
#define SERVO_DESTRANCADO  100
#define TEMPO_DESTRANCADO  5000
#define SERVO_OPERANDO     500
//Estados
#define START              0
#define BLUETOOTH_CONNECT  1
#define BLUETOOTH_RECV     2
#define ESPERA_ABERTO      3
#define TRANCA             4

/* Inicializações */
#define servo_signal  2
Servo servo_tranca;
#define LED_VERDE     53
#define LED_VERMELHO  52

/* Variáveis globais */
char estado = START;
char bt_readByte = 0;

void setup()
{
  Serial.begin(9600);
  servo_tranca.attach(servo_signal);   //configuração do servo
  delay(SERVO_OPERANDO);
  servo_tranca.write(SERVO_TRANCADO);  //Posição inicial
  delay(SERVO_OPERANDO);
  pinMode(LED_VERDE,OUTPUT);
  pinMode(LED_VERMELHO,OUTPUT);
}

void loop()
{
  switch(estado)
  {
    case START :
      Serial.println("Estado - START");
      estado = BLUETOOTH_CONNECT;
      digitalWrite(LED_VERDE,LOW);
      digitalWrite(LED_VERMELHO,HIGH);
      break;
    
    case BLUETOOTH_CONNECT :
      Serial.println("Estado - BLUETOOTH_CONNECT");
      if(Serial.available() > 0)
      {
        estado = BLUETOOTH_RECV;
      }
      break;
    
    case BLUETOOTH_RECV :
      Serial.println("Estado - BLUETOOTH_RECV");
      if(Serial.available() > 0)
      {
        bt_readByte = Serial.read();
        
        if(bt_readByte == 'D' || bt_readByte == 'd')
        {
          estado = ESPERA_ABERTO;
          Serial.println("\r\nAbrindo\r\n");
          servo_tranca.write(SERVO_DESTRANCADO);
          for(int i = 0;i < 5;i++)
          {
            digitalWrite(LED_VERMELHO,LOW);
            delay(100);
            digitalWrite(LED_VERMELHO,HIGH);
            delay(100);
          }
          digitalWrite(LED_VERDE,HIGH);
          digitalWrite(LED_VERMELHO,LOW);
        }
      }
      break;
    
    case ESPERA_ABERTO :
      Serial.println("Estado - ESPERA_ABERTO");
      delay(TEMPO_DESTRANCADO);
      estado = TRANCA;
      break;
    
    case TRANCA :
      Serial.println("Estado - TRANCA");
      servo_tranca.write(SERVO_TRANCADO);
      for(int i = 0;i < 5;i++)
      {
        digitalWrite(LED_VERDE,LOW);
        delay(100);
        digitalWrite(LED_VERDE,HIGH);
        delay(100);
      }
      digitalWrite(LED_VERDE,LOW);
      digitalWrite(LED_VERMELHO,HIGH);
      
      estado = START;
      break;
    
    default:
      break;
  }
}
```
