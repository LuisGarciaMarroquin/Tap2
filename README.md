# Diagrama de Clases del Sistema Tinaco

Este es un proyecto que simula un sistema de un tinaco con productores y consumidores de fluidos (agua y aceite), gestionado con reglas de extracción definidas por filtros químicos.

## Diagrama de Clases

A continuación, se presenta el diagrama de clases que describe la estructura del sistema:

```mermaid
classDiagram
    class Tinaco {
        -double agua
        -double aceite
        -final double capacidadMaxima
        +synchronized double getAgua()
        +synchronized double getAceite()
        +synchronized double getTotal()
        +synchronized boolean depositar(String tipo, double cantidad)
        +synchronized boolean extraer(String tipo, double cantidad, FiltroQuimico filtro)
        +synchronized EstadoTinaco getEstado()
    }
    class Tinaco__EstadoTinaco {
        +final double agua
        +final double aceite
        +EstadoTinaco(double agua, double aceite)
    }

    interface FiltroQuimico {
        +boolean validarExtraccion(String tipo, double cantidad, Tinaco__EstadoTinaco estado)
    }

    class FilexOblnico implements FiltroQuimico {
        -final ReglaExtraccion reglaAgua
        -final ReglaExtraccion reglaAceite
        +FilexOblnico()
        +boolean validarExtraccion(String tipo, double cantidad, Tinaco__EstadoTinaco estado)
    }

    interface ReglaExtraccion {
        +boolean validar(Tinaco__EstadoTinaco estado, double cantidad)
    }

    class ReglaExtraccionAgua implements ReglaExtraccion {
        +boolean validar(Tinaco__EstadoTinaco estado, double cantidad)
    }

    class ReglaExtraccionAceite implements ReglaExtraccion {
        +boolean validar(Tinaco__EstadoTinaco estado, double cantidad)
    }

    abstract class Productor extends Thread {
        #Tinaco tinaco
        #Random random
        #String tipoFluido
        +Productor(Tinaco tinaco, String tipoFluido)
        #double generarCantidad()
        #void esperar()
        #void registrarDeposito(double cantidad, boolean exito)
        +void run()
    }

    abstract class Consumidor extends Thread {
        #Tinaco tinaco
        #Random random
        #String tipoFluido
        #FiltroQuimico filtro
        +Consumidor(Tinaco tinaco, String tipoFluido, FiltroQuimico filtro)
        #double generarCantidad()
        #void esperar()
        #void registrarExtraccion(double cantidad, boolean exito)
        +void run()
    }

    class ProductorAgua extends Productor {
        +ProductorAgua(Tinaco tinaco)
        +void run()
    }

    class ProductorAceite extends Productor {
        +ProductorAceite(Tinaco tinaco)
        +void run()
    }

    class ConsumidorAgua extends Consumidor {
        +ConsumidorAgua(Tinaco tinaco, FiltroQuimico filtro)
        +void run()
    }

    class ConsumidorAceite extends Consumidor {
        +ConsumidorAceite(Tinaco tinaco, FiltroQuimico filtro)
        +void run()
    }

    class SimulacionTinaco {
        +static void main(String[] args)
    }

    Tinaco "1" -- "*" Productor : utiliza
    Tinaco "1" -- "*" Consumidor : utiliza
    FiltroQuimico "1" <.. "*" Consumidor : utiliza
    FilexOblnico ..|> FiltroQuimico
    FilexOblnico "1" -- "1" ReglaExtraccionAgua : utiliza
    FilexOblnico "1" -- "1" ReglaExtraccionAceite : utiliza
    ReglaExtraccion <|-- ReglaExtraccionAgua
    ReglaExtraccion <|-- ReglaExtraccionAceite
    Productor <|-- ProductorAgua
    Productor <|-- ProductorAceite
    Consumidor <|-- ConsumidorAgua
    Consumidor <|-- ConsumidorAceite
    Tinaco "1" -- "1" Tinaco__EstadoTinaco : tiene
    FiltroQuimico -- Tinaco__EstadoTinaco : usa
    FilexOblnico -- Tinaco__EstadoTinaco : usa
    ReglaExtraccion -- Tinaco__EstadoTinaco : usa
