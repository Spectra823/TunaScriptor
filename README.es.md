# Readme en distintos idiomas
[![en](https://img.shields.io/badge/readme-en-blue?style=flat)](https://github.com/Spectra823/MelAdvisor/blob/main/README.md)
[![es](https://img.shields.io/badge/readme-es-yellow?style=flat)](https://github.com/Spectra823/MelAdvisor/blob/main/README.es.md)
[![fr](https://img.shields.io/badge/readme-fr-red?style=flat)](https://github.com/Spectra823/MelAdvisor/blob/main/README.fr.md)

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
- [Estructura del programa](#estructura-programa)
- [Plan de desarrollo](#plan-desarrollo)
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
 **De momento, todo puede cambiar.** Tanto la arquitectura básica como los algoritmos de análisis, tipos de archivo utilizados, e interfaz están taodavía
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

## Plan de desarrollo

## Licencia
El código de esta aplicación es publicado bajo la licencia [MIT License](LICENSE).

Los modelos y herramients de análisis son software externos con sus propias licencias y condiciones,
y no están incluidos en el código. En particular, los pesos de los modelos de MuScriptor están publicados
bajo la licencia **CC BY-NC 4.0** con condiciones de acceso restringidas. Véase [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
