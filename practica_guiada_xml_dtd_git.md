#  Creación y validación de documentos XML con Git

## 1. Objetivos de aprendizaje

Al finalizar la práctica, el estudiante será capaz de:

-   diseñar documentos XML a partir de información no estructurada;
-   identificar elementos y atributos;
-   construir documentos XML bien formados;
-   definir DTD internos y externos;
-   utilizar cardinalidades y restricciones de atributos;
-   validar documentos XML;
-   gestionar incrementalmente los artefactos mediante Git.

``` text
Problema → Modelar información → Crear XML → Comprobar buena formación
→ Diseñar DTD → Validar XML → Pruebas negativas → Registrar cambios con Git
```

## 2. Preparación del repositorio

Cree el proyecto:

``` bash
mkdir xml-dtd-practica
cd xml-dtd-practica
git init
```

Estructura inicial:

``` text
xml-dtd-practica/
├── README.md
├── ejercicio1/
├── ejercicio2/
└── ejercicio3/
```

Realice el primer commit:

``` bash
git add .
git commit -m "Inicializar estructura de la práctica XML"
git log --oneline
```

## 3. Ejercicio 1 --- De texto no estructurado a XML

A partir del texto del pedido, identifique destinatario, artículo,
dirección y fecha de entrega.

| Información  | Valor identificado | Elemento XML propuesto                 |
|--------------|--------------------|----------------------------------------|
| Destinatario | Angel Lerma        | &lt;destinatario&gt; o &lt;cliente&gt; |
| Artículo     | Laptop             | &lt;articulo&gt; o &lt;producto&gt;    |
| Dirección    | Unison 5J 203      | &lt;direccion&gt;                      |
| Fecha        | 2026-10-05         | &lt;fecha&gt;                          |

Proponga una jerarquía. Considere si la dirección debe descomponerse en
calle, número, piso y letra.

### Preguntas

1.  ¿Conviene almacenar la dirección como un único texto? no por que puede dificultar las busquedas o validaciones por ejemplo al buscar todos los pedidos de un mismo area
2.  ¿Qué ventajas tendría separar sus componentes? facilita la extraccion de datos especificos, permite ordenar o filtrar informacion mas facilmente 
3.  ¿Cómo debería almacenarse una fecha para facilitar su procesamiento? utilizando un formato estandar YYYY-MM-DD (ISO 8601) 
4.  ¿Qué información podría ser atributo y cuál elemento? los elementos la informacion principal que representa el contenido del negocio como destinatario direccion articulo y los atributos metadatos o identificadores que describen a las entidades como id o codigo del articulo.

Cree `ejercicio1/pedido.xml` comenzando con:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
```

Compruebe que existe un solo elemento raíz, las etiquetas están
cerradas, el anidamiento es correcto y la información requerida puede
localizarse independientemente.

Registre el avance:

``` bash
git add ejercicio1/pedido.xml
git commit -m "Resolver ejercicio 1: documento XML de pedido"
```

## 4. Ejercicio 2 --- DTD externo para `nota`

Cree `ejercicio2/nota.xml` y transcriba el XML proporcionado en el
ejercicio.

Analice:

``` text
nota
├── para
├── de
├── titulo
└── contenido
```

Responda:

1.  ¿Cuál es el elemento raíz? nota
2.  ¿Cuántas veces aparece `para`? 1 vez
3.  ¿El orden de los elementos es significativo? si los elementos deben aparecer estrictamente en el orden especificado
4.  ¿Los elementos contienen otros elementos o solamente texto? el elemento raiz nota contiene otros elementos, los elementos hijos (para,de,titulo,contenido) contienen solamente texto(#pcdata)

Cree `ejercicio2/nota.dtd`. Defina primero:

``` dtd
<!ELEMENT nota (...)>
```

y después los elementos que contienen texto mediante `#PCDATA`.

