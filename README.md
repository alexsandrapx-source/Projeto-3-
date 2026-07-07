# Projeto 3
Projeto 3 de SEL0433 - Aplicação de Microprocessadores
Alexsandra Pavani Xavier N°USP 14681372

# Parte 1

## Objetivo
O objetivo desta etapa foi controlar a intensidade luminosa de um LED RGB utilizando a técnica de Modulação por Largura de Pulso (PWM – Pulse Width Modulation). Cada uma das três cores (vermelho, verde e azul) possui seu nível de brilho alterado automaticamente ao longo da execução do programa, produzindo diferentes combinações de cores. Além disso, os valores enviados ao LED são monitorados pela comunicação serial.

## Bibliotecas utilizadas
```cpp
#include <Arduino.h>
```
Foi utilizada a biblioteca Arduino.h, responsável por disponibilizar as funções básicas da plataforma Arduino/ESP32, como:
- configuração dos pinos;
- comunicação serial (`Serial`);
- temporização (`delay()`);
- funções para controle de PWM;
- estrutura principal do programa (`setup()` e `loop()`).

## Configuração do PWM
```cpp
const uint16_t frequenciaPWM = 5000;
const uint8_t resolucaoPWM = 8;
const uint8_t ledR = 4, ledG = 0, ledB = 2;
```
Neste trecho são definidos os principais parâmetros do PWM.
- frequência PWM: 5000 Hz;
- resolução: 8 bits, permitindo valores entre 0 e 255;
- ledR, ledG e ledB: definem os GPIOs conectados aos LEDs vermelho, verde e azul.
Esses parâmetros serão utilizados para configurar os canais PWM do microcontrolador.

## Definição das variáveis
```cpp
const uint8_t incremento = 5;

volatile uint8_t R, G, B;
```
Nesta parte são declaradas as variáveis responsáveis pelo controle das intensidades das três cores do LED RGB.
- `incremento` determina quanto o brilho será aumentado a cada repetição do programa.
- `R`, `G` e `B` armazenam os níveis de intensidade dos LEDs vermelho, verde e azul.
As variáveis foram declaradas como `volatile`, indicando ao compilador que seus valores podem sofrer alterações durante a execução do programa, evitando otimizações inadequadas.

## Inicialização do sistema
```cpp
void setup() {

  Serial.begin(115200);

  ledcAttach(ledR, frequenciaPWM, resolucaoPWM);
  ledcAttach(ledG, frequenciaPWM, resolucaoPWM);
  ledcAttach(ledB, frequenciaPWM, resolucaoPWM);

}
```
A função `setup()` é executada apenas uma vez, logo após a inicialização do microcontrolador. Primeiramente é iniciada a comunicação serial com velocidade de 115200 bps, permitindo acompanhar os valores enviados ao LED. Em seguida, cada um dos três pinos do LED RGB é configurado para operar utilizando PWM através da função `ledcAttach()`, recebendo a frequência e a resolução definidas anteriormente.

## Atualização das intensidades dos LEDs
```cpp
R = R + incremento;
G = G + incremento * 2;
B = B + incremento * 3;
```
Durante cada execução da função `loop()`, os valores de intensidade das três cores são incrementados. Cada cor recebe uma velocidade de variação diferente:
- Vermelho: incremento de 5;
- Verde: incremento de 10;
- Azul: incremento de 15.
Como as variáveis possuem 8 bits (`uint8_t`), seus valores variam entre **0 e 255**. Quando esse limite é ultrapassado, ocorre automaticamente o retorno para zero (overflow), reiniciando o ciclo de variação do brilho.

## Escrita dos valores PWM
```cpp
ledcWrite(ledR, R);
delay(10);

ledcWrite(ledG, G);
delay(10);

ledcWrite(ledB, B);
delay(10);
```
Após o cálculo das novas intensidades, cada valor é enviado ao respectivo canal PWM por meio da função `ledcWrite()`. Essa função ajusta o Duty Cycle do sinal PWM, alterando a potência média entregue ao LED e, consequentemente, seu brilho. Os atrasos de 10 ms entre cada atualização tornam a transição das cores mais suave durante a simulação.

