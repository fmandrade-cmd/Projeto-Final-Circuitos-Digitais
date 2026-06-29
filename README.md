# Sistema Digital de Monitoramento da Qualidade da Água (ODS 6)

Este repositório contém o projeto de um circuito digital desenvolvido para o **Ideathon-CD** como parte do Trabalho Final da disciplina de **Circuitos Digitais** no Instituto de Ciência e Tecnologia da Universidade Federal de São Paulo (**Unifesp/ICT**).

O projeto responde diretamente ao desafio de criar soluções tecnológicas voltadas aos **Objetivos de Desenvolvimento Sustentável (ODS)** da ONU, mapeando especificamente o **ODS 6: Água Potável e Saneamento**.

---

## 📌 Contextualização e Motivação
A garantia de acesso à água potável exige um monitoramento rigoroso e em tempo real de parâmetros físicos e químicos. Este protótipo simula um sistema de análise automatizado que recebe dados digitais já convertidos de múltiplos sensores ambientais e realiza o processamento aritmético e lógico para determinar o nível crítico de contaminação da água, exibindo o alerta visual formatado para o usuário final.

---

## 🛠️ Requisitos Técnicos Implementados
O circuito cumpre rigorosamente os requisitos desejáveis estipulados no regulamento do Ideathon-CD:
* **Plataforma:** Desenvolvido integralmente no software simulador **WiredPanda**.
* **Sensores de Entrada:** 3 entradas de sensoriamento independentes:
  * **Sensor de Turbidez ($S_T$):** Numérico, quantizado em 5 bits ($0$ a $31$).
  * **Sensor de Condutividade ($S_C$):** Numérico, quantizado em 5 bits ($0$ a $31$).
  * *(Opcional: adicione aqui o 3º sensor binário ou digital utilizado no seu grupo, ex: Sensor de Fluxo/Nível)*.
* **Componentes Utilizados:** Circuitos aritméticos (Somador de 5 bits), Comparadores de magnitude, Flip-Flops (Lógica Sequencial de Estado), Portas Lógicas e um Decodificador Customizado para Display de 7 Segmentos.
* **Saídas:** Display de 7 segmentos de Cátodo Comum para indicação do nível de alerta ($0$ a $3$) e alarmes lógicos paralelos.

---

## 📐 Formulação Matemática e Lógica (LaTeX)

O núcleo do sistema realiza a soma vetorial dos dois sensores numéricos principais para obter um índice bruto de impurezas ($I_B$):

$$I_B = S_T + S_C$$

Como cada sensor possui $5\text{ bits}$ (máximo $31$), a saída do somador gera um barramento de $5\text{ bits}$ mais o bit de transbordo (*Carry Out* $C_{out}$), totalizando uma escala de $0$ a $62$.

O estágio do Comparador mapeia o índice $I_B$ em um vetor de estado binário de dois bits $(N_1, N_0)$, que define o grau de severidade apresentado no display:

### Equações do Decodificador de 7 Segmentos
A matriz do display de **Cátodo Comum** foi sintetizada utilizando mapas de Karnaugh baseados nas variáveis de estado salvas nos Flip-Flops $(N_1, N_0)$. As equações lógicas otimizadas para a ativação de cada segmento ($a$ até $g$) são dadas por:

* **Segmento $a$:** $$a = N_1 + \overline{N_0}$$
* **Segmento $b$:** $$b = 1 \quad \text{(VCC Constante)}$$
* **Segmento $c$:** $$c = \overline{N_1} + N_0$$
* **Segmento $d$:** $$d = N_1 + \overline{N_0}$$
* **Segmento $e$:** $$e = \overline{N_0}$$
* **Segmento $f$:** $$f = \overline{N_1} \cdot \overline{N_0}$$
* **Segmento $g$:** $$g = N_1$$

---

## 🧠 Justificativa de Engenharia e Casos Extremos

Durante a validação de bancada do protótipo, foi mapeado um comportamento crítico de atenuação aritmética quando um dos sensores operava em escala máxima e o outro em escala zerada ($S_T = 31$ e $S_C = 0$). Matematicamente, a soma resulta em $31$, o que representa exatamente a metade da escala total ($62$) do barramento de saída do somador:

$$I_B = 31 + 0 = 31 \implies \text{Binário: } [011111]_2$$

Onde o Bit 5 ($C_{out}$) assume nível lógico `0` e o Bit 4 assume nível lógico `1`.

Para evitar cenários onde uma pane de contaminação isolada (como altíssima turbidez) fosse diluída e mascarada pela média da soma, o circuito implementa uma **Lógica de Sobreelevação (*Override*)**. Foram acoplados blocos de limiares independentes (*Comparadores de 5 bits*) ligados diretamente às portas **OR** de entrada dos Flip-Flops. Desse modo, se qualquer um dos sensores atingir isoladamente um patamar crítico de perigo, o circuito ignora a atenuação da soma e força o estado síncrono para $(N_1N_0) = 11$, disparando imediatamente o nível máximo de alerta (**Nível 3**) no display.

---
