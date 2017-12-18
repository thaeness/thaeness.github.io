---
layout: post
title:  "Introducción a Backtracking"
date:   2017-12-15 22:29:00
tags: backtracking
---

En mis clases me encanta mostrar esto a mis alumnos, esta estrategia de programación permite resolver problemas
que son bastante entretenidos, problemas que se ven "complejos" y se solucionan con unas pocas lineas de código.
Si bien podemos obtener una definición técnica en nuestra querida [Wikipedia](https://es.wikipedia.org/wiki/Vuelta_atr%C3%A1s), me gustaría 
dar un ejemplo práctico aplicando esta estrategia.

Me gusta definir esta estrategia como un algoritmo de "fuerza bruta", que en el fondo es lo que es, una estrategia que prueba todas las posibles
combinaciones que se pueden dar para resolver un problema dado. Es importante tener presente que, como cualquier solucion de "fuerza bruta"
esta estrategia puede llegar a hacer brutalmente lenta para encontrar soluciones a ciertos problemas, ya que la solución de un problema determinado
podría incluir miles de millones de posibles combinaciones. Ahora bien, con un poco de ingenio, se puede aplicar "podas" (asi como de podar un árbol), lo
que significa descartar combinaciones que "sabes" que no van a llegar a un buen final o que rompen la lógica del problema en sí. Las podas permiten 
ahorrar cientos, miles o incluso millones de posibles iteraciones/combinaciones, haciendo que el algoritmo mejore considerablemente el tiempo que 
tarda en encontrar la(s) solucion(es). 

Por cierto, se llama "poda" porque las posibles combinaciones, estando en un escenario dado, da paso a varias "ramas" donde cada rama es la 
posible siguiente combinación, por ejemplo, si tenemos los digitos del 1 al 3, y queremos probar todas las posibles combinaciones de 3 digitos (permitiendo repeticiones), se puede
dar el siguiente arbol, partiendo desde el número 1:

[<img src="/assets/backtracking_arbol.png">]

Si bien en este ejemplo no es necesario realizar una poda, porque se probaran todas las posibles combinaciones, incluyendo repeticiones, si quisieramos
agregar una regla, por ejemplo, **que no se repitan números** podríamos realizar varias podas, ya que no necesitariamos recorrer algunas de esas ramas, ahorrandonos
varios ciclos de combinaciones, quedando el arbol de posibles combinaciones asi: 

[<img src="/assets/backtracking_arbol_poda.png">]

Donde las lineas significan un "corte", una "poda", es decir el algoritmo **no** pasará por ahi, no realizará esas combinaciones.

Ahora que queda claro (o no?) como se comporta el algoritmo, pasemos a la parte entretenida.

Antes de entrar derechamente a la parte técnica, uno podría preguntarse, pero que tipo de problemas puedo resolver especificamente con 
esta estrategia?, bueno, pondre unos ejemplos de la vida real asi como ejemplos "académicos" (es decir, que "no sirven de nada" en la vida real)

1. **8 Reinas** (académico): consiste en buscar todas las posibles soluciones en donde se puedan colocar 8 reinas en un tablero de ajedrez
sin que ningúna de ellas se "coma" entre si. Las posibles combinaciones son todas las casillas disponibles dond se puede colocar una reina, se itera 
con cada reina consecutivamente probando todas las posibles combinaciones. Puedes obtener mas información [https://es.wikipedia.org/wiki/Problema_de_las_ocho_reinas](aquí).

2. **Horarios academicos** (real): Si bien esto tambien puede ser resuelto de otras formas, una forma sencilla (quizas no la mas eficiente) es con backtracking,
donde dada salas y horarios disponibles, se debe disponer un profesor en cada segmento. Las restricciones que puede tener la solución 
a este problema es la disponibilidad horaria de cada profesor y/o sala, asi como la cantidad de horas de cada asignatura.

3. **Descubrir contraseña** (real): Ademas de usar diccionarios para "descubrir" contraseñas usando ataques por diccionario (podría ser un tema para otro post..), es posible usar backtracking, aplicando reglas de contraseñas conocidas,
como largos máximos, caracteres a incluir o excluir, etc. de forma tal que luego de cada combinación se pruebe una contraseña.

4. **Laberinto** (mixto): Este podría aplicar para ambos casos, desarrollar un algoritmo capaz de encontrar la solucion a un laberinto, en donde se intenten
todos los posibles caminos, hasta que se encuentre la salida. Este problema puede verse complejo desde fuera, pero con el uso de backtracking es muy sencillo 
de resolver, tan sencillo, que lo usaremos ahora como ejemplo =)


{% highlight java %}
public static void main(String[] args){
 System.out.println("Hola mundo!");
}
{% endhighlight %}

