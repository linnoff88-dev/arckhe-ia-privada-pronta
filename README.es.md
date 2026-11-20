# IA privada lista

[![IA privada lista](guia/assets/banner-es.jpg)](https://inematds.github.io/ia-privada-pronta/guia/es/)

**🇧🇷 [Português](../../README.md) · 🇺🇸 [English](../../README.en.md) · 🇪🇸 [Español](README.es.md)**

[🎬 Video: conversa con tu IA sin internet (audio en portugués).](https://www.youtube.com/watch?v=TJ9omYZdIrE)

Guía completa: https://inematds.github.io/ia-privada-pronta/guia/es/

**Validación real en Linux:** se confirmó la ejecución sin red externa, la conversación con contexto y el cierre de los procesos propios. Pasaron las 14 pruebas de software. El modelo ligero se equivocó en el ejercicio de cálculo del documento; el resultado está registrado, sin cambiar la respuesta correcta. Consulta [los resultados completos](../../verificacao/STATUS.md).

## Qué es

Este kit gratuito pone una IA a conversar contigo en tu propio ordenador. Está pensado para quienes quieren procesamiento local, sin que una empresa de IA reciba cada solicitud. Incluye preparación del modelo, conversación, lectura de archivos y un modo Linux con la red bloqueada para el proceso. Necesitas Python, Ollama y memoria suficiente para el modelo. Las pruebas reales y las limitaciones están en esta página.

Este kit pone una IA a conversar contigo en tu propio ordenador, sin proveedores externos de inferencia. El objetivo es mantener el procesamiento local: servicio propio en una dirección local, nube desactivada y, en Linux, un modo que elimina el acceso de red del proceso. El modelo predeterminado es Llama 3.2 3B; mantiene las restricciones propias del modelo.

**Terminas conversando con un modelo real.** El kit incluye el lanzador, el perfil del modelo, la preparación de los pesos, conversación con contexto, preguntas por archivo, un modo Linux sin red y una verificación ejecutable. No necesitas clave, suscripción a un modelo ni bibliotecas Python adicionales.

## Requisitos

- Git para clonar, o [descarga el ZIP](https://github.com/inematds/ia-privada-pronta/archive/refs/heads/main.zip) y extrae la carpeta.
- Python 3.10 o superior: [instalador oficial](https://www.python.org/downloads/).
- [Ollama instalado](https://ollama.com/download), en Linux, macOS o Windows.
- Para el modelo predeterminado: aproximadamente **2 GB de descarga**, se recomiendan **8 GB de RAM**, espacio libre para los pesos y recursos para el sistema. El contexto predeterminado es de 2048; el consumo real depende del entorno de ejecución. La GPU es opcional: la CPU funciona, con un rendimiento distinto.
- En Linux, para el modo sin red: `bubblewrap` e `iproute2`. El modo requiere que el sistema permita espacios de nombres de usuario.

Comprueba antes de empezar:

```bash
python3 --version
ollama --version
```

En Windows, usa `py` en lugar de `python3` si hace falta. Después de instalar Python u Ollama, abre una terminal nueva. El kit no instala paquetes con privilegios ni modifica el Ollama que usan otros proyectos.

## Instalar y conversar

```bash
git clone https://github.com/inematds/ia-privada-pronta.git
cd ia-privada-pronta
python3 ia.py diagnostico
python3 ia.py preparar
python3 ia.py verificar
python3 ia.py conversar
```

`preparar` usa internet para descargar o comprobar los pesos del modelo. Después, `verificar` y `conversar` usan el modelo local ya descargado, sin descargar otro automáticamente. Ollama almacena los pesos, no el repositorio.

En la conversación, escribe `/limpar` para reiniciar el contexto y `/sair` para cerrar. El kit conserva en memoria solo algunos mensajes recientes y no guarda el historial. Al salir, descarga el modelo de memoria y cierra únicamente su instancia y sus procesos secundarios. La terminal o el sistema operativo aún pueden registrar lo que aparece en pantalla.

## Linux: conversación con la red del proceso bloqueada

Después de preparar los pesos, instala los dos recursos del sistema si faltan:

```bash
sudo apt install bubblewrap iproute2
bash sem-rede.sh verificar
bash sem-rede.sh conversar
```

El lanzador crea una red separada con solo la interfaz local `lo`. El modelo y el cliente siguen comunicándose dentro de ella; no hay una interfaz para salir a internet. El ordenador sigue conectado normalmente para los demás programas. Este modo aísla la red, no los archivos del usuario; no es una máquina virtual.

En macOS/Windows, la conversación local funciona mediante `ia.py`; el lanzador sin red es específico de Linux. Para comprobarlo manualmente en esos sistemas, prepara primero, desconecta el Wi-Fi/cable y ejecuta `verificar`. No afirmamos haber realizado esta prueba en sistemas que no hemos usado.

## Pregunta única y archivo

```bash
python3 ia.py perguntar "Escreva três ideias para organizar uma reunião curta."
python3 ia.py perguntar --arquivo exemplos/primeiro-pedido.txt
```

Para texto sensible, prefiere `--arquivo` o la entrada estándar: los argumentos escritos en la línea de comandos pueden quedar en el historial del shell. Las respuestas son texto; el kit no ejecuta los comandos sugeridos por la IA.

## Cómo saber si funcionó

```bash
python3 ia.py verificar
```

La verificación envía una pregunta real al modelo (`17 + 25`) y espera `42`. Guarda el resultado en `.estado/verificacao.json`, con el digest de los pesos, los tokens generados y las interfaces de red. El código de salida 0 significa que ese caso pasó; 1 indica una respuesta distinta; 2 indica un error de preparación o ejecución. Una respuesta correcta no demuestra competencia general ni ausencia de negativas.

Lee también [verificacao/STATUS.md](../../verificacao/STATUS.md): distingue las pruebas de software de la inferencia realmente ejecutada. La respuesta correcta de un segundo ejercicio está en `exemplos/documento-ficticio.txt`.

## Qué significa cada elección

**Privacidad:** quién recibe los datos y dónde se ejecuta la tarea. En este kit, la solicitud solo llega a la instancia propia de Ollama en `127.0.0.1`. La nube y las herramientas externas están desactivadas. El modo Linux sin red añade una restricción del sistema operativo.

**Sin censura:** modelo publicado por quien lo mantiene con menos negativas. Este término no promete aceptar todas las solicitudes, responder siempre correctamente ni sustituir la revisión humana. El kit no añade filtros por temas ni modifica el modelo automáticamente.

## Modelo, versión y memoria

El perfil usa **`llama3.2:latest`**, comprobado el 15/11/2026. `perfil.json` registra el digest esperado de los pesos para impedir un cambio silencioso del modelo. Si quien lo mantiene cambia la etiqueta y el digest deja de coincidir, la ejecución se detiene: comprueba la nueva versión antes de actualizar `digest_esperado`. No elimines la comprobación solo para continuar.

El perfil limita el contexto a 2048 y la respuesta a 512 tokens. La conversación tiene una ventana breve; los documentos largos deben dividirse. El tiempo de la primera respuesta incluye la carga de los pesos. Si falta memoria, usa una máquina con capacidad adecuada o elige conscientemente otro modelo local, ajustando el nombre y el digest. No hay alternativa en la nube.

## Comportamientos importantes

- Instancia propia, puerto exclusivo y nube desactivada. Si el puerto está ocupado, aparece un error; nunca nos conectamos silenciosamente a otro servicio.
- Los proxies heredados no reciben las solicitudes y se rechazan las redirecciones HTTP.
- Un nombre de modelo remoto, pesos ausentes, un digest distinto o una respuesta vacía detienen la ejecución.
- No hay conectores, búsqueda web, agentes que ejecuten herramientas ni publicación automática.
- La privacidad de este flujo no protege contra malware, administradores del ordenador, capturas de pantalla, copias de seguridad ni un entorno de ejecución comprometido.

## Pruebas de software

```bash
python3 -m unittest discover -s tests -v
```

Estas pruebas usan un entorno de ejecución simulado y no cuentan como prueba del modelo. La prueba con IA real es `verificar`.

## Documentación de las herramientas

[Ollama: ejecución local y nube](https://docs.ollama.com/faq) · [API local de conversación](https://docs.ollama.com/api/chat) · [Modelo predeterminado](https://ollama.com/library/llama3.2).

Código del kit: MIT. El entorno de ejecución y los pesos tienen sus propias licencias; aquí no se redistribuyen los pesos.

## Si descargaste el ZIP

Extrae la carpeta. En Windows, abre la carpeta que contiene ia.py, haz clic en la barra de direcciones, escribe powershell y presiona Enter. Ejecuta los comandos de Python de la guía, usando py si es necesario. Los accesos directos preparar.cmd y conversar.cmd también funcionan con doble clic. En Linux/macOS, abre Terminal y usa cd para entrar en la carpeta extraída; la ruta depende de dónde guardaste el ZIP. No ejecutes git clone si elegiste el ZIP.

Runtime es el programa que ejecuta el modelo; digest es la identificación exacta de los pesos; un espacio de nombres de red es una red separada para el proceso en Linux. Si cambia el digest, el error muestra los valores esperado y encontrado: confirma la versión en la página oficial de la etiqueta antes de decidir actualizar digest_esperado en perfil.json. Sin confirmación, conserva la protección.
