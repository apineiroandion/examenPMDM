# Examen: MVVM
## Autor: Angel Piñeiro Andion

Crea un Repositorio privado para realizar las siguientes modificaciones en este codigo, comentando adecuadamente en el código lo realizado y realizando un readme explicativo:

- Implementa una cuenta atrás 5...4...3...2...1
- Utiliza los Estados auxiliares para la cuenta atrás
- Configura un cuadro de texto para mostrar la cuenta atrás
- Cuando el usuario le da al "Start" empieza la cuenta atrás
- Si la cuenta atrás llega a uno y el usuario aun no acertó, la app vuelve al estado INICIO
- Plantea una mejora

Entrega el enlace al repositorio

-----

## Solucion:

### Tras analizar el codigo proporcionado, y las implementaciones necesarias he seguido los siguientes pasos:

#### 1. Crear un nuevo texto en la interfaz:
La idea es crear un texto que muestre la cuenta atras, por lo que he creado un Text, que recibe como parametro de texto, el mutableStateOf del view model con un by remember, para que se repinte cuando haya modificaciones:

#### 2. Implementar la cuenta atras:
Para implementar la cuenta atras, he añadido dos estados auxiliares, el AUX4 y el AUX5

    enum class EstadosAuxiliares(val txt: String) {
        AUX1(txt = "inicio"),
        AUX2(txt = "contando"),
        AUX3(txt = "contando"),
        AUX4(txt = "contando"),
        AUX5(txt = "fin"),
    }

Y he los he añadido a la corutina que lanzaba los estados auxiliares modificandole el tiempo para que acada estado se cambien cada segudo, y que en cada uno de ellos, le quite 1 al valor del contador de modo que empieza en 5, ya que es el valor aximo definido en datos, y cuando llega a cero se pone el estado de la app en INICIO.

    fun estadosAuxiliares() {
        viewModelScope.launch {
            cuentaAtras.value = Datos.cuentaAtras
            // guardamos el estado auxiliar
            var estadoAux = EstadosAuxiliares.AUX1

            // hacemos un cambio a tres estados auxiliares
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            cuentaAtras.value--
            estadoAux = EstadosAuxiliares.AUX2
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            cuentaAtras.value--
            estadoAux = EstadosAuxiliares.AUX3
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            cuentaAtras.value--
            estadoAux = EstadosAuxiliares.AUX4
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            cuentaAtras.value--
            estadoAux = EstadosAuxiliares.AUX5
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            cuentaAtras.value--
            estadoLiveData.value = Estados.INICIO
        }
    }

### 3. Lanzar corutina:

En el codigo desde el que aprtimos esto se lanza desde el boton de color, lo he modificado apra que se lanze justo cuando el estado se cambia a adivinando. Otra opcion seria que el propio estado adivinadno actualizara alguna variable que hiciese que se lanzara la rutina, pero he decidido dejarlo asi por falta de tiempo.

    fun actualizarNumero(numero: Int) {
        Log.d(TAG_LOG, "actualizamos numero en Datos - Estado: ${estadoLiveData.value}")
        Datos.numero = numero
        // cambiamos estado, por lo tanto la IU se actualiza
        estadoLiveData.value = Estados.ADIVINANDO
        estadosAuxiliares()
    }

### 4. Comprobacion:


![contador.png](app%2FimagesReadme%2Fcontador.png)


### 5. Mejora:

La mejora propuesta es que si el jugador acierta se acabe la cuenta atras.

Para ello creo un metodo pararCorutina() que lo que hace es poner el contador a cero. Este metodo se llama cuando ganamos la partida.
Por otro lado tambien he implementado ifs en la corutina para comprobar que el numero de la cuenta atraas sea mayor que 0, si no seguia descontado numeros hasat acabar la rutina, y añadimos un return@launch en el else, para que si el contador llego a cero, no se sigua ejecuando la rutina, y de esta manera no seguiria funcionando si empezamos otra ronda, solapandose de esta forma corutinas que harian que el contador bajara a mayor velocidad.

    /**
     * comprobar si el boton pulsado es el correcto
     * @param ordinal: Int numero de boton pulsado
     * @return Boolean si coincide TRUE, si no FALSE
     */
    fun comprobar(ordinal: Int): Boolean {

        Log.d(TAG_LOG, "comprobamos - Estado: ${estadoLiveData.value}")
        return if (ordinal == Datos.numero) {
            Log.d(TAG_LOG, "es correcto")
            estadoLiveData.value = Estados.INICIO
            Log.d(TAG_LOG, "GANAMOS - Estado: ${estadoLiveData.value}")
            pararCorutina()
            true
        } else {
            Log.d(TAG_LOG, "no es correcto")
            estadoLiveData.value = Estados.ADIVINANDO
            Log.d(TAG_LOG, "otro intento - Estado: ${estadoLiveData.value}")
            false
        }
    }

    /**
     * Corutina que lanza estados auxiliares
     */
    fun estadosAuxiliares() {
        viewModelScope.launch {
            cuentaAtras.value = Datos.cuentaAtras
            // guardamos el estado auxiliar
            var estadoAux = EstadosAuxiliares.AUX1

            // hacemos un cambio a tres estados auxiliares
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            if(cuentaAtras.value > 0) {
                cuentaAtras.value--
            }else{
                return@launch
            }
            estadoAux = EstadosAuxiliares.AUX2
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            if(cuentaAtras.value > 0) {
                cuentaAtras.value--
            }else{
                return@launch
            }
            estadoAux = EstadosAuxiliares.AUX3
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            if(cuentaAtras.value > 0) {
                cuentaAtras.value--
            }else{
                return@launch
            }
            estadoAux = EstadosAuxiliares.AUX4
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            if(cuentaAtras.value > 0) {
                cuentaAtras.value--
            }else{
                return@launch
            }
            estadoAux = EstadosAuxiliares.AUX5
            Log.d(TAG_LOG, "estado (corutina): ${estadoAux}")
            delay(1000)
            if(cuentaAtras.value > 0) {
                cuentaAtras.value--
            }else{
                return@launch
            }
            estadoLiveData.value = Estados.INICIO
        }
    }

    /**
     * metodo que para la corutina
     */
    fun pararCorutina() {
        viewModelScope.launch {
            cuentaAtras.value = 0
        }
    }