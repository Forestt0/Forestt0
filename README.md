#include <18F4550.h>
#device ADC=10
#fuses HSPLL, NOWDT, NOPROTECT, NOLVP, NODEBUG, USBDIV, PLL5, CPUDIV1, VREGE, NOPBADEN
#use delay (clock=48000000)
#include <usb/usb_bootloader.h>
#include <JACSS_CDC.c>

// Definición de pines
#define LED_PORT PORTB
#define BUTTON_PIN PIN_D0

void secuenciaApagado() {
  output_b(0x00);
  delay_ms(1000);
}

void secuenciaDerechaIzquierda() {
  int i;

  for (i = 0; i < 8; i++) {
    output_b(1 << i);
    delay_ms(500);
  }
}

void secuenciaIzquierdaDerecha() {
  int i;

  for (i = 7; i >= 0; i--) {
    output_b(1 << i);
    delay_ms(500);
  }
}

void secuenciaPar() {
  int i;

  for (i = 0; i < 8; i += 2) {
    output_b(1 << i);
    delay_ms(500);
  }
}

void secuenciaImpar() {
  int i;

  for (i = 1; i < 8; i += 2) {
    output_b(1 << i);
    delay_ms(500);
  }
}

void secuencia2en2() {
  int i;

  for (i = 0; i < 8; i += 2) {
    output_b(3 << i);
    delay_ms(500);
  }
}

void cambiarSecuencia() {
  int secuencia = 1;
  int buttonState;
  int prevButtonState = 1;

  while (TRUE) {
    buttonState = input(BUTTON_PIN);

    if (buttonState == 0 && prevButtonState == 1) {
      secuencia++;
      if (secuencia > 6) {
        secuencia = 1;
      }
    }

    prevButtonState = buttonState;

    switch (secuencia) {
      case 1:
        secuenciaApagado();
        break;
      case 2:
        secuenciaDerechaIzquierda();
        break;
      case 3:
        secuenciaIzquierdaDerecha();
        break;
      case 4:
        secuenciaPar();
        break;
      case 5:
        secuenciaImpar();
        break;
      case 6:
        secuencia2en2();
        break;
    }
  }
}

void main() {
  set_tris_b(0x00);  // Configurar pines B como salida
  set_tris_d(0xFF);  // Configurar pin D0 como entrada
  output_b(0x00);    // Apagar todos los LEDs al inicio

  cambiarSecuencia();
}
