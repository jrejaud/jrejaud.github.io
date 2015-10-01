---
layout: post
title:  "After Arduino Part 2: Arduino to AVR-C Reference Guide"
date:   2015-09-30 23:25:00
categories: arduino
---

##Arduino to AVR-C on the ATmega328p

-----

Note: This reference guide is intended for those are very familiar with programming using the Arduino "Language" and are somewhat familiar with AVR-C.

Use this guide in conjunction with the ATmega328p [datasheet][datasheet].

This page is a work in progress, follow me on [twitter][twitter] if you would like to know when I update it.

-----

The ATmega328p is the microcontroller "brains" inside of an Arduino Uno. Anyone who has spend any significant time working with Arduino boards will quickly realize the limitations of the Arduino "Language". A more powerful (and complex) alternative is to use AVR-C instead. According to their [website][avr-c]:


> AVR Libc is a Free Software project whose goal is to provide a high quality C library for use with GCC on Atmel AVR microcontrollers.

The purpose of this Reference Guide is to help "translate" Arduino functions, like digitalWrite(), to their AVR-C equivalents.

1. [Port Manipulation](#port_manipulation)
2. [Digital Output](#digital_output)
3. [Digital Input](#digital_input)
4. [Analog Input](#analog_input)
5. [Delay and Timers](#delay_and_timers)


<a name="port_manipulation"></a>
## 1. Port Manipulation
Arduino Equivalent: [pinMode()][arduino_pin_mode]

[Atmel Datasheet][datasheet]: Section 14.4

{% include image.html img="/images/atmegapinImage.png" caption="Source: http://www.chicoree.fr/w/Arduino_sur_ATmega328P" %}

### Syntax
```
//Set Pin for output
DDRx |= (1<<PDn);

//Set Pin for input
DDRx &= ~(1<<PDn);

//Set internal pull-up resistor (after setting Pin for input)
PORTx |= (1<<PDn);
```

### Registers
```
DDRx: Port x Data Direction Register

PDn: AVR-C Library "shortcut" for pin number n

x = PORT; A, B, C, or D

n = Pin number; 0, 1, 2, 3, ect.
```

### Explanation
Set pin to 1 for output; Set pin to 0 for input

-----

<a name="digital_output"></a>
## 2. Digital Output
Arduino Equivalent: [digitalWrite()][arduino_digital_write]

[Atmel Datasheet][datasheet]: Section 14.4


### Syntax
```
//Set Pin High
PORTx |= (1<<PDn);

//Set Pin Low
PORTx &= ~(1<<PDn);
```

### Registers
```
PORTx: Port x Data Register

PDn: AVR-C Library "shortcut" for pin number n

x = PORT; A, B, C, or D

n = Pin number; 0, 1, 2, 3, ect.
```

### Explanation
Set pin to 1 for HIGH (5V on ATmega328p); Set pin to 0 for LOW (0V on ATmega328p)

-----

<a name="digital_input"></a>
## 3. Digital Input
Arduino Equivalent: [digitalRead()][arduino_digital_read]

[Atmel Datasheet][datasheet]: Section 14.4


### Syntax
```
if (PINx & (1<<PDn));
```

### Registers
```
PINx: Port x Input Pins Address

PDn: AVR-C Library "shortcut" for pin number n

x = PORT; A, B, C, or D

n = Pin number; 0, 1, 2, 3, ect.
```

### Explanation
Don't forget to turn on pull-up resistors if you need them!

Will return 0 is pin is LOW; will return NOT 0 is pin is HIGH

-----

<a name="analog_input"></a>
## 3. Analog Input
Arduino Equivalent: [digitalRead()][arduino_analog_read]

[Atmel Datasheet][datasheet]: Sections 24 and 14.4


### Syntax (ADC initialize example)
```
//16Mhz / 128 = 125kHz ADC reference clock
ADCSRA |= ((1<<ADPS2)|(1<<ADPS1)|(1<<ADPS0));

//Voltage reference from AVcc (5V on ATMega328p)
ADMUX |= (1<<REFS0);

//Turn on ADC
ADCSRA |= (1<<ADEN);

//Do a preliminary conversion
ADCSRA |= (1<<ADSC);
```

### Syntax (ADC initialize example)
```
//Clear previously read channel
ADMUX &= 0xF0;

//Define new ADC Channel to read (which analog pin: 0-5 on ATMega328p
ADMUX |= channel;

//New Conversion
ADCSRA |= (1<<ADSC);

//Do a preliminary conversion
ADCSRA |= (1<<ADSC);

//Wait until conversion is finished
while(ADCSRA & (1<<ADSC));

//Return ADC value
return ADCW;
```


### Registers
```
ADCSRA and ADCSRB: ADC Control and Status Registers A and B

ADPS[2:0]: ADC Prescaler Select Bits

ADMUX: ADC Multiplexer Selection Register

REFS[1:0]: Voltage Reference Selection Bits

ADEN: ADC Enable

ADSC: ADC Start Conversion

ADCW: "Shortcut" by AVR-C, holds ADV value
```

### Explanation
Analog read from Pins!

It's dangerous to go alone, read [this][using_adc_tut].

-----

<a name="delay_and_timers"></a>
## 3. Delays and Timers
Arduino Equivalent: [delay()][arduino_delay]
[Atmel Datasheet][datasheet]: Sections 15, 16, and 18

#### Syntax (Delay)
```
//Include delay .h
#include <util/delay.h>

//Delay for delay_timer ms
_delay_ms(delay_timer);
```

#### Syntax (Normal Mode)

##### Initialize Example

```
//Set Prescaler (256 in this example)
TCCRiB |= (1<<CSi2);
```

##### Timer value

```
//Set Prescaler (256 in this example)

if (TCNTi >= Target Timer Count)
{
//Write your code here :)

//Reset Timer

TCNTi = 0;
//Disable Timer (Haven't tested this yet)

TCCRiB &= ~((1<<CSi0)|(1<<CSi1)|(1<<CSi2));
}
```

### Syntax (CTC Mode)
(Clear Timer on Counter Match)

##### Initialize Example

```
//Set Prescaler (256 in this example)

TCCRiB |= (1<<CSi2);
//Set timer to CTC mode

TCCRiB |= (1<<WGMi2);
//Set Target Timer Count

OCRiA = Target Timer Count;
//Enable Timer Interrupts

TIMSKi |= (1<<OCIEiA);
//Enable Global Interrupts

sei();
```

#### ISR Example
```
//Define ISR Function
ISR(TIMERi_COMPA_vect)
{

//Write your code here :)

}
```

### Registers
```
TCCRiA: Timer/Counter Control Register A

TCCRiB: Timer/Counter Control Register B

TCNTi: Timer/Counter Register

CSi[2:0]: Clock Select

WGMi[2:0]: Waveform Generation Mode

OCRiA: Output Compare Register A

OCRiB: Output Compare Register B

TIMSKi: Timer/Counter Interrupt Mask Register

OCIEiA: Timer/Counter0 Output Compare Match A Interrupt Enable

OCIEiB: Timer/Counter0 Output Compare Match B Interrupt Enable

i = Timer; 0, 1, 2 (on ATMega328p)
```

### Explanation
Timer Frequency = System Clock Frequency / Prescaler

Target Timer Count = Timer Frequency / Target Frequency - 1 = Target Period / Timer Period - 1

System Clock Settings: Section 9; Arduino Uno clock frequency is 16mHz.

#####Prescaler Settings

{% include image.html img="/images/prescaleImage.png" caption="Source: http://www.chicoree.fr/w/Arduino_sur_ATmega328P" %}

Target Frequency = Frequency of PWM; Target Timer Count = What timer counter will "hit" after Target Period has been reached.

#### Waveform Settings

{% include image.html img="/images/waveImage.png" caption="Table 15-8 of ATMega328p datasheet" %}

-----



[datasheet]: http://www.atmel.com/devices/atmega328p.aspx
[twitter]: https://twitter.com/JordanRejaud
[avr-c]: http://www.nongnu.org/avr-libc/
[arduino_pin_mode]: http://arduino.cc/en/Reference/PinMode
[arduino_digital_write]: http://arduino.cc/en/Reference/DigitalWrite
[arduino_digital_read]: http://arduino.cc/en/Reference/DigitalRead
[arduino_analog_read]: http://arduino.cc/en/Reference/AnalogRead
[arduino_delay]: http://arduino.cc/en/Reference/delay
[using_adc_tut]: http://hekilledmywire.wordpress.com/2011/03/16/using-the-adc-tutorial-part-5/