## Monitor Serial
```cpp
Serial.print("LED vermelho: ");
Serial.println(R);

Serial.print("LED verde: ");
Serial.println(G);

Serial.print("LED azul: ");
Serial.println(B);
```
Ao final de cada repetição do programa, os valores de intensidade das três cores são enviados ao Monitor Serial. Esse recurso facilita a verificação do funcionamento do algoritmo, permitindo acompanhar em tempo real a evolução dos níveis de PWM aplicados ao LED RGB.

## Simulação

**Inserir nesta seção uma captura da simulação realizada no Wokwi mostrando o LED RGB em funcionamento.**



# Parte 2.1

## Objetivo
O objetivo desta etapa foi controlar a posição angular de um servo motor utilizando um potenciômetro. O ESP32 realiza a leitura da tensão fornecida pelo potenciômetro por meio do conversor Analógico-Digital (ADC), converte esse valor para um ângulo entre 0° e 180° e posiciona o servo de forma proporcional. Além disso, os valores lidos são exibidos no Monitor Serial para facilitar a verificação do funcionamento do sistema.

## Bibliotecas utilizadas
```cpp
#include <Arduino.h>
#include <ESP32Servo.h>
```
Foram utilizadas as seguintes bibliotecas:
- Arduino.h: fornece as funções básicas da plataforma Arduino/ESP32, como leitura analógica, comunicação serial e temporização.
- ESP32Servo.h: disponibiliza funções para o controle de servo motores utilizando os recursos de PWM do ESP32.

## Configuração dos pinos
```cpp
const uint8_t pinoServo = 18;
const uint8_t pinoPot = 34;
```
São definidos os pinos utilizados pelo circuito.
- GPIO 18: controle do servo motor.
- GPIO 34: entrada analógica conectada ao potenciômetro.

## Declaração das variáveis
```cpp
Servo servoMotor;

int leituraADC;
int angulo;
```
O objeto `servoMotor` é utilizado para controlar o servo, enquanto as variáveis `leituraADC` e `angulo` armazenam, respectivamente, o valor lido pelo ADC e o ângulo calculado para posicionamento do motor.


## Inicialização do sistema
```cpp
void setup() {

  Serial.begin(115200);

  servoMotor.attach(pinoServo);

}
```
A comunicação serial é inicializada para monitoramento dos valores durante a execução do programa. Em seguida, o servo motor é associado ao pino de controle utilizando a função `attach()`.

## Leitura do potenciômetro
```cpp
leituraADC = analogRead(pinoPot);
```
O ESP32 realiza a leitura do potenciômetro utilizando seu conversor Analógico-Digital de 12 bits, produzindo valores entre 0 e 4095, proporcionais à posição do potenciômetro.

## Conversão da leitura para ângulo
```cpp
angulo = map(leituraADC, 0, 4095, 0, 180);
```
A função `map()` converte a leitura do ADC para a faixa de operação do servo motor (0° a 180°), permitindo que a posição do eixo acompanhe a movimentação do potenciômetro.

## Controle do servo motor
```cpp
servoMotor.write(angulo);
```
O ângulo calculado é enviado ao servo motor utilizando a função `write()`, responsável por gerar o sinal PWM necessário para posicionar o eixo na posição desejada.

## Monitor Serial
```cpp
Serial.print("ADC: ");
Serial.print(leituraADC);

Serial.print("   Angulo: ");
Serial.println(angulo);
```
Os valores da leitura analógica e do ângulo calculado são enviados ao Monitor Serial, permitindo acompanhar o funcionamento do sistema em tempo real.

## Simulação

**Inserir nesta seção uma captura da simulação realizada no Wokwi mostrando o controle do servo motor pelo potenciômetro.**

# Parte 2.2 – Controle de uma Cortina Inteligente com Sensor LDR e Display OLED

