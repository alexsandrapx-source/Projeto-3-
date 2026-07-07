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
<img width="715" height="617" alt="simulacao_parte1" src="https://github.com/user-attachments/assets/2163b0fa-6000-4b2b-8214-d78165a11396" />

**Figura 1.** Simulação da Parte 1 no Wokwi.

## Discussão dos resultados
O sistema apresentou o comportamento esperado, variando continuamente a intensidade das três cores do LED RGB por meio do PWM. Como cada canal utiliza um incremento diferente, foram obtidas diversas combinações de cores ao longo da execução. Os valores exibidos no Monitor Serial confirmaram a correta atualização dos duty cycles.


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

<img width="1107" height="740" alt="simulacao_parte21_0graus" src="https://github.com/user-attachments/assets/e0f282cd-190a-4cd5-b3d0-74710f755013" />

**Figura 2.** Simulação da Parte 2.1 no Wokwi.

<img width="1112" height="688" alt="simulacao_parte21_180graus" src="https://github.com/user-attachments/assets/4060957e-3ee7-4783-a162-38e1be32dd94" />

**Figura 3.** Simulação da Parte 2.1 no Wokwi.

## Discussão dos resultados
O servo motor respondeu corretamente às variações do potenciômetro, movimentando seu eixo de forma proporcional entre 0° e 180°. A leitura do ADC e o ângulo calculado foram exibidos no Monitor Serial, confirmando o correto funcionamento da conversão analógico-digital.


## Conclusão

O desenvolvimento deste projeto permitiu aplicar, de forma prática, conceitos de sistemas embarcados utilizando o ESP32. Foram implementadas aplicações envolvendo controle por PWM, leitura de sinais analógicos por meio do ADC e comunicação serial, permitindo compreender a integração entre hardware e software em sistemas embarcados. As simulações realizadas no Wokwi validaram o funcionamento das soluções desenvolvidas, demonstrando que os objetivos propostos para cada etapa foram alcançados.
