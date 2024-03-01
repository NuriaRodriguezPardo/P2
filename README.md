# Pràctica 2: interrupciones
## A. Interrupción por GPIO

**Código:**

    struct Button {
        const uint8_t PIN;
        uint32_t numberKeyPresses;
        bool pressed;
    };
    Button button1 = {18, 0, false};
    // Instancia de la estructura Button con valores iniciales
    void IRAM_ATTR isr() {
        button1.numberKeyPresses += 1;
        button1.pressed = true;
    }
    void setup() {
        Serial.begin(115200); 
        // Inicia la comunicación serial para la depuración
        pinMode(button1.PIN, INPUT_PULLUP);
        // Configura el pin del botón como entrada con pull-up interno
        attachInterrupt(button1.PIN, isr, FALLING);
        //Funcion integrada de Arduiono, no necesario inicializar.
        //Función de interrupción al pin del botón
    }
    
    void loop() {
        if (button1.pressed) {
            Serial.printf("Button 1 has been pressed %u times\n",
            button1.numberKeyPresses);
            button1.pressed = false;
            }
        //Detach Interrupt after 1 Minute
        static uint32_t lastMillis = 0;
        // Variable estática para rastrear el tiempo transcurrido
        if (millis() - lastMillis > 60000) {
            lastMillis = millis(); // Reinicia el contador de tiempo
            detachInterrupt(button1.PIN); 
            // Desvincula la interrupción del pin del botón
            Serial.println("Interrupt Detached!");
            }
    }
**Descripcion:**<br>
Este programa cuenta las veces que ha habido una interrupción.<br>Primero hemos creado un "objeto", que llamamos "Button", esta estructura contiene los datos necesarios para saber si el boton se ha pulsado, un contador para saber quantas veces se ha pulsado y a la vez un PIN para diferenciarlo.<br>
A continuación hemos creado una instancia (ejemplar de la estructura inicializado a unos valores especificos) de la estructura "Button" y creado una funcion que solo se ejecutará cuando se produzca una interrupción incrementando el contador y cambiando el estado del booleano tambien creado en la estructura.<br>Con la configuración inicial hemos inicializado la comunicación serial para la depuración, hemos configurado el pin del botón como entrada con el pull-up interno y adjuntado la función de interrupción al pin del botón.<br>
Por último, hemos creado un bucle que se encarga de comprobar si el botón ha sido presionado, es decir, si ha habido una interrupción. Si es asi, imprime el numero de pulsaciones en el puerto de serie con un mensaje y luego estableze el estado del boton nuevamente a no presionado. En el caso de estar un minuto sin ninguna interrupción, se desvincula la interrupción del pin del botón i acaba el programa.<br>
En conclusión, este código nos permite contar el numero de veces que creamos una interrupción, (en nuestro caso juntamos los dos cables) y lo imprime por el puerto serie, pero despues de un minuro deja de contar acabando el programa.





