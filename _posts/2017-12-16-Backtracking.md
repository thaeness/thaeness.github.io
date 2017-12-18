---
layout: post
title:  "Backtracking"
date:   2017-12-16 18:40:13
tags: java backtracking programacion
---

Hay cosas que siempre resultan entretenidas, ciertos problemas en los que uno se maravilla como la programación puede resolverlos de forma "facil y bonita", y creo que
este tema es uno de esos. El backtracking es una estrategia de programación que nos permite resolver problemas, por decirlo de alguna forma; con "fuerza bruta", es decir, 
probar todas las posibles combinaciones hasta que encontremos la solución (o las soluciones). Nuestra buena amiga wikipedia nos da una idea mas acabada sobre esto [aquí](https://es.wikipedia.org/wiki/Vuelta_atr%C3%A1s).

La idea es simple: comenzar a probar todas las combinaciones de los elementos en juego para dar con el resultado final. Por ejemplo, supongamos que queremos saber todas las posibles
combinaciones que se pueden dar con los dígitos del 1 al 3 y que el resultado tenga siempre 3 dígitos. Una forma gráfica de ver esto, es en forma de arbol invertido, donde 
cada paso que se avanza hacia abajo se va acumulando para formar la respuesta final:

[<img src="/assets/backtracking_arbol.png">]

Cada vez que se llega a una "hoja" del arbol (aquél elemento que no tiene hijos) es una solución, por ejemplo si seguimos el camino desde el inicio, siempre por el lado izquierdo,
tendremos la solucion 111, que correspondería a la primera solución, mientras que si seguimos el camino por el lado derecho, la combinación obtenida es la 333, correspondiente
a la última solución posible.

Ahora, cuando comienzan a aparecer mas reglas o limitaciónes a nuestra solución, entra en juego la "poda" (si, como podar árboles), la cual consiste en cortar ramas de ejecución, es decir, no 
ralizar combinaciones que sabemos que no son parte de la solución o que no van por buen camino. Supongamos que agregamos la siguente regla a este problema: ya no se pueden repetir 
números. Como resultado, al algoritmo se le incluye esta poda, para no realizar iteraciones sobre elementos que no son parte de la solución, por lo que el arbol de las
posibles combinaciones quedaría así:

[<img src="/assets/backtracking_arbol_poda.png">]

Las lineas dobles representan la poda, por donde el algoritmo **no va pasar**, omiti algunas lineas porque estaba complicado dibujarlas ahi, pero los elementos los pinté blancos, 
por ahi tampoco pasa el algoritmo, son parte de la poda. La poda nos permite ahorrar ejecuciones, de forma tal que nuestro algoritmo se vuelva mas eficiente, ya que si comprobamos
solo al final de tener los 3 digitos si tenemos o no números repetidos, vamos a estar realizando iteraciones extra que no son necesarias. Ahora, este ejemplo es pequeñisimo, con 
muy pocas combinaciones, cuando en la realidad, los problemas que se resuelven con backtracking tienes millones, miles de millones o incluso mas posibles combinaciones, y 1 sola poda
podría ahorrar cientos, miles de cientos, miles de millones o más iteraciones, por lo que son neceseraias y parte de esta estrategia.

En un principio puede ser difícil ver que tipo de problemas pueden ser resueltos con ésta estrategia, ya que al mencionar "combinaciones" lo primero que se piensa es en números,
pero la realidad es que pueden ser tambien combinaciones de "opciones", por ejemplo, existen algunos problemas de estudio académico típicos para resolver con backtracking,
como el del caballo de ajedrez que debe recorrer todo el tablero sin repetir ninguna posición, o colocar 8 reinas en el tablero sin que se coman entre ellas. Para ambos problemas,
que pueden ser representados facilmente en una matriz cuadrada, las combinaciones que se pueden dar corresponden a los posibles movimientos del caballo en cada iteración según donde
este parado, o donde colocar la siguiente reina sin que se coma con las que ya estan. Estos problemas están ampliamente documentados en internet, siguiendo con nuestra querida amiga
wikipedia, puedes ver dichos ejemplos [aquí](https://es.wikipedia.org/wiki/Problema_del_caballo) y [aquí](https://es.wikipedia.org/wiki/Problema_de_las_ocho_reinas).

En este post vamos a hacer un ejemplo de backtracking desarrollando un algoritmo que sea capaz de encontrar la salida de un laberinto, para ello, vamos a representar el laberinto
en una matriz de CHAR (caracteres ascii), donde cada casilla tiene 3 posibles valores:
1. '#' representa un "muro", por donde **no** se puede pasar.
2. ' ' (espacio en blanco) representa un camino, una opción por donde avanzar.
3. 'o' (letra O minúscula) representa un "paso", un avance por el camino.


Me he dado el trabajo de hacer una matriz, la puedes copiar y pegar en un archivo de texto, o crear tu propia matriz. 
Para facilitar el trabajo, al final de este post hay una funcion que convierte un archivo de texto en este formato y lo convierte a una matriz.

{% highlight text %}
       #######         ###           ###                     ##               ##########
 ##### ####### ####### ### ######### ### ################### ## ############# #       ##
 ##### ####### ##   ## ### ##     ## ### ####             ## ##            ## # ##### ##
 #####         ## #### ### ## ### ## ### #### ########### ## ############# ## # ##### ##
 ####### ######## #### ### ## ### ## ### #### ##          ##         ##### ## #    ## ##
 ####### ###      #### ### ## ### ## ### #### ## ################### ##### ## #### ## ##
 ####### ### ######### ### ## ### ## ### #### ## #######         ###       ##      ## ##
 ####### ### ###       ### ## ### ## ###      ##         ####### #################### ##
 ###       # ### ##### ### ## ### ## ################## ########    #         ####### ##
 ### ##### # ### ##### ###    ### ##                    ########### # ####### ####    ##
 ### ##### # ### ##### ########## ####### ##################    ### #      ## #### #####
 ###    ## # ### #####            ####### ###          ##### ## ### ###### ## #### #####
 ###### ## # ### ######################## ###  #######       ## ### ###### ## #### #####
        ## # ### ####                     #### ################ ###        ## #### #####
########## # ### #### ################### #### ################ ############# #### #####
####         ### #### ################### #### #####     ######    ###        #### #####
#### ####### ### ####               ##### #### ##### ### ######### ### ########### #####
#### ####### ### ############## ######### ##   ##### ###       ### ### ###         #####
             ### ##     ###               ## ####### ### ##### ### ### ### #############
 ########### ### ###### #### ############ ## ###     ### ##### ### ### ### #############
 ########### ###        #### ###       ## ## ### #######       ### ### ### #############
 ####      # ### ########### ### ##### ## ## ### ################# ### ###          ####
 #### #### # ### ##       ## ### ##### ## ## ### #####             ### ############ ####
 #### #    # ### ## ##### ## ### ##### ## ## ### ##### ###############      ####### ####
 #### # #### ### ## ## ## ## ### ##### ## ## ### ##### ####       ######### ####### ####
 #### # #    ### ## ## ## ###### ##### ## ## ### ##### #### ##### ######### ###         
      # # ## ### ##    ## ###### ##### ## ## ### ##### #### ##### ######### ########### 
####### # ## ### ######## ####      ##### ## ### ##### #### ##### #########        #### 
###     # ## ###          #### ####     ####     ##### #### #####       ########## #### 
### ##### ## ### ############# #### ################## #### ###################### ##   
###       ## ################# ####                ### ####                  ##### ## ##
############ ####      ####### ################### ### ##################### ##### ## ##
###          #### #### ####### #######             ### ##################### ##### ## ##
### ############# ####         ####### ############### ####           ###### ##### ## ##
###               ####################                      #########                   
}
{% endhighlight %}

Entonces, vamos a crear un algoritmo, usando backtracking, que pueda resolver este (y cualquier otro) laberinto que cumpla estas condiciones:
1. Debe ser una matriz N x M (cualquier ancho por cualquier alto) de caracteres, donde los espacios sean camino y los "#" sean muro.
2. Solo se puede avanzar hacia arriba, abajo, izquierda o derecha, siempre que no haya un muro.
3. El laberinto debe tener su entrada en la posición 0,0 (esquina superior izquierda) y su salida en la posición n-1,m-1 (esquina inferior derecha).

Para comenzar a desarrollar, debemos aprender que la mayoría, si no todos, los algoritmos de backtracking tienen la siguiente estructura:
{% highlight text %}

funcion bt(siguientePaso){
 si(estadoActualEsSolucion()){
     mostrarSolucion();
 }

 si(siguientePaso no es valido)return;
 //insertar podas aquí

 for(posibles combinaciones siguientes){
   avanzar(siguientePaso);
   bt(siguientePaso + 1); //lamada recursiva
   retroceder(siguientePaso);
 }
}

{% endhighlight %}

En resumen, consiste en programar una función recursiva que:
1. Valide que el estado actual del problema es una solución o no, si lo es, entonces que la muestre
2. Bloque de validaciones que verifiquen que la combinación actual es valida, de lo contrario, que "corte" esa ejecución, que no continue por ese camino.
3. Bloque de Poda, todas las validaciones que sean necesarias para validar que ese camino va por buen rumbo, de lo contrario, al igual que el bloque anterior, que corte las siguientes ejecuciones.
4. Representado con un FOR (que no necesariamente es asi), el bloque principal, en donde:
  1. Se "efectua" la combinación, en este ejemplo, avanzar a la siguiente casilla válida en la matriz. 
  2. Se llama recursivamente a la misma funcion con el siguiente paso.
  3. Se "deshace" lo que se hizo en el paso uno, la vuelta atras (o backtracking en inglés), para que el algoritmo siga probando los demás caminos.


Como primer paso, debemos declarar nuestra clase y sus atributos, he decidido llamar a esta clase Laberinto, cuyas propiedades serán una matriz de char,y las variables
estaticas que representan los distintos elementos que no sufren modificación durante la ejecición de nuestro algoritmo (esto, para no incluir 
antipatrones como el [Magic String](https://en.wikipedia.org/wiki/Magic_string) o el [Magic Number](https://goo.gl/P5AB6S) ).

{% highlight java %}
public class Laberinto {

    private static char[][] LABERINTO;
    private static char MURO = '#';
    private static char CAMINO_LIBRE = ' ';
    private static char PASO = 'o';
    private static char SALTO_LINEA = '\n';
}

{% endhighlight %}

Luego, tenemos que implementar nuestro algoritmo backtracking, siguiendo la estructura mencionada mas arriba, pondre el codigo necesario aquí y lo explico mas abajo:

{% highlight java %}
public void resolverLaberinto(int fila, int columna) {

    if (esSolucion()) {
        mostrar();
        return;
    }

    if (filaEsInvalida(fila) || columnaEsInvalida(columna) || esMuro(fila,columna) || yaPasePorAhi(fila,columna)) {
        return;
    }

    LABERINTO[fila][columna] = PASO; //avanzo!
    resolverLaberinto(fila + 1, columna); // derecha
    resolverLaberinto(fila - 1, columna); // izquierda
    resolverLaberinto(fila, columna + 1); // arriba
    resolverLaberinto(fila, columna - 1); // abajo
    LABERINTO[fila][columna] = CAMINO_LIBRE; //vuelta atras! backtracing!
}

{% endhighlight %}

Tenemos en una primera instancia nuestra funcion "resolverLaberinto", cuyos argumentos son "fila" y "columna" representando la posicion actual X,Y dentro de nuestra matriz,
la primera vez que esta funcion sea llamada, los argumentos serán 0,0 indicando de que estamos iniciando en la entrada de nuestro laberinto (esquina superior izquierda).
Lo primero que debemos hacer, es verificar si el estado actual de nuestra matriz y el camino recorrido es una solucion válida. Ésto esta siendo validado por la funcion "esSolucion",
pero, que hace esa funcion? por ahora no importa, solo asumamos de que valida que el laberinto ya fue solucionado (es decir, llegamos a la esquina inferior derecha).
Si el estado actual de nuestro laberinto indica que ya fue solucionado, entonces mostramos la solucion con la funcion "mostrar", la cual desplegará en pantalla el laberinto y el camino recorrido.
Si bien nuestra funcion es de tipo "void" (lo cual quiere decir que no retornará nada) tenemos un "return" dentro de este primer if, ese return indica que la funcion se termina (ojo,
que al ser una funcion recursiva, indica que solo termina "esa" llamada, y no las otras que esten en curso mas arriba, como padre).

Luego, viene nuestro bloque de validaciones, representado con un if que verifica que la posicion X,Y es valida dentro de la matriz (que no nos hemos salido de ella), ademas, valida
que la posición donde nos intentamos mover no sea un muro o ya hayamos pasado por ahí. El como funcionan esas funciones, otra vez, no importa por ahora, de hecho, asi es como al menos 
yo recomiendo programar, usar funciones que faciliten nuestro trabajo, sin importar si la funcion existe o no, si no existe la creamos luego, pero al usarlas ahora, hace que 
nuestro codigo sea mas simple de leer, mas entendible, dejando de lados validaciones que aunque sean cortas de hacer, ensucian la lógica principal. Si cualquiera de las condiciones
en el IF se cumple, entonces quiere decir que la posicion X,Y ingresada no es valida, ya sea porque esta fuera de la matriz, por que es un muro o ya se pasó por ahí.

El tercer bloque de código, es el corazón de backtracking, donde, la primera acción es marcar la posición de la matriz que ingreso como argumento, en la matriz como un PASO dado,
es decir con una 'o'. Una vez marcado ese paso, damos todos los posibles pasos (combinaciones) que podemos hacer desde donde estamos ahora, esto es, avanzar hacia arriba,
abajo, a la izquierda o a la derecha. Aqui la recursividad realizará su trabajo, recorriendo todos los posibles caminos desde donde estoy parado ahora, y una vez que los recorra
todos, "deshago" ese avance, para que pueda retroceder y probar el siguiente camino.

Y por increible que parezca, eso es todo! ese pequeño algoritmo puede resolver cualquier laberinto con las condiciones explicadas mas arriba, ahora solo nos queda 
implementar todas las funciones que usamos en este bloque, que son de apoyo para solucionar el problema principal. 

Como sabemos cuando el estado actual del labertinto es la solucion?, cada vez que pongamos un paso en la casilla de la esquina inferior derecha, es decir la posicion n-1,m-1 
(donde n es el alto y m es el ancho, además, se resta 1 porque las matrices en java comienzan desde el indice 0 y no del 1).

{% highlight java %}
private boolean esSolucion() {
    return LABERINTO[LABERINTO.length - 1][LABERINTO[0].length - 1] == PASO;
}
{% endhighlight %}

Como validaciones básicas, tenemos que verificar que la posición a la que tengo que avanzar no se encuentra fuera de la matriz, esto lo realizamos
con 2 funciones por separado (no necesariamente, es solo para tener mas claridad en el código):

{% highlight java %}
public boolean filaEsInvalida(int fila) {
    return fila < 0 || fila >= LABERINTO.length;
}

public boolean columnaEsInvalida(int columna) {
    return columna < 0 || columna >= LABERINTO[0].length;
}
{% endhighlight %}

Luego, tenemos la función que valida si la posición a la que deseo avanzar es un muro o no, recordemos que un muro es representado por un "#", por lo 
que solo tenemos que comprar si la posición actual es un # o no (para eso utilizamos la variable estatica definida al principio).

{% highlight java %}
public boolean esMuro(int fila,int columna){
   return LABERINTO[fila][columna] == MURO;
}
{% endhighlight %}

Nos queda la funcion que valida si acaso ya pasamos por una casilla determinada, esto se deduce facilmente, ya que cada vez que pasamos por una casilla
la marcamos con una "o" (que la definimos como variable estatica llamada PASO):

{% highlight java %}
public boolean yaPasePorAhi(int fila,int columna){
   return LABERINTO[fila][columna] == PASO;
}
{% endhighlight %}

En este punto ya estan definidas todas las funciones que trabajan en equipo para solucionar el problema de encontrar la salida al laberinto, solo nos queda definir las
funciones de apoyo "mostrar" y la ultima que nos permite cargar un archivo de texto a la matriz.

La funcion para mostrar es muy simple, solo se recorre la matriz en todo su esplendor, guarandola en un String, y por ultimo se imprime el string por pantalla,
para optimizar en algo el tiempo de ejecución y la memoria ram, se utiliza StringBuilder, una clase Java que nos permite construir String sin necesidad de estar
concatenando (que para java significa crear en cada concatenación un objeto nuevo, y crear objetos es "caro" en terminos de recursos).

{% highlight java %}
private void mostrar() {
    StringBuilder out = new StringBuilder();
    for (int fila = 0; fila < LABERINTO.length; fila++) {
        for (int columna = 0; columna < LABERINTO[0].length; columna++) {
            out.append(LABERINTO[fila][columna]);
        }
        out.append(SALTO_LINEA);
     }
    System.out.println(out);
}
{% endhighlight %}

La ultima función que dejo aquí, es el utilitario para cargar el archivo de texto a la matriz, la cual simplemente carga un archivo a un String, se separa en lineas, y cada
linea se separa en caracteres, guardando cada uno en la posición que le corresponde dentro de la matriz:

{% highlight java %}
public void cargarLaberinto(String path) {
    try {
        boolean yaSeInicializoElLaberinto = false;
        int fila = 0;
        String[] lineas = new String(Files.readAllBytes(Paths.get(path))).split(SALTO_LINEA);
        for (String linea : lineas) {
            String[] columnas = linea.split("");
            if (!yaSeInicializoElLaberinto) {
                LABERINTO = new char[lineas.length][columnas.length];
                yaSeInicializoElLaberinto = true;
            }
            for (int columna = 0; columna < LABERINTO[0].length; columna++) {
                LABERINTO[fila][columna] = columnas[columna].charAt(0);
            }
            fila++;
        }
    } catch (IOException ex) {
        Logger.getLogger(Laberinto.class.getName()).log(Level.SEVERE, null, ex);
    }
}
{% endhighlight %}

Para ejecutar nuestro codigo, podemos crear un main que cree el objeto y llame a nuestra funcion recursiva que implementa el backtracking:

{% highlight java %}
public static void main(String[] args) {
    Laberinto laberinto = new Laberinto();
    laberinto.cargarLaberinto("/ruta/al/archivo.txt"); //pon la ruta de tu archivo
    laberinto.resolverLaberinto(0, 0);
}
{% endhighlight %}

Al ejecutarlo, derías tener el laberinto resuelto, en este caso, con el laberinto puesto mas arriba, el resultado de la ejecución del código es el siguiente:

{% highlight text %}
o      #######ooooooooo###ooooooooooo###                     ##               ##########
o##### #######o#######o###o#########o### ################### ## ############# #       ##
o##### #######o##   ##o###o##ooooo##o### ####             ## ##            ## # ##### ##
o#####  ooooooo## ####o###o##o###o##o### #### ########### ## ############# ## # ##### ##
o#######o######## ####o###o##o###o##o### #### ##          ##         ##### ## #    ## ##
o#######o###      ####o###o##o###o##o### #### ## ################### ##### ## #### ## ##
o#######o### #########o###o##o###o##o### #### ## #######ooooooooo###       ##      ## ##
o#######o### ###      o###o##o###o##o###      ##       oo#######o#################### ##
o###ooooo  # ### #####o###o##o###o##o##################o########oooo#ooooooooo####### ##
o###o##### # ### #####o###oooo###o##oooooooooooooooooooo###########o#o#######o####    ##
o###o##### # ### #####o##########o####### ##################    ###o#oooooo##o#### #####
o###oooo## # ### #####oooooooooooo####### ###          ##### ## ###o######o##o#### #####
o######o## # ### ######################## ###  #######       ## ###o######o##o#### #####
oooooooo## # ### ####                     #### ################ ###oooooooo##o#### #####
########## # ### #### ################### #### ################ #############o#### #####
####         ### #### ################### #### #####     ######    ###oooooooo#### #####
#### ####### ### ####               ##### #### ##### ### ######### ###o########### #####
#### ####### ### ############## ######### ##   ##### ###       ### ###o###         #####
             ### ##     ###               ## ####### ### ##### ### ###o### #############
 ########### ### ###### #### ############ ## ###     ### ##### ### ###o### #############
 ########### ###        #### ###       ## ## ### #######       ### ###o### #############
 ####      # ### ########### ### ##### ## ## ### ################# ###o###          ####
 #### #### # ### ##       ## ### ##### ## ## ### #####             ###o############ ####
 #### #    # ### ## ##### ## ### ##### ## ## ### ##### ###############oooooo####### ####
 #### # #### ### ## ## ## ## ### ##### ## ## ### ##### ####       #########o####### ####
 #### # #    ### ## ## ## ###### ##### ## ## ### ##### #### ##### #########o###         
      # # ## ### ##    ## ###### ##### ## ## ### ##### #### ##### #########o########### 
####### # ## ### ######## ####      ##### ## ### ##### #### ##### #########oooooooo#### 
###     # ## ###          #### ####     ####     ##### #### #####       ##########o#### 
### ##### ## ### ############# #### ################## #### ######################o##   
###       ## ################# ####                ### ####                  #####o## ##
############ ####      ####### ################### ### ##################### #####o## ##
###          #### #### ####### #######             ### ##################### #####o## ##
### ############# ####         ####### ############### ####           ###### #####o## ##
###               ####################                      #########             oooooo

============================
BUILD SUCCESSFUL (total time: 1 second)
{% endhighlight %}

Como puedes observar, se ha llenado en la matriz el camino de la solución con puras 'o', desde la esquina superior izquierda a la esquina inferior derecha.

Backtracking puede ser utilizado para resolver muchos tipos de problemas que necesiten combinaciones, por ejemplo armar un calendario academico, con profesores, horarios, asignaturas
y otras restricciones, es posible armar un algoritmo que pruebe todas las posibles combinaciones para encontrar la mejor alternativa. Es posible armar algoritmos para encontrar
mejores rutas entre 2 o mas puntos, para encontrar el camino que utilice menos combustible, o tarde menos tiempo, o la restricción que se quiera. Esta estrategia muy versatil y útil
sin embargo se debe tener presente que algunos problemas, dependiento de la cantidad de combinaciones posibles en cada paso, podria tardar minutos, horas o incluso días en encontrar
una solución.

Aquí abajo dejo la clase completa, incluyendo el main:

{% highlight java %}
package io.github.thaeness.backtracking;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.util.logging.Level;
import java.util.logging.Logger;

/**
 *
 * @author Michel M. <michel@febos.cl>
 */
public class Laberinto {

    private static char[][] LABERINTO;
    private static char MURO = '#';
    private static char CAMINO_LIBRE = ' ';
    private static char PASO = 'o';
    private static char NUEVA_LINEA = '\n';

    public static void main(String[] args) {
        Laberinto laberinto = new Laberinto();
        laberinto.cargarLaberinto("/Users/michel/Documents/Laberinto.txt");
        laberinto.resolverLaberinto(0, 0);
    }

    public void resolverLaberinto(int fila, int columna) {
        if (esSolucion()) {
            mostrar();
            return;
        }
        if (filaEsInvalida(fila) || columnaEsInvalida(columna) || esMuro(fila,columna) || yaPasePorAhi(fila,columna)) {
            return;
        }
        LABERINTO[fila][columna] = PASO; //avanzo!
        resolverLaberinto(fila + 1, columna); // derecha
        resolverLaberinto(fila - 1, columna); // izquierda
        resolverLaberinto(fila, columna + 1); // arriba
        resolverLaberinto(fila, columna - 1); // abajo
        LABERINTO[fila][columna] = CAMINO_LIBRE; //vuelta atras! backtracing!
    }
    
    private boolean esSolucion() {
        return LABERINTO[LABERINTO.length - 1][LABERINTO[0].length - 1] == PASO;
    }

    public boolean filaEsInvalida(int fila) {
        return fila < 0 || fila >= LABERINTO.length;
    }

    public boolean columnaEsInvalida(int columna) {
        return columna < 0 || columna >= LABERINTO[0].length;
    }
    
    public boolean esMuro(int fila,int columna){
        return LABERINTO[fila][columna] == MURO;
    }
    public boolean yaPasePorAhi(int fila,int columna){
        return LABERINTO[fila][columna] == PASO;
    }

    private void mostrar() {
        StringBuilder out = new StringBuilder();
        for (int fila = 0; fila < LABERINTO.length; fila++) {
            for (int columna = 0; columna < LABERINTO[0].length; columna++) {
                out.append(LABERINTO[fila][columna]);
            }
            out.append(NUEVA_LINEA);
        }
        System.out.println(out);
        System.out.println("============================");
    }

    public void cargarLaberinto(String path) {
        try {
            boolean init = false;
            int fila = 0;
            String[] lineas = new String(Files.readAllBytes(Paths.get(path))).split("\n");
            for (String linea : lineas) {
                String[] columnas = linea.split("");
                if (!init) {
                    LABERINTO = new char[lineas.length][columnas.length];
                    init = true;
                }
                for (int columna = 0; columna < LABERINTO[0].length; columna++) {
                    LABERINTO[fila][columna] = columnas[columna].charAt(0);
                }
                fila++;
            }

        } catch (IOException ex) {
            Logger.getLogger(Laberinto.class.getName()).log(Level.SEVERE, null, ex);
        }
    }
}
{% endhighlight %}