## Objetivo
O objetivo desta etapa foi desenvolver um sistema de controle para uma cortina inteligente utilizando um sensor de luminosidade (LDR), um servo motor e um display OLED. O ESP32 realiza a leitura da intensidade luminosa, converte esse valor em um ângulo para o servo motor, que representa a abertura da cortina, e exibe em tempo real as informações de luminosidade, ângulo e estado da cortina no display OLED e no Monitor Serial.

## Bibliotecas utilizadas
```cpp
#include <Arduino.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <ESP32Servo.h>
```
Foram utilizadas as seguintes bibliotecas:
- Arduino.h: disponibiliza as funções básicas da plataforma Arduino/ESP32.
- Wire.h: utilizada para comunicação I²C entre o ESP32 e o display OLED.
- Adafruit_GFX.h: fornece funções gráficas para escrita de textos e desenhos.
- Adafruit_SSD1306.h: permite o controle do display OLED SSD1306.
- ESP32Servo.h: utilizada para controlar o servo motor por meio de PWM.

## Configuração do display e dos pinos
```cpp
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

Servo cortina;

const int pinoServo = 18;
const int pinoLDR = 34;
```
Neste trecho são definidos os parâmetros do display OLED e os pinos utilizados pelo circuito.
- Display OLED: resolução de 128 × 64 pixels.
- GPIO 18: controle do servo motor.
- GPIO 34: entrada analógica conectada ao sensor LDR.

## Inicialização do sistema
```cpp
void setup()
{
  Serial.begin(115200);

  Wire.begin(21, 22);

  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);

  display.clearDisplay();
  display.display();

  cortina.attach(pinoServo);
}
```
Na inicialização são configurados:
- a comunicação serial;
- o barramento I²C do display OLED;
- o display SSD1306;
- o servo motor, associado ao pino de controle.

## Leitura do sensor de luminosidade
```cpp
luminosidade = analogRead(pinoLDR);
```
O ESP32 realiza continuamente a leitura do sensor LDR utilizando o conversor Analógico-Digital (ADC). O valor obtido varia entre 0 e 4095, conforme a intensidade luminosa incidente sobre o sensor.

## Controle da cortina
```cpp
angulo = map(luminosidade, 0, 4095, 0, 180);

cortina.write(angulo);
```
A leitura do sensor é convertida para um ângulo entre 0° e 180° utilizando a função `map()`. Em seguida, esse valor é enviado ao servo motor, que ajusta a abertura da cortina de forma proporcional à luminosidade medida.

## Monitor Serial
```cpp
Serial.print("Luminosidade: ");
Serial.print(luminosidade);

Serial.print("   Angulo: ");
Serial.println(angulo);
```
Durante a execução do programa, os valores de luminosidade e do ângulo calculado são enviados ao Monitor Serial, permitindo acompanhar o funcionamento do sistema em tempo real.

## Atualização do display OLED
```cpp
display.clearDisplay();

display.setTextColor(SSD1306_WHITE);

display.setTextSize(1);
display.setCursor(30, 0);
display.println("ESTUFA");

display.setCursor(18, 10);
display.println("INTELIGENTE");
```
O display OLED é atualizado continuamente para apresentar informações do sistema. Inicialmente é exibido o título **"ESTUFA INTELIGENTE"**, seguido pelos valores medidos e calculados. Posteriormente são mostrados:
- intensidade luminosa;
- ângulo da cortina;
- estado atual da cortina.
  
## Identificação do estado da cortina
```cpp
if (angulo < 60)
{
    display.print("FECHADA");
}
else if (angulo < 120)
{
    display.print("MEIA");
}
else
{
    display.print("ABERTA");
}
```
O programa classifica automaticamente a posição da cortina de acordo com o ângulo calculado:
- 0° a 59°: FECHADA;
- 60° a 119°: MEIA;
- 120° a 180°: ABERTA.
Essa informação é apresentada no display OLED para facilitar a visualização do estado do sistema.

## Simulação

**Inserir nesta seção uma captura da simulação realizada no Wokwi mostrando o funcionamento do sistema.**
