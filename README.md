# Asyncio Learn

## Capítulo 1. Concurrencia con Asyncio
Una **corutina** es una función que puede ser suspendida y reanudada. Tienen
el control sobre cuando son suspendidas.
Puede ser suspendida por varias razones, como la ejecución de otra corutina 
para esperar otra task o esperar por un recurso externo.

Muchas corutinas pueden ser ejecutadas a la vez, por lo que permitimos la 
concurrencia.  
Una **subrutina** es una función que puede ser ejecutada, empezando desde un
punto y finalizada en otro punto. 
Por lo tanto, una corutina puede ser ejecutada después de ser suspendida, y 
reanudada muchas veces antes de su finalización.

La diferencia entre multihebra y multitasking es que la multihebra es 
gestionada por el SO. Él es quien decide cuál hebra ejecutar y cuál detener.
En el multitasking, son "ellas mismas" quien toman estas decisiones.

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
> await (asyncio) --> misma función que *yield* en generador. Pausa su ejecución
> y continua la otra.

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
> comprobar el estado y tomar la devolución de datos.

> Una **task** se refiere al conjunto de una llamada asíncrona junto con su 
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
> devolución no se producirá hasta que la corutina termine. Es como ejecutar yield

Es necesario definir un *event loop* para trabajar con corutinas en Python. Este
event loop se puede crear y ejecutar con **asyncio.run()**. Desde este event loop 
se llama a la corutina "padre".
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

## Capítulo 2. Corutinas y tasks
Una manera de ejecutar una rutina es pasarla como parámetro a ``asyncio.run()``.  
Otra es utilizar la sentencia ``await``. Por ejemplo, ``await my_coro()``
````python
python3 example_await.py
````
En este ejemplo, se planifica *my_first_coroutine*. Se suspende su ejecución
para lanzar *my_second_coroutine* y continua cuando ha terminado. 

### ¿Cómo crear y correr Tasks?
Las tasks son un wrapper para ejecutar corutinas de manera independiente. Una 
task proveeo de un identificador de una rutina planificada con el fin de 
interactuar con ella. 
> La diferencia entre llamar a una corutina y crear una task es que la task no
> hay que esperarla (no hay que poner await my_coroutine()). Sin embargo, si no 
> se esperan con await, al terminar la ejecución se pausará la posible ejecución
> sin devolver nada.

````python
python example_tasks.py
````

En este ejemplo, creamos dos task que se planifican en el event loop. Es necesario
poner un await dentro de las tasks para que se puedan ir turnando los print, 
ya que sin los await la primera task se ejecutaría hasta terminar y luego 
continuaría la otra task. Si las lanzamos sin crear una task (con un await),
deberíamos hacer un ``await my_task()`` y 
deberíamos esperar (pase lo que pase en el interior de la corutina) su 
finalización. 

Cuando usamos await en una coroutine, como await my_task(), estás diciendo:
"Ejecuta esta coroutine hasta que se pause (por una operación de 
entrada/salida o await interno), luego continúa con la coroutine actual 
(la que está esperando)".

Con las funciones *get_name()* y *result()* podemos obtener el 
nombre y el return de la task.

### ¿Cuándo se ejecuta una task tras crearla?
Aun ejecutando ``asyncio.create_task()``, puede que no se ejecute inmediatamente.
Se ejecutará cuando el event loop tenga la oportunidad de hacerlo. Es decir, 
cuando se suspenda la corutina que se ejecuta actualmente en el event loop y le 
toque ejecutarse a esa task. 

### ¿Cómo utilizar Gather para esperar todas las task creadas?
asyncio.gather es una manera de esperar una lista de tasks. Imaginemos
que tenemos que procesar 100 ficheros. Debemos crear entonces 100 tasks
para procesarlos todos de manera concurrente. Pero sería muy sucio hacer
un await por cada una de las tasks. Para ello utilizamos asyncio.gather
y esperamos una lista de tasks.
````python
python example_gather.py
````

### Captura de excepciones en corrutinas
Podemos capturar la excepción lanzada por la corrutina utilizando
``task.exception()``

### Cancelación de task
Podemos cancelar una task mediante ``task.cancel()``. Nos devolverá
True si se ha cancelado correctamente y False en caso contrario.
IMPORTANTE: si la tarea ha acabado y llamamos a task.cancel() nos devolverá
False. 

Si se ha recibido correctamente, la siguiente ejecución de esa
corrutina devolverá un CancelledError.
### Callback de correcta finalización.
Podemos asignar un manejador que se ejecute cuando la Task acaba
correctamente. Para ello usamos ``task.add_done_callback(function_to_call())``
La función a llamar debe tener como primer parámetro la Task.

## Capítulo 3. Colecciones de Tasks

