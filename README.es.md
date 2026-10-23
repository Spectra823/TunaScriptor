# TunaScriptor

# Readme en distintos idiomas
[![en](https://img.shields.io/badge/readme-en-blue?style=flat)](https://github.com/Spectra823/TunaScriptor/blob/main/README.md)
[![es](https://img.shields.io/badge/readme-es-yellow?style=flat)](https://github.com/Spectra823/TunaScriptor/blob/main/README.es.md)
[![fr](https://img.shields.io/badge/readme-fr-red?style=flat)](https://github.com/Spectra823/TunaScriptor/blob/main/README.fr.md)

**De mp3 a *cifrado*, editor de cifrados y más. Una herramienta específica para *tunas*.**

¡Hola, soy Cáscara! Novata de la Tuna Femenina de Salamanca. Este proyecto no está relacionado de ninguna forma con mi tuna (si algo sale mal, no tienen culpa alguna), pero esta experiencia es la que me
ha impulsado a la creación de este programa. TunaScriptor (es un nombre provisional, el nombre final está todavía por determinar pero seguramente sea *Aladín*) es un software de Windows
creado específicamente para su uso por parte de tunas. Permite importar canciones, generando un análisis y un cifrado aproximado tanto para bandurria
y laúd como para guitarra. Además, permite la creación desde cero de cifrados utilizando la nomenclatura habitual de número de la tuna, y su edición de forma cómoda
para poderse compartir posteriormente en PDF o archivos editables.

> 🚧 **Está todavía en proceso.** Por ello todavía no está disponible para descarga, y no lo estará hasta que no haya solventado y pulido
> al menos sus funcionalidades básicas. Vea [Desarrollo actual](#desarrollo-actual) para más información.

<p align="center">
  <img src="./mainwindowfoto.PNG" width="85%" alt="Analysis Main Window"/>
</p>

## Índice
- [Razones de desarrollo](#razones)
- [Desarrollo actual](#desarrollo-actual)
- [Funcionamiento](#funcionamiento)
- [Características actuales](#características-actuales)
- [Estructura del programa](#estructura-programa)
- [Licencia](#licencia)

## Razones de desarrollo
Dado que no se requiere una educación formal en música para participar en una tuna, muchos nos encontramos con dificultades a la hora de tener que interpretar
cifrados tradicionales o crear cifrados propios desde cero de canciones que nos gusten. A pesar de que el aprender a "sacar" una canción de oido es una habilidad
crucial para todo tuno, y también algo muy entretenido una vez se le coge el tranquillo, esto resulta muy complejo al principio sobre todo para aquellos que carecemos de esta formación
al inicio.

Por ello, para ayudar a mis compañeros y tratar de simplificar un poco este proceso, sobre todo para novatos que estén empezando sus andanzas en el mundo tunil
y no tengan mucha idea, decidí crear este programa. Al principio iba a ser un pequeño script de Python para ayudarme en mis comienzos a transcribir canciones,
pero con el desarrollo de herramientas de análisis musical más precisas estos últimos años, creí conveniente ampliar los objetivos y crear algo más complejo
y util.

De nuevo, recalcar que esta herramienta no busca sustituir el sacar canciones de oído, pues creo que es una habilidad que todos deberíamos tratar de aprender y pulir
cuanto podamos, pero creo que puede ser tanto una muleta útil en los comienzos como una ayuda en momentos en que dispongamos de poco tiempo para sacar un cifrado.
Además, TunaScriptor puede utilizarse como un simple editor de cifrados para aquellos que prefieran escribirlos desde cero, pero requieran un programa más cómodo que las alternativas
actuales, que permita el uso de nuestros sistemas de numeración para bandurrias y laudes.

## Desarrollo actual
 **De momento, todo puede cambiar.** Tanto la arquitectura básica como los algoritmos de análisis, tipos de archivo utilizados, e interfaz están todavía
 en proceso, por ello prefiero no ofrecer todavía descargas hasta que no esté contenta con una versión.
- **Resultados varían de canción a canción.** Aquellas canciones acapella o solo con un instrumento son las que ofrecen mejores resultados
  por el momento. Cuando se introducen muchos intrumentos solapados o armonías, pueden empeorar los análisis. El objetivo es mejorar
  todo lo posible hasta poder analizar convincentemente todo tipo de grabación.
- **Áreas en que más estoy trabajando** sobre todo, estoy tratando de mejorar el análisis de las canciones, centrándome en la claridad
  y seguimiento de la melodía principal en caso de bandurria y laúd, y en el correcto seguimiento y cambio de acordes en guitarra.
- **No está preparado para el público general todavía.** El programa funciona en mi ordenador, pero todavía no lo he preparado para
  ser llevado a otros equipos. No publicaré un instalador hasta que no esté conforme con el programa. Si aún así deseáis descargar la versión que tenga
  para probarlo, podéis escribirme.
- **Instalación sencilla** estoy tratando de simplificar todo lo posible la intalación y descarga de las distintas herramientas que soportan TunaScriptor, para
  que toda persona pueda utilizar el programa sin problemas, sin necesidad de crear por su cuenta entornos con versiones específicas ni meter comandos en
  el terminal etc. Algunas barreras son infranqueables, sobre todo los requerimientos legales debido a las distintas licencias de las herramientas de análisis,
  los samples utilizados para el sonido de guitarra etc. Pero trato de acortar y simplificar esos procesos todo lo posible, incluyendo guías claras en el propio
  programa de cómo instalar todo paso a paso.

## Funcionamiento
Al principio, deberás seguir la guía de instalación, en la que verá que herramientas se están descargando, y podrá elegir aquellas que desee. Dberá también aceptar
algunas condiciones de licencias de estos modelos, pasos que no se pudieron automatizar o simplificar más por razones legales.
  
Hay distintas formas de comenzar un cifrado en TunaScriptor: Puedes importar un mp3 para analizar, escribir uno desde cero en una plantilla, o grabar desde tu micrófono para obtener una aproxiamción.
Todos los archivos importados se guardan en la "Biblioteca de música". En ella encontrarás todas tus canciones con
la imagen apropiada si tenían alguna, así como información adicional: número de análisis y cifrados de la canción, duración y fecha de importación.
  
A su vez, se muestra abajo una lista de todos los cifrados existentes, detallando su tipo, fecha de creación etc. Al abrir una canción, se muestran al principio de esta lista aquellos cifrados relacionados con ella.
Una vez ya tienes la canción que deseas importada, se abrirá la interfaz de análisis, en ella puedes escoger el tipo de análisis que quieres realizar: para guitarra o bandurria/laúd y los modelos
a usar en este (existen distintos modelos disponibles, algunos más pesados y precisos, pero requieren ordenadores potentes y son más lentos. Otros más ligeros, para ordenadores que
no puedan utilizar mejores modelos).
  
Verás una barra de progreso a medida que avanza el análisis. Si escogiste el cifrado de guitarra, podrás disfrutar directamente de él. Si escogiste laúd/bandurria, podrás observar
el análisis realizado, así como las pistas de los distintos instrumentos de la canción, las notas recogidas etc. Para crear el cifrado, hay un botón de "Crear cifrado". En esta interfaz podrás escoger en base a qué pista
se crea. La más recomendada es aquella que sigue la melodía principal (la voz del cantante en caso de que lo haya, o el instrumento dominante en partes únicamente instrumentales). Esta pista es la más
testeada por mi para realizar cifrados, pero puedes escoger a tu gusto y editar cada pista como desees antes de crear los cifrados.
  
Una vez creado el cifrado, este buscará las partes repetidas para ser lo más compacto y legible posible. Aún así, puede editarse añadiendo, quitando o cambiando notas, añadiendo o borrando secciones
o incluso cambiando la tonalidad de secciones (o de la canción entera si así se desea. Esto fue implementado con el objetivo de simplificar la creación de cifrados de distintas voces
de una misma canción). También puede añadirse letra sobre cada sección del cifrado (en los cifrados de guitarra, esta aparece de forma automática).
Antes de exportar puedes escuchar el cifrado para ver si te convence. Este playback utiliza samples de instrumentos reales (véase [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
Por último, los cifrados pueden exportarse tanto en PDF como en archivos editables (uno propio de TunaScriptor, y el otro .musicxml, usado en otros programas como MuseScore o GuitarPro).

## Características actuales

Leyenda: ✅ funciona y se realizaron tests automáticos · 🧪 funciona, pero los resultados no son
del todo fiables o no se ha probado con hardware real todavía · 🔧 en proceso

### Análisis
| Funcionalidad | Estado | Notas |
|---|---|---|
| Transcripción de notas de una grabación, una pista por instrumento | ✅ 🧪 | Utiliza el modelo MuScriptor; la calidad depende de la grabación y del tamaño del modelo |
| Melodía principal (★): una sola línea para toda la canción, la voz cuando alguien canta y el instrumento principal en los demás pasajes | 🔧 | Los pasajes cantados se siguen con bastante fiabilidad. En los pasajes instrumentales todavía aparecen notas espurias o faltan algunas |
| Melodía vocal con separación de voz opcional | ✅ 🧪 | El vibrato se mantiene como una sola nota; las sílabas repetidas se conservan como notas separadas |
| Reconocimiento de acordes (Chordify) y de letras (Whisper) | ✅ 🧪 | La precisión de los acordes se está validando con evidencias independientes; el reconocimiento de las letras depende del modelo |
| Comparación de los acordes revisados por TunaScriptor con los originales de Chordify | ✅ | Script de desarrollo; las discrepancias se evalúan contrastándolas con las notas y el audio |
| Secciones repetidas (estrofa, estribillo, variaciones, BIS) | 🔧 | Funciona con muchas canciones, pero algunas todavía se dividen en demasiados fragmentos |

### Cifrados y edición
| Funcionalidad | Estado | Notas |
|---|---|---|
| Cifrados de bandurria y laúd a partir de cualquier parte transcrita | ✅ | Reglas de notación de tuna (trémolo, espaciado, repeticiones y siempre seis líneas), digitación al estilo de tuna y notación del laúd igual que la de la bandurria |
| Cifrados de guitarra: letras con acordes colocados sobre las sílabas | ✅ 🧪 | Nombres de acordes en español (LAm, MI7…); la colocación sigue la temporización de las palabras y los acordes |
| Nuevo cifrado cantando al micrófono (guitarra, bandurria y laúd) | 🧪 | Añadido recientemente; probado con grabaciones sintéticas, pero todavía no con un micrófono real |
| Transposición (por semitonos, a una tonalidad o a una tesitura vocal) con previsualización audible | ✅ | Se vuelven a elegir las cuerdas y los trastes; las notas fuera de rango se señalan, nunca se desplazan silenciosamente |
| Edición de notas en piano roll, editores de cifrados y deshacer/rehacer | ✅ | Las notas y palabras dudosas se resaltan para su revisión |
| Voces, solistas, colores y leyenda en los cifrados de guitarra | ✅ | |

### Reproducción, guardado y exportación
| Funcionalidad | Estado | Notas |
|---|---|---|
| Reproducción de la grabación, las notas o un fragmento seleccionado, con la partitura resaltada durante la reproducción | ✅ 🧪 | Las previsualizaciones utilizan sonidos sintetizados sencillos |
| Acompañamiento de guitarra generado a partir de un cifrado de guitarra, con la voz separada como opción | 🔧 | La sincronización está garantizada por construcción; el sonido todavía es básico |
| Biblioteca musical, análisis guardados, cifrados independientes, carpetas, guardado automático y recuperación tras fallos | ✅ | Los análisis originales nunca se sobrescriben; se conservan los resultados anteriores de la melodía |
| Exportación a PDF (con imagen del escudo y autor) | ✅ | |
| Exportación e importación editables: formato nativo, tablatura MusicXML, ChordPro y MIDI | ✅ | MusicXML todavía no se ha comprobado en MuseScore |

## Estructura del programa
El programa se divide en dos tipos de proceso, para que los modelos de análisis musical no impidan o entorpezcan
la edición de cifrados.

```
┌──────────── Application (PySide6 / Qt) ─────────────┐
│ ui/        windows, piano roll, cifrado editors      │
│ cifrado/   cifrado models, fingering, chords,        │
│            repeats & structure, transposition        │
│ music/     notes, tracks, melody results, MIDI       │
│ playback/  audio player, synthesis, recording        │
│ library/   SQLite library, autosave, file storage    │
│ export/    PDF, MusicXML / ChordPro interchange      │
│ engines/   starts and supervises the engine workers  │
└──────────────┬───────────────────────────────────────┘
               │ JSON request on stdin → JSON lines on stdout
┌──────────────▼──────── Engine workers (separate Python environment) ──┐
│ muscriptor_worker.py  note transcription (MuScriptor, PyTorch)        │
│ chordify_worker.py    chords + lyrics (Chordify, Whisper)             │
│ melody_worker.py      main / vocal melody (librosa; optional voice    │
│                       separation with Mel-Band RoFormer)              │
└───────────────────────────────────────────────────────────────────────┘
```

- **Transcripción** (bandurria y laúd): MuScriptor transcribe la grabación distinguiendo los distintos
  instrumentos presentes, creando una pista para cada uno. Luego, busca la melodía principal (aquella que sigue a la voz y los instrumentos más
  prominentes si no hay voz) y la separa del resto, en otra pista. Cada pista puede convertirse en cifrado, pudiendo
  editarse las pistas de forma simple, combinarlas etc. A la hora de crear el cifrado, se prioriza la precisión y
  comodidad (por ejemplo, siempre que encuentre una nota sexta o superior en cuerdas graves, escogerá bajar a la
  correspondiente en la siguiente cuerda cuando sea posible).
- **Guitarra**: Chordify detecta los acordes y Whisper la letra. TunaScriptor después busca repeticiones y ordena
  los acordes en el lugar adecuado respecto a la letra.
- **Estructura**: las partes repetidas se buscan comparando la melodía a lo largo de la canción, los intrumentos y la letra. Esto
  se hace para evitar, por ejemplo, repetir 7 veces el cifrado de estrofa en Guantanamera cuando se puede resumir todo en 1 misma estrofa
  con distinta letra. 
- **Almacenamiento**: todo se almacena localmente en el ordenador del usuario, sin necesitar contacto alguno con programas
  externos. Esto es para mantener tanto la seguridad como la privacidad del usuario (y además, de momentop no hay presupuesto para
  un servidor). Utliza una biblioteca SQLite local, los resultados de los análisis se guardan como JSON inmutables al igual que los cifrados, que se pueden
  editar, copiar, compartir etc.

Más detalles en: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

**Principales herramientas:** Python, PySide6 (Qt 6), NumPy, SQLite, ReportLab (PDF), mido (MIDI). En el
análisis: PyTorch, MuScriptor, Chordify, Whisper y librosa.

## Licencia
El código de esta aplicación es publicado bajo la licencia [MIT License](LICENSE).

Los modelos y herramients de análisis son software externos con sus propias licencias y condiciones,
y no están incluidos en el código. En particular, los pesos de los modelos de MuScriptor están publicados
bajo la licencia **CC BY-NC 4.0** con condiciones de acceso restringidas. Véase [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
