# Asyncio Learn

## Capítulo 1. Concurrencia con Asyncio
Una **corutina** es una función que puede ser suspendida y reanudada. Tienen
el control sobre cuando son suspendidas.
Puede ser suspendida por varias razones, como la ejecución de otra corutina 
para esperar otra task o esperar por un recurso externo. Las corrutinas son
usadas para **concurrencia**. Muchas corutinas pueden ser ejecutadas a la vez.
Una **subrutina** es una función que puede ser ejecutada, empezando desde un
punto y finalizada en otro punto. 
Entonces, una corutina puede ser ejecutada después de ser suspendida, y 
reanudada muchas veces antes de su finalización.

La diferencia entre multihebra y multitasking es que la multihebra es 
gestionada por el SO. Él es quien decide cuál hebra ejecutar y cuál detener.
En el multitasking, son ellas mismas quien toman estas deiciones.

### Corutina vs rutina vs subrutina
Rutina --> programa  
Subrutina --> función del programa  
Corutina --> extensión de subrutina. Un tipo especial de subrutina 
> Una rutina tiene subrutinas (un programa tiene funciones)

Entonces, la gran diferencia es que la **subrutina se ejecuta de principio a 
fin y la corutina se puede pausar durante su ejecución las veces que queramos**

Cuando una corutina lanza otra corutina, debe parar su ejecución y continuar 
con la nueva.

### Corutina vs generador
Un generador es una función especial que puede pausar/suspender su ejecución, 
retornar un valor y recuperar su flujo al terminar la función llamada.
Una corutina puede suspenderse usando **await**. Será reanudada cuando la 
rutina haya sido completada.
> await (asyncio) --> misma función que *yield* en generador

### Corutina vs thread
Una thread es una hebra de ejecución en el sistema operativo. Cada programa es
un proceso y tiene al menos una hebra (la hebra principal). Se pueden ejecutar
múltiples subprocesos (o hebras), que tendrán como padre la hebra principal.

El SO es quien controla la ejecución de dichas hebras. Una corutina es mucho
más liviano que una hebra, ya que es definida como una función. IMPORTANTE: una 
hebra puede ejecutar múltiples corutinas.
> Las corutinas son más rápidas de crear y ejecutar y toman menos memoria. 

Por lo tanto, una hebra es un "objeto independiente", mientras que las corutinas
son "funciones" dentro de una misma hebra, que se van "turnando".

### ¿Qué es la programación asíncrona?
Asíncrono significa "no al mismo tiempo". Cuando programamos algo asíncrono, 
significa que es una acción requerida que se ejecutará más adelante.  
> Un **futuro** es un identificador de la llamada asíncrona con el fin de 
> comprobar el estado y tomar la devolución de datos

> Una **task** se refiere a un agregado de una llamada asíncrona junto con su 
> futuro.

La programación asíncrona se utiliza para I/O no bloqueantes. Es decir, lecturas
y escrituras, por ejemplo, cuyo resultado es devuelto más adelante.  
En Python, la programación asíncrona se implementa, por ejemplo, con **asyncio**,
que nos brinda operaciones *async/await*. Se implementa usando corutinas que corren
en un bucle de eventos que corren sobre **una hebra**.

> async def --> Corutina
````python
async def my_coroutine():
    ...
````
Llamar a una corutina, devuelve un objeto corutina. 
> await --> suspenderá la llamada y planificará la corutina para ejecutarse. La
> devolución no se producirá hasta que la corutina termine. 

Es necesario definir un *event loop* para trabajar con corutinas en Python. Este
event loop se puede crear y ejecutar con **asyncio.run()**. Desde este event loop 
nosotros llamamos a la corutina "padre".
> asyncio.run() --> inicia el bucle de eventos. Toma una corutina como argumento

### ¿Cuándo usar asyncio?
1. I/O no bloqueantes, como solicitudes HTTP, conexiones a base de datos, etc
2. Lectura y escritura de archivos 
3. La programación asíncrona es preferida o requerida.  

Las ventajas de asyncio frente a las hebras son:
- Menor sobrecarga: menos consumo de memoria y mejor y más rápido cambio de contexto.
- Escalabilidad: pueden manejar muchas más conexiones que las hebras
- Simplicidad en manejo de estados: es más sencillo manejar estados de corutinas.

### Resumen capítulo 1
1. Una corutina es una función que puede ser pausada y reanudada varias veces hasta
que finaliza. Se define como ``async def my_async_function()``
2. Se utiliza ``asyncio.run(my_async_routine())`` para lanzar el bucle de eventos.
3. Asyncio se utiliza para I/O no bloqueantes, con grandes necesidades de una
rápida construcción y lanzamiento (mejores que con hebras).
