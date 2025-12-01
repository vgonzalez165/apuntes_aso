```
---------------- ADMINISTRACIÓN DE SISTEMAS INFORMÁTICOS Y REDES ----------------
---------------------------------------------------------------------------------

Módulo:                     ADMINISTRACIÓN DE SISTEMAS OPERATIVOS
Profesor:                   Víctor J. González
Unidad de Trabajo:          UT05
Práctica:                   PR0505. Herencia de GPOs
Resultados de aprendizaje:  RA1
```


# PR0505: Gestión avanzada de Herencia en GPOs

Hasta ahora hemos aplicado GPOs de forma directa. Sin embargo, en un entorno real como el IES San Andrés, las necesidades de un departamento (Informática) a menudo chocan con las normas generales del centro (Alumnado).

## Objetivo de la Práctica

Comprender cómo fluyen las directivas a través de la jerarquía de UOs y cómo manipular este flujo.
Aprenderás a:
1.  Verificar la **herencia predeterminada**.
2.  Utilizar el **boqueo de herencia** para aislar una UO.
3.  Utilizar la opción **Forzado (No Invalidar)** para imponer seguridad crítica.
4.  Entender el orden de precedencia cuando dos GPOs entran en conflicto.

Para ello vamos a aplicar una restricción global **"Nadie debe tener acceso al Símbolo del Sistema (CMD)"** de la que estarán exentos los  alumnos del ciclo de **Informática (DAM, DAW, ASIR)**.

Deberás permitir que los informáticos usen la consola, pero mantener la prohibición para Administración, *sin dejar de aplicar otras normas de seguridad críticas*.


## Parte 1: La prohibición global 

Primero, aplicaremos la norma general a todos los alumnos.

1.  Crea una GPO llamada **`GPO_ALU_PROHIBIDO_CMD`**.
2.  Vincúlala a la UO padre **`Alumnado`**.
3.  Edita la GPO y configura:
    - *Configuración de usuario > Directivas > Plantillas administrativas > Sistema*.
    - Habilita: **"Impedir el acceso al símbolo del sistema"**.
    - (Opcional) En las opciones de la directiva, marca "¿Desea desactivar también el procesamiento de scripts...?" -> **No**.
4.  **Verificación:**
    - Inicia sesión con un alumno de Administración (`alu_afi_1`). Intenta abrir `cmd`. **Debe estar bloqueado**.
    - Inicia sesión con un alumno de Informática (`alu_dam_1`). Intenta abrir `cmd`. **También debe estar bloqueado** (por herencia).


## Parte 2: Bloqueo de Herencia

Ahora vamos a "proteger" a los informáticos de las normas generales que les impiden trabajar.

1.  Abre la consola de **Administración de directivas de grupo** (`gpmc.msc`).
2.  Localiza la UO **`Alumnado \ Informatica`**.
3.  Haz clic derecho sobre la UO `Informatica` y selecciona **Bloquear herencia**.
    - *Observa que aparece un icono de exclamación azul sobre la carpeta.*
4.  **Verificación:**
    - En el cliente, haz `gpupdate /force` y vuelve a entrar con el usuario de informática (`alu_dam_1`).
    - Intenta abrir `cmd`. **Ahora debería funcionar**.
    - **Pregunta de reflexión:** ¿Qué ha pasado con el fondo de pantalla y las restricciones del Panel de Control de la práctica PR0503?
        - *Comprueba si siguen aplicándose. Al bloquear la herencia, has cortado TODAS las GPOs que venían de arriba (incluida la `GPO_ALU_RESTRICCIONES`).*


## Parte 3: Restaurando el orden

Al bloquear la herencia, hemos dejado a los informáticos "desnudos" de configuraciones básicas (como el fondo de pantalla). Necesitamos que tengan el fondo, pero sigan pudiendo usar el CMD.

1.  Busca la GPO **`GPO_ALU_RESTRICCIONES`** (la de la práctica PR0503) en la carpeta "Objetos de directiva de grupo".
2.  Arrástrala y vincúlala **directamente** a la UO **`Alumnado \ Informatica`**.
3.  **Análisis de situación:**
    - La UO `Informatica` tiene herencia bloqueada (ignora lo que viene de `Alumnado`).
    - Pero tiene un vínculo directo a `GPO_ALU_RESTRICCIONES`.
    - **Resultado esperado:** Tienen fondo de pantalla (vínculo directo) y tienen CMD (porque la prohibición está en `Alumnado` y la herencia está bloqueada).



## Parte 4: La seguridad crítica (Forzado / No Invalidar)

El director está preocupado. Al bloquear la herencia en Informática, podríamos haber bloqueado también directivas de seguridad importantes que vengan del Dominio.
Ha ordenado que la **"GPO de Aviso Legal"** (PR0504) se muestre SIEMPRE, sin importar lo que hagan los administradores de las UOs inferiores.

1.  Localiza la GPO **`GPO_Configuracion_Usuario_Dinamica`** (o la que creaste en PR0504 vinculada al dominio/raíz). Si no la tienes vinculada al dominio, vincúlala a la raíz `iessanandres.local`.
2.  En la consola GPMC, selecciona el **vínculo** de esa GPO en la raíz del dominio.
3.  Haz clic derecho sobre el vínculo y selecciona **Forzado** (en inglés *Enforced*, a veces traducido como *No invalidar*).
    - *Observa que aparece un icono de candado sobre el vínculo.*
4.  **Verificación final:**
    - La UO `Informatica` sigue teniendo "Bloquear herencia".
    - Inicia sesión con el alumno de informática.
    - **Comprobación:** ¿Aparece el aviso legal al iniciar sesión?
        - Si aparece, significa que el estado "Forzado" ha atravesado el "Bloqueo de herencia".



## Parte 5: Conflicto de valores (LSDOU)

Vamos a crear una situación donde dos GPOs intentan configurar **exactamente lo mismo** con valores diferentes para ver cuál gana.

1.  Crea una nueva GPO llamada **`GPO_Conflicto_A`** y vincúlala a **`Alumnado`**.
    - Configura: *Directivas > Plantillas admin > Panel de control > Personalización >* **"Forzar una imagen específica de pantalla de bloqueo e inicio de sesión"**.
    - Ruta: `C:\Windows\Web\Screen\img100.jpg` (o una ruta válida cualquiera).
2.  Crea una nueva GPO llamada **`GPO_Conflicto_B`** y vincúlala a **`Informatica`**.
    - Configura **la misma directiva** pero con otra ruta: `C:\Windows\Web\Screen\img105.jpg`.
3.  **Quita el "Bloqueo de herencia"** de la UO `Informatica` para que el flujo sea normal.
4.  **Predicción:**
    - Un alumno de informática recibe la GPO A (desde arriba) y la GPO B (directa).
    - ¿Qué imagen ganará? (Recuerda la regla LSDOU: Local, Site, Domain, OU).
5.  **Comprobación:**
    - Usa el comando: `gpresult /r /user alu_dam_1` en el cliente.
    - Mira la sección "Objetos de directiva de grupo aplicados". ¿Cuál aparece primera (gana)?
