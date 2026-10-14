# Sistema de Monitoramento de Aquário Inteligente (ODS 14)

Repositório desenvolvido para atividade acadêmica, com o objetivo de aplicar conceitos de sistemas embarcados e Internet das Coisas (IoT) voltados à sustentabilidade, alinhado diretamente à **ODS 14: Vida na Água**.

---

##  Sobre o Projeto

O projeto consiste em um protótipo de monitoramento automatizado para aquários, focado em preservar a vida marinha e o bem-estar de animais de estimação aquáticos. O sistema coleta dados cruciais do ambiente aquático em tempo real, exibindo-os em um display LCD e acionando alertas visuais (LEDs) caso algum parâmetro saia dos limites ideais de sobrevivência.

### Parâmetros Monitorados:
1. **Temperatura da Água:** Medida através do sensor analógico TMP36. Faixa ideal de segurança: entre 24°C e 32°C.
2. **Nível de Oxigênio (O₂):** Simulado via potenciômetro.
3. **Nível de pH da Água:** Simulado via potenciômetro.

---

## Tecnologias e Componentes Utilizados

* **Hardware / Simulação:**
  * Arduino Uno
  * Display LCD 16x2 com adaptador I2C
  * Sensor de Temperatura TMP36
  * 2x Potenciômetros (para simulação dos níveis de Oxigênio e pH)
  * 3x LEDs (Indicadores de alerta para temperatura, oxigênio e pH)
  * Resistores de 220Ω
  * Protoboard e cabos de conexão
* **Software:**
  * **Tinkercad Circuits** (Ambiente de simulação)
  * **Linguagem C++** (Firmware do Arduino)
  * **Biblioteca:** `LiquidCrystal_I2C`

---

## 💻 Código-Fonte (`sketch.ino`)

O código utilizado no Arduino realiza leituras analógicas periódicas, converte os valores para unidades reais (como graus Celsius e percentuais) e alterna dinamicamente as informações no display LCD, além de acionar os LEDs de alerta:

```cpp
// C++ code
// Biblioteca para o leitor LCD I2C
#include <LiquidCrystal_I2C.h>

// Inicializa o display no endereço 39 (16 colunas e 2 linhas)
LiquidCrystal_I2C lcd(39, 16, 2);

int ledvermelho = 2; 
int ledOxi = 3; 
int ledPh = 4; 

int pinoSensor = A0;
int pinoOxigenio = A1;  // Potenciômetro simulando Oxigênio
int pinoPh = A2;        // Potenciômetro simulando pH/Toxidade
int contadorTela = 0;
int leituraAnalogica;
float tensao, temperaturaC;

float medicaoTempValor(){
  leituraAnalogica = analogRead(pinoSensor);
  tensao = (leituraAnalogica * 5.0) / 1023.0;
  temperaturaC = (tensao - 0.5) * 100.0;
  return temperaturaC;
}

void medicaoTemperaturaLED(int temp){
  if(temp < 24 || temp > 32){
    digitalWrite(ledvermelho, HIGH);
  } else {
    digitalWrite(ledvermelho, LOW);
  }
}

void medicaoOxigenio(int oxg){
  if(oxg < 6.0 || oxg > 8.0){
    digitalWrite(ledOxi, HIGH);
  } else {
    digitalWrite(ledOxi, LOW);
  }
}
  
void medicaoPh(int ph){
  if(ph < 6.5 || ph > 9.5){
    digitalWrite(ledPh, HIGH);
  } else {
    digitalWrite(ledPh, LOW);
  }
}

void setup()
{
  Serial.begin(9600);
  pinMode(ledvermelho, OUTPUT);
  pinMode(ledOxi, OUTPUT);
  pinMode(ledPh, OUTPUT);
  
  lcd.init();         
  lcd.backlight();      
}

void loop()
{
  float tempAtual = medicaoTempValor();
  int leituraO2 = analogRead(pinoOxigenio);
  int leituraPh = analogRead(pinoPh);

  int nivelOxigenio = map(leituraO2, 0, 1023, 0, 100);
  int nivelPh = map(leituraPh, 0, 1023, 0, 100);

  if(contadorTela < 3){
    lcd.setCursor(0, 0); 
    lcd.print("Temp da Agua:     "); 
    lcd.setCursor(0, 1);
    lcd.print(medicaoTempValor());
    lcd.print(" C    "); 
  } 
  else if (contadorTela >= 3 && contadorTela < 6) {
    lcd.setCursor(0, 0); 
    lcd.print("Oxigenio:    ");
    lcd.setCursor(0, 1);
    lcd.print(nivelOxigenio);
    lcd.print(" %      ");
  } 
  else if (contadorTela >= 6 && contadorTela < 9){
    lcd.setCursor(0, 0); 
    lcd.print("PH da agua:    ");
    lcd.setCursor(0, 1);
    lcd.print(nivelPh);
    lcd.print(" %      ");
  } else {
    contadorTela = -1; 
  }
  
  medicaoOxigenio(nivelOxigenio);
  medicaoTemperaturaLED(tempAtual); 
  medicaoPh(nivelPh);
  
  contadorTela++; 
  delay(1000); 
}
```

## Como Executar a Simulação

O projeto pode ser executado tanto em um ambiente virtual(Tinkercad circuits) quanto na IDE própria do arduino com um sistema fisico montado. 

1. Acesse o ambiente
2. Crie um novo circuito baseado em Arduino Uno
3. Monte os componentes conforme o esquema elétrico (Sensor TMP36, Display LCD com I2C, Potenciômetros e LEDs nas portas digitais e analógicas correspondentes).
4. Copie o código acima e cole na aba de código do Tinkercad (modo Texto).
5. Clique em Iniciar Simulação (Start Simulation) e utilize os potenciômetros para alterar os valores e testar os alertas visuais no display e nos LEDs.

## Aluno 
Nome: Vinicius Eduardo Campos de Carvalho
Curso: Ciência da Computação - PUC Minas
