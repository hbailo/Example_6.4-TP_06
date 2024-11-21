# Diseño
La utilización del display se realiza en el módulo `user_interface.cpp`, funciones `userInterfaceDisplayInit()` y `userInterfaceDisplayUpdate()`

El módulo Display implementa la interfaz paralelo de 8 o 4 bits e I2C.

Para la interfaz de 8 bits utiliza las salidas digitales:

```cpp
    DigitalOut displayD0( D0 );
    DigitalOut displayD1( D1 );
    DigitalOut displayD2( D2 );
    DigitalOut displayD3( D3 );
    DigitalOut displayD4( D4 );
    DigitalOut displayD5( D5 );
    DigitalOut displayD6( D6 );
    DigitalOut displayD7( D7 );
    DigitalOut displayRs( D8 );
    DigitalOut displayEn( D9 );
```

Para la interfaz de 4 bits solo utiliza las salidas digitales:

```cpp
    DigitalOut displayD4( D4 );
    DigitalOut displayD5( D5 );
    DigitalOut displayD6( D6 );
    DigitalOut displayD7( D7 );
    DigitalOut displayRs( D8 );
    DigitalOut displayEn( D9 );
```

Para la interfaz I2C se utilizan los pines:

```cpp
    I2C i2cPcf8574( I2C1_SDA, I2C1_SCL ); // PB9 y PB8
```

Para la interfaz SPI se utilizan los pines:

```cpp
    SPI spiSt7920(SPI1_MOSI, SPI1_MISO, SPI1_SCK); // PA7, PA6, PA5
```

En la función `userInterfaceDisplayInit()` del archivo `user_interface.cpp` se establece la interfaz SPI para la comunicación con el display de caracteres:

```cpp
    displayInit( DISPLAY_TYPE_GLCD_ST7920, DISPLAY_CONNECTION_SPI); 
```

# Lock / unlock

Mbed OS calls lock/unlock for mutex locking to guaranteed thread safety.

# Impresión por consola

Para la impresión por consola se implementan las funciones:

- `clearVirtualDisplay`: Borra la memoria del display virtual.
- `printVirtualDisplay`: Imprime la memoria del display virtual.

En las funciones `displayCharPositionWrite` y `displayStringWrite` se agrega la funcionalidad para imprimir simultáneamente en el display virtual.

# Árbol de funciones

<picture>
    <img src=img/dependency-tree-example-6.4.png>
</picture>

# Captura de mensaje

Se conecta el analizador lógico a los pines correspondientes a las salidas digitales de la interfaz SPI (ver arriba) para capturar la trama correspondiente a la impresión al display de la línea

```cpp
    displayStringWrite( "'C" );
```

En la siguiente tabla se muestran los códigos de los caracteres en el display.

<picture>
    <img src=img/table-6.1.png>
</picture>

A continuación se muestra la captura de la trama utilizando el software saleae. Los datos se envían en el nibble alto de cada byte, 0xFA es el comando de escritura.

<picture>
    <img src=img/spi.png>
</picture>

Los bytes se envían por nibbles:

#N: D7 D6 D5 D4

#1:  0  0  1  0  [0x20]

#2:  0  1  1  1  [0x70]

#3:  0  1  0  0  [0x40]

#4:  0  0  1  1  [0x30]