| Elemento    | Contenido esperado | Declaración DTD |
|-------------|--------------------|-----------------|
| `nota`      | elementos          |  <!ELEMENT nota (para, de, titulo, contenido)>       
 | `para`      | texto              |           <!ELEMENT para (#PCDATA)>      |       
 | `de`        | texto              |           <!ELEMENT de (#PCDATA)>      |        
 | `titulo`    | texto              |         <!ELEMENT titulo (#PCDATA)>        |       
| `contenido` | texto              |           <!ELEMENT contenido (#PCDATA)>      |      

Asocie el DTD mediante:

``` xml
<!DOCTYPE nota SYSTEM "nota.dtd">
```

Compruebe la validación y registre:

``` bash
git add ejercicio2/
git commit -m "Agregar validación externa DTD para nota"
```

## 5. Pruebas negativas

Introduzca temporalmente estos cambios en `nota.xml`:

1.  sustituir `<para>` por `<destinatario>`;
2.  intercambiar el orden de `<para>` y `<de>`;
3.  agregar `<telefono>5551234567</telefono>`.

Registre:

| Modificación       | ¿Bien formado? | ¿Válido? | ¿Por qué? |
 |--------------------|----------------|----------|-----------|
 | Cambiar `para`     | si             | no       |cumple con la sintaxis xml pero el dtd espera la etiqueta para y no reconoce destinatario.
| Cambiar orden      | si             | no       | las etiquetas abren y cierran bien pero el dtd exige el orden exacto (para,de,titulo,contenido)
| Agregar `telefono` | si             | no       |es correcto, pero el dtd no tiene declarado el elemento telefono dentro de nota.

Observe los cambios:

``` bash
git diff
```

Restaure el documento válido:

``` bash
git restore ejercicio2/nota.xml
```

## 6. DTD interno de `nota`

Cree:

``` text
ejercicio2/nota-interno.xml
```

Sustituya la referencia externa por:

``` xml
<!DOCTYPE nota [
    ...
]>
```

Complete la comparación:

| Característica                      | DTD interno            | DTD externo                         |
|-------------------------------------|------------------------|-------------------------------------|
| Ubicación                           | dentro del archivo xml | en un archivo independiente (.dtd)1 |                                    
| Reutilizable entre XML              | no                     | si                                  |            
| Archivo adicional                   | no                     | si                                  |                  
| Conveniente para un único documento | si                     | no                                  |   
| Conveniente para muchos documentos  | no                      | si                                  |   

Registre:

``` bash
git add ejercicio2/
git commit -m "Agregar versión con DTD interno para nota"
```

## 7. Ejercicio 3 --- Matrícula

Cree `ejercicio3/matricula.xml` y analice:

``` text
matricula
├── personal
│   ├── dni
│   ├── nombre
│   ├── titulacion
│   ├── curso_academico
│   └── domicilios
│       └── domicilio+
└── pago
    └── tipo_matricula
```

Identifique elementos simples, compuestos, repetibles, atributos y
restricciones.

### Cardinalidad

El requisito establece que debe existir **al menos un domicilio**.

| Símbolo|  Significado|
 |---------|------------|
 |`?` |     cero o uno|
| `*`  |    cero o más|
 |`+`   |   uno o más|

Determine qué operador corresponde a "al menos uno" e incorpórelo al
DTD.

Elimine temporalmente todos los domicilios y registre:

``` text
¿XML bien formado? __________
¿XML válido? _________________
¿Por qué? ____________________
```

## 8. Restricción del atributo `tipo`

El XML utiliza:

``` xml
<domicilio tipo="familiar">
<domicilio tipo="habitual">
```

El atributo `tipo` debe ser obligatorio y solo admitir `familiar` o
`habitual`.

Utilice:

``` dtd
<!ATTLIST ...>
```

y determine cómo expresar la enumeración.

Pruebe:

 | Caso              | Predicción | Resultado | Explicación                                            |
|-------------------|------------|-----------|--------------------------------------------------------|
 | `tipo="familiar"` | valido     | valido    | es uno de los valores permitidos en la enumeracion dtd |
 | `tipo="habitual"` | valido     | valido    | es uno de los valores permitidos en la enumeracion dtd |
| `tipo="temporal"` | invalido   | invalido  | tempral no esta definido en la lista permitida         |
| sin `tipo`        | invalido   | invalido  | el atributo es #REQUIRED por lo que no se puede omitir

## 9. Git para desarrollar una variante

Cree una rama:

``` bash
git switch -c dtd-interno-matricula
git branch
```

En ella cree `ejercicio3/matricula-interno.xml` e implemente el DTD
interno.

``` bash
git add ejercicio3/matricula-interno.xml
git commit -m "Implementar DTD interno para matrícula"
git log --oneline --graph --all
```

Regrese a la rama principal e integre:

``` bash
git switch main
git merge dtd-interno-matricula
git log --oneline --graph --all
```

Si su rama principal se denomina `master`, utilice ese nombre.

## 10. Publicar el repositorio

Cree en GitHub un repositorio denominado:

``` text
xml-dtd-practica
```

Asocie el repositorio local con el remoto y realice `push` de la rama
principal. Compruebe que aparezcan los tres ejercicios y el historial de
commits.

## 11. README final

Documente:

``` markdown
# Práctica XML y DTD

## Objetivo
Aprender a estructurar y validar XMLs con DTD (internos y externos), manejando reglas, atributos y el historial con Git.

## Ejercicio 1: Pedido
### Modelo propuesto
Estructura sencilla para un pedido:

- <pedido> (raíz con id)
  - <destinatario>
  - <direccion>
    - <calle>
    - <numero>
    - <piso>
    - <letra>
  - <articulo> (con atributo codigo)
  - <fecha_entrega>

### Decisiones de diseño
- Dirección dividida: Se separaron calle, número, piso y letra para poder filtrar o buscar datos específicos sin rodeos.
- Formato de fecha: Usé ISO 8601 (YYYY-MM-DD) para evitar problemas al ordenar datos.
- Atributos: Se usaron solo para IDs y códigos; lo demás va como elemento.

## Ejercicio 2: Nota
### DTD externo
Se definieron las reglas en nota.dtd y se vinculó con <!DOCTYPE nota SYSTEM "nota.dtd">.

### DTD interno
Se probó meter las reglas dentro del mismo XML (nota-interno.xml) usando <!DOCTYPE nota [ ... ]>.

### Pruebas realizadas
- Cambiar <para> por <destinatario>: Bien formado, pero no válido (el DTD espera <para>).
- Cambiar el orden: Bien formado, pero inválido al romper la secuencia (para, de, titulo, contenido).
- Agregar <telefono>: Bien formado, pero inválido porque <telefono> no existe en el DTD.

## Ejercicio 3: Matrícula
### Modelo
Estructura para matrículas:
- Raíz: <matricula>
- Compuestos: <personal>, <domicilios>, <pago>
- Simples: <dni>, <nombre>, <titulacion>, <curso_academico>, <domicilio>, <tipo_matricula>

### Cardinalidad
Se usó + en <!ELEMENT domicilios (domicilio+)> para obligar a que exista al menos un domicilio. Sin domicilios, el XML está bien formado pero falla en la validación.

### Restricción del atributo tipo
Con <!ATTLIST domicilio tipo (familiar | habitual) #REQUIRED>, el atributo tipo pasa a ser obligatorio y solo acepta familiar o habitual.

### DTD externo
Definido en matricula.dtd y enlazado desde matricula.xml.

### DTD interno
Se armó en la rama dtd-interno-matricula (matricula-interno.xml) y luego se integró a main.

### Pruebas realizadas
- tipo="familiar" / tipo="habitual": Válidos.
- tipo="temporal": Inválido (valor fuera de la lista).
- Sin tipo: Inválido (es obligatorio).

## Conclusiones
- Bien formado vs. Válido: Estar bien formado es solo cumplir la sintaxis; ser válido es pasar las reglas del DTD.
- DTD Interno vs. Externo: El interno sirve para casos puntuales; el externo para reutilizar reglas en varios archivos.
- Git: Las ramas permiten experimentar con variantes sin arriesgar la versión principal.
```

Responda:

1.  ¿Cuál es la diferencia entre XML bien formado y XML válido? bien formado es cumplir la sintaxis basica de etiquetas y valido es ademas seguir la estructura y reglas del dtd
2.  ¿Qué función cumple un DTD? definir las reglas los elementos permitidos y atributos para que la estructura del xml sea consistente
3.  ¿Qué diferencia existe entre DTD interno y externo? el interno va metido en el mismo xml con doctype y el externo en un archivo dtd aparte para reusarlo en varios xmls
4.  ¿Cómo se expresa cardinalidad en DTD? con los operadores signo de interrogacion para 0 o 1 asterisco para 0 o mas y mas para 1 o mas
5.  ¿Cómo puede restringirse un atributo a determinados valores? metiendo una lista de opciones entre parentesis con attlist separadas por barras como familiar o habitual y poniendo required si es obligatorio
6.  ¿Qué ventaja proporcionó Git durante las pruebas? llevar historial de cambios probar cosas sin perder el codigo que ya servia y tener todo registrado paso a paso
7.  ¿Qué utilidad tuvieron `git diff` y `git restore`? git diff para ver que le moviste exactamente al codigo y git restore para borrar las pruebas feas y regresar al archivo bueno
8.  ¿Qué ventaja proporcionó una rama para desarrollar una solución
    alternativa? probar la variante en un entorno aislado sin arruinar la rama main y luego fusionarla cuando ya quedo

## 12. Estructura final esperada

``` text
xml-dtd-practica/
├── README.md
├── ejercicio1/
│   └── pedido.xml
├── ejercicio2/
│   ├── nota.xml
│   ├── nota.dtd
│   └── nota-interno.xml
└── ejercicio3/
    ├── matricula.xml
    ├── matricula.dtd
    └── matricula-interno.xml
```

Cada commit debe representar una unidad lógica de trabajo.

## 13. Entregables

1.  URL del repositorio de GitHub.
2.  `pedido.xml`.
3.  `nota.xml`.
4.  `nota.dtd`.
5.  `nota-interno.xml`.
6.  `matricula.xml`.
7.  `matricula.dtd`.
8.  `matricula-interno.xml`.
9.  `README.md` con análisis, pruebas y conclusiones.

El historial Git forma parte de la evidencia del proceso de
construcción.
