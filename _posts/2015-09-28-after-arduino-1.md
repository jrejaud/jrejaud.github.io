---
layout: post
title:  "After Arduino Part 1: What is the Arduino and why go beyond?"
date:   2015-09-28 20:02:00
categories: arduino
---

###What is Arduino?

The word "Arduino", in my experience, is often used in a vague way by various sources on the internet. To make it more concrete, I argue that there are three parts of the Arduino prototyping platform:

-----

1. The Arduino [Programming Language][arduino prog lang]: The Arduino "Language" isn't its own programming language, but rather C++ with many libraries and functions to simplify electronics related tasks, like digitalWrite, analogRead, ect.

2. The Arduino [IDE][arduino ide] (Integrated Development Environment) that lets you write your C++ Arduino code, compile it, and upload it to a Microcontroller.

3. The Arduino [boards][arduino boards] themselves, such as the Arduino Uno, the Arduino Mega, and the Arduino Micro. There are MANY different boards.

-----
<!-- ![Arduino Uno](/images/uno_picture.png) -->
{% include image.html img="/images/uno_picture.png" caption="Ceci n'est pas un Microcontroller" %}

Arduino boards are often referred to as microcontrollers by electronics neophytes. According to Arduino's website:

{% include image.html img="/images/arduino_site_quote.png" caption="Straight from the horses mouth" %}


###What is a microcontroller?

An Arduino board (like the Arduino Uno) isn't a microcontroller, but rather an electronics prototyping platform that contains a microcontroller. When you upload your code to your arduino board, what you are actually doing is uploading it to the microcontroller on the board. A microcontroller (also referred to as a microcontroller Unit, MCU, or µC) is the brains of an Arduino board, but your Arduino board also contains many other components (that you may or may not be using, based on your project).

After this great revelation, you might be wondering: "Where is the MCU on my Arduino board?" For this tutorial, let's focus on the Arduino Uno board.

{% include image.html img="/images/mcu_picture.png" caption="The MCU" %}

{% include image.html img="/images/no_mcu_picture.png" caption="The rest" %}

This is all rather edifying, but why would you care what the MCU on your Arduino is? Why bother learning about how the board works and what the Arduino "language" is?

Because, with the right hardware and software, you can program your MCU directly without a board and you can write non-Arduino software to run on your Arduino Board!

But that's not all! You also get these amazing perks once you actually understand ~~wtf is going on~~ delve deeper into how MCUs work.

###Why go beyond just using an Arduino

1. You don't have to use the Arduino IDE anymore. Want to collapse your functions (hide them) while editing? Want to use features that you've come to expect in a modern IDE? You can use [Atmel Studio][atmel studio] (or any other IDE) instead of the Arduino IDE.

2. Learn to write programs in AVR-C as opposed to Arduino C++ and your code will be much smaller and faster. The Arduino Blink.ino file takes up 3% of the flash memory space on an Arduino Uno. An equivalent program written in AVR-C would only take up 0.625% and it would run faster. That size and speed difference doesn't matter if you using Blink, but if are writing a more complex program, you will probably notice the difference. The Arduino uses a bootloader so Arduino programs take up more space, but don't require a dedicated programmer.

3. An Arduino Uno costs $25, an ATMega328p costs less then $4. Not that much money for a single project, but if you plan to make a lot of things (or commercializing your project), that's quite a difference.

4. Size; Arduinos can be rather large. You can make your projects a lot smaller if you build the circuit yourself and eliminate components that you don't need.

[arduino prog lang]: http://arduino.cc/en/Reference/HomePage
[arduino ide]: http://arduino.cc/en/main/software
[arduino boards]: http://arduino.cc/en/main/boards
[atmel studio]: http://www.atmel.com/tools/ATMELSTUDIO.aspx
