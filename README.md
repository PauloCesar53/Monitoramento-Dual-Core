# Sistema de Monitoramento Inercial Multicore (BitDogLab + MPU6050, RP2040)

Projeto da aula **Desenvolvimento de Sensores e Atuadores IoT — Parte 8 (Multicore)**.  
Explora a arquitetura **dual-core** do RP2040:  
- **Core 0:** aquisição e pré-processamento (acelerômetro + giroscópio, MPU6050 via I²C).  
- **Core 1:** interface com o usuário (OLED SSD1306 + LED) e apresentação em tempo real.  

> Considerando o **MPU6050** como **dois sensores integrados** (acel + giro).

---

## Sumário
- [Arquitetura](#arquitetura)
- [Hardware e Ligações](#hardware-e-ligações)
- [Requisitos](#requisitos)
- [Como compilar](#como-compilar)
- [Como gravar no Pico/BitDogLab](#como-gravar-no-picobitdoglab)
- [Como executar](#como-executar)
- [Demonstração em vídeo](#demonstração-em-vídeo)
- [Troubleshooting](#troubleshooting)
- [Licença e créditos](#licença-e-créditos)

---

## Arquitetura
**Divisão de responsabilidades:**
- **Core 0**
  - Inicializa I²C e **MPU6050** (endereço típico `0x68`);
  - Lê aceleração e velocidade angular;
  - Calcula **roll** e **pitch**;
  - Empacota e envia dados ao Core 1 via **FIFO** (multicore).
- **Core 1**
  - Recebe dados pelo **FIFO**;
  - Atualiza **OLED SSD1306** (valores de roll/pitch);
  - Sinaliza atividade em **LED Azul** (dado recebido).

Comunicação inter-core com `multicore_fifo_push_blocking()` / `multicore_fifo_pop_blocking()`.

---

## Hardware e Ligações
- **Placa:** BitDogLab (RP2040).
- **Sensor:** MPU6050 (I²C).
- **Display:** SSD1306 (I²C).
- **LED:** LEDs on-board da BitDogLab.

> Ajuste os pinos de I²C (SDA/SCL) no código conforme sua ligação física na BitDogLab.

---

## Requisitos
- **Pico SDK** configurado (variável `PICO_SDK_PATH`).
- **CMake** (>= 3.13), **Ninja** ou **Make**.
- **GCC ARM** (ex.: `arm-none-eabi-gcc`) ou toolchain equivalente.
- Biblioteca do **SSD1306** usada no projeto (já incluída no código).
- (Opcional) **Windows:** instale CMake + Ninja + GCC ARM (ou use WSL).

---

## Como compilar

### Linux / macOS
```bash
# Na raiz do projeto
mkdir build && cd build
cmake -DPICO_SDK_PATH=/caminho/para/pico-sdk -DCMAKE_BUILD_TYPE=Release ..
cmake --build . -j
```

### Windows (PowerShell, com Ninja)
```bash
mkdir build; cd build
cmake -G "Ninja" -DPICO_SDK_PATH="C:/path/to/pico-sdk" -DCMAKE_BUILD_TYPE=Release ..
cmake --build . -- -j
```
O binário .uf2 será gerado em build/ (nome conforme o CMakeLists.txt).

---

## Como gravar no Pico/BitDogLab

1. Conecte a placa ao PC em modo BOOTSEL (segure BOOT, conecte USB e solte).

2. O volume RPI-RP2 aparecerá.

3. Arraste o arquivo .uf2 gerado para dentro do RPI-RP2.

4. A placa reinicia executando o firmware.

---

## Como executar

- Incline a placa para variar roll/pitch.

- Observe o OLED atualizando os valores em tempo real.

- (Opcional) Monitore a saída serial para logs (se habilitados no código).

---

## Demonstração em vídeo

Link:

---

## Troubleshooting

- OLED não atualiza: confirme pinos SDA/SCL e endereço I²C do SSD1306.

- Sem leitura do MPU: verifique alimentação, GND comum e endereço 0x68.

- Compilação falha: confira PICO_SDK_PATH e versão do toolchain.

- UF2 não aparece: entre no modo BOOTSEL corretamente ou troque o cabo USB/diferente porta.

---

## Licença e créditos

Uso acadêmico, Residência EmbarcaTech.

Professor: Wilton Lacerda Silva.

Equipe: Heitor Lemos, Luiz Filipe Ribeiro, Roberto Cardoso e Paulo César Di Lauro.