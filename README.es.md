# awk Cheatsheet · Hoja de referencia de comandos awk

> 🌍 [中文](README.md) · [Português](README.pt.md)

Una **hoja de referencia de una sola página** con 30 usos reales de `awk`: la forma `patrón { acción }` de la que nace todo programa awk; campos, registros y los dos contadores de línea; selección de líneas con regex, rangos y comparaciones; las variables y funciones de cadena que hacen el trabajo pesado; y las recetas de una línea que la gente guarda de verdad en su historial (sumar, promediar, deduplicar, agrupar, unir dos archivos). Cada entrada responde solo tres preguntas: **qué hace, un comando listo para copiar y pegar, y etiquetas de uso**. Sin sintaxis inventada: las 30 entradas se **ejecutaron de verdad**, una por una, contra archivos de prueba en mawk 1.3.4, y la semántica se verificó contra la Guía de Usuario de GNU Awk. Las diferencias entre gawk y mawk/BusyBox están señaladas al final de la hoja: algunas fallan en silencio.

## Para quién es

Ingenieros de operaciones (SRE/DevOps), desarrolladores backend y cualquiera que tenga el log delante pero no pueda sacar la suma por grupo, o que para cruzar dos archivos por una clave termine escribiendo un script. Si `grep` solo filtra líneas y `sed` solo sustituye, y lo que necesitas es **entender columnas, hacer aritmética y llevar cuentas entre líneas**, esta hoja es para ti.

## Cómo usarla

1. Ve directo a **[CHEATSHEET.md](CHEATSHEET.md)**: el cuerpo de la referencia; los comandos en inglés los entiende cualquier desarrollador.
2. Identifica primero tu awk: `awk --version` dice gawk o mawk en la primera línea; macOS trae el awk de BSD. Las 30 entradas están verificadas en mawk 1.3.4 y las notas de implementación del final son lectura obligada.
3. Sigue el orden de la hoja —conceptos básicos → patrones → variables y funciones → recetas— y prueba cada comando a mano sobre archivos de práctica. En una hora los dominas.

## Pruébalo en 30 segundos

```bash
awk --version | head -1                # confirma la implementación: gawk / mawk / BSD
printf 'alice math 88\nbob math 72\n' > /tmp/scores.txt
awk '{s += $3} END {print s}' /tmp/scores.txt   # 160: la suma de una columna, el gesto clásico de awk
```

## La combinación de oro, lista para usar

```bash
awk 'NR == FNR {tier[$1] = $2; next} {print $1, $2, tier[$1]}' codes.txt events.txt
```

La unión de dos archivos: el primero se carga en memoria como tabla de consulta (en la fase `NR == FNR` solo se guarda), el segundo se recorre línea a línea añadiendo el valor encontrado. Sin esto necesitarías un script o una base de datos; con esto, una línea.

## Qué incluye

| Categoría | Contenido |
|---|---|
| 🧱 Conceptos básicos | print y $0 la línea completa / campos $1, $NF y NF / los contadores NR y FNR / BEGIN y END / comportamiento por defecto de patrón y acción / variables propias y -v (6) |
| 🎯 Patrones | /regex/ sobre toda la línea / rango /inicio/,/fin/ / la trampa de tipos en comparaciones / ~ y !~ por campo / &&, ||, ! / FNR>1 para saltar la cabecera de cada archivo (6) |
| 🧰 Variables y funciones | -F y FS / reconstrucción de $0 y OFS al asignar un campo / length / substr / split / sub y gsub / printf y sprintf / match con RSTART y RLENGTH / toupper e index (9) |
| 🍳 Recetas | suma de columna / media con guarda contra archivo vacío / deduplicar conservando el orden con !seen[$0]++ / suma por grupos / frecuencia de palabras / unión NR==FNR / reordenar columnas CSV / la advertencia CSV / la línea más larga (9) |

La documentación oficial enlazada es solo https://www.gnu.org/software/gawk/manual/; todos los comandos se verificaron contra la Guía de Usuario de GNU Awk y se ejecutaron con éxito en mawk 1.3.4; los ejemplos operan únicamente sobre archivos de prueba creados al momento, sin datos reales.

## Por qué estas 30

- **Los contadores, claros desde el principio**: NR y FNR difieren en una letra pero gobiernan dos tareas enormes —saltar cabeceras y unir archivos—; saltar cabeceras con NR deja pasar la del segundo archivo como si fuera un dato, en silencio. La hoja lo marca con ⚠️ y da la forma correcta.
- **La trampa de tipos en las comparaciones tenía que estar**: `"010" == 10` es verdadero y `"010" == "10"` es falso: las comillas de la constante eligen comparación numérica o de texto. Es lo menos intuitivo de awk y hay que decirlo antes de que muerda.
- **Las diferencias de implementación, a la vista**: `gensub`, `BEGINFILE` y `FPAT` son exclusivos de gawk; mawk protesta con `gensub`, pero **ignora en silencio** los otros dos: el script corre y el resultado sale mal sin avisar. Al final de la hoja van enumerados, con la conclusión medida en esta máquina.
- **Recetas que sobreviven en el historial**: sumar, deduplicar, agrupar y unir son el grueso del uso diario de awk; cada una viene en la forma en la que basta cambiar el número de campo.

## Se aceptan PR

¿Un comando incorrecto, una opción obsoleta, una traducción mejorable? Los Pull Requests son bienvenidos. Mantén el formato de las entradas (una línea de explicación / bloque bash / etiquetas) y **solo comandos reales y verificados; ante la duda, déjalo fuera**.

## 📚 Más hojas de referencia

Otras hojas del mismo autor, también en chino / español / portugués:

- [Hoja de SSH](https://github.com/Ezra-Zhao/ssh-cheatsheet) — 28 comandos de conexión, claves, túneles y endurecimiento
- [Hoja de FFmpeg](https://github.com/Ezra-Zhao/ffmpeg-cheatsheet) — 28 comandos de audio y vídeo
- [Hoja de Git](https://github.com/Ezra-Zhao/git-cheatsheet) — 30 comandos diarios y de rescate
- [Hoja de Docker](https://github.com/Ezra-Zhao/docker-cheatsheet) — 28 comandos de contenedores
- [Hoja de curl](https://github.com/Ezra-Zhao/curl-cheatsheet) — 29 comandos para depurar APIs
- [Hoja de expresiones regulares](https://github.com/Ezra-Zhao/regex-cheatsheet) — 30 patrones prácticos
- [Hoja de jq](https://github.com/Ezra-Zhao/jq-cheatsheet) — 30 filtros para JSON
- [Hoja de superpoderes de terminal](https://github.com/Ezra-Zhao/terminal-superpowers-cheatsheet) — 14 herramientas de productividad
- [Hoja de find + xargs](https://github.com/Ezra-Zhao/find-xargs-cheatsheet) — 30 comandos para buscar archivos y actuar en lote
- [Hoja de kubectl](https://github.com/Ezra-Zhao/kubectl-cheatsheet) — comandos habituales de Kubernetes
- [Hoja de SQL](https://github.com/Ezra-Zhao/sql-cheatsheet) — consultas y funciones habituales
- [Hoja de Vim](https://github.com/Ezra-Zhao/vim-cheatsheet) — 30 comandos de edición, búsqueda y macros
- [Hoja de tmux](https://github.com/Ezra-Zhao/tmux-cheatsheet) — 30 comandos de sesiones, ventanas y paneles
- [Hoja de systemd](https://github.com/Ezra-Zhao/systemd-cheatsheet) — 30 comandos de servicios y registros

## Licencia

MIT © 2026 Guangyi Zhao. Véase [LICENSE](LICENSE).
