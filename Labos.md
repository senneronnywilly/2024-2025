# Inhoudstafel
- [Inhoudstafel](#inhoudstafel)
  - [Reeks 1](#reeks-1)
    - [Oefening 8](#oefening-8)
  - [Reeks 2](#reeks-2)
    - [Oefening 1](#oefening-1)
    - [Oefening 2](#oefening-2)
    - [Oefening 3](#oefening-3)
    - [Oefening 4](#oefening-4)
    - [Oefening 5](#oefening-5)

## Reeks 1

### Oefening 8

vb. rrc gevolgd door rlc:
1. in Accumulator: 1100 -> 110. met 0 in carry
2. in register B: 0 van carry naar register -> 0
3. in Accumulator: 110. -> 11.. met 0 in carry
4. in register B: 0 van carry naar register -> 00
5. in Accumulator: 11.. -> 1... met 1 in carry
6. in register B: 1 van carry naar register -> 001
7. in Accumulator: 1... -> 0 met 1 in carry
8. in register B: 1 van carry naar register -> 0011

```
org 0000H

jmp main

org  0050H

main:   mov R2,#8
        mov A,#0BCH
        mov B,#00
loop:   rrc A           (ACC>>=1 en C=LSb Acc)
        mov R3,A        (voor tijdelijke opslag eig "push Acc")
        mov A,B
        rlc A 
        mov B,A         (B<<=1 en LSb B=C)
        mov A,R3        (terughalen is dan "pop Acc")
        djnz R2, loop
        jmp $
```

## Reeks 2

> **De simulator bevat slechts twee timers en de klokfrequentie bedraagt 12 MHz (intern gedeeld door 12).**

### Oefening 1

> **zie pagina 120 van datasheet**

de klokfrequentie is 12 MHz gedeeld door 12 => simpele regel van 3 om uit te rekenen hoelang het duurt

```
0000
0001
....
00FF
0100
....
FFFF
    -> overflow
0000
```
hoelang duurt het om van 0000 naar FFFF te geraken?

(65,636 * 10^3^s) / (10^6^) = (65,636 * 10^-3^s) = 65,536 ms

==(**aantal ticks**)/(**klokfrequentie**) =  **tijdsinterval**==

### Oefening 2

> **zie pagina 286, 287, 289 en 290 van datasheet**

beschikbare registers: TMOD, TCOM, TH0, TH1, TL0 en TL1 (nog niet CKCON, pas later toegevoegd)

TH0 en TL0 zijn de tel-registers

TCOM: Bit7 -> Bit4 gaan over timers

TMOD: configuratie (1x), Bit7 -> Bit4 gaan over timer1 en Bit3 -> Bit0 gaan over timer2

### Oefening 3

```
org 0000H

jmp main

org 0050H

main:   mov TMOD,#10H   (timer 1 mode 1 (16 bit timer ))(bv. TMOD=23H --> timer1 mode 2, timer 0 mode 3)
        mov TH1,#06H
        mov TL1,#00H
        setb TR1
loop:   jnb TF1,$   (zolang niet overlopen, vlag dus op 0)
        mov TH1,#06H   (hier breakpoint)(registers terug op startwaarden na overlopen)
        mov TL1,#00H
        clr TF1     (vlag clearen, terug op 0)
        cpl P1.6
        jmp loop
```
aantal ticks = 64ms * 10^6^ = 64 000

**x + inverse_x = -1** => **-x = inverse_x + 1**  
in Windows calculator:
1. Programmer
2. 64 000
3. Bitwise -> NOT
4. +1
5. HEX: ... **0600** (minst beduidende zijn belangrijkste)

### Oefening 4

aantal ticks (voor 2s) = 2 000 000 = 20 000 * 100

in Windows calculator:
1. Programmer
2. 20 000 (DEC)
3. +/-
4. HEX: ... **B1E0**

_versie 1: om seconden te checken_

```
org 0000H

jmp main

org 0050H

main:   mov TMOD,#10H
        mov TH1,#0B1H
        mov TL1,#0E0H
        setb TR1
start:  mov R7,#100
loop:   jnb TF1,$
        mov TH1,#0B1H
        mov TL1,#0E0H
        clr TF1
        djnz R7,loop
        cpl P1.6    (hier breakpoint)
        jmp start
```

_versie 2_

```
org 0000H

jmp main

org 0050H

main:   mov TMOD,#10H
        mov TH1,#0B1H
        mov TL1,#0E0H
        mov R2,#00
        setb TR1
start:  mov R7,#100
loop:   jnb TF1,$
        mov TH1,#0B1H
        mov TL1,#0E0H
        clr TF1
        djnz R7,loop
        inc R2
        cjne R2,#10,uitschrijven    (wanneer R2 verschillend is naar 10 dan jump naar uitschrijven)
        mov R2,#00      (voert dit uit als gelijk aan 10)
uitschrijven:   mov A,R2
                cpl A
                mov P1,A
        jmp start
```

### Oefening 5





 


