# Informe ASTRA CLOUD

Este directorio contiene el informe técnico en LaTeX del proyecto ASTRA CLOUD.

## Estructura

- `main.tex`: documento principal.
- `sections/`: capítulos del informe.
- `figures/`: figuras utilizadas y sus fuentes TikZ regenerables.
- `evidence/`: capturas y archivos de respaldo de las evidencias usadas en el informe.
- `assets/`: recursos de presentación, incluido el logo institucional.
- `archive/`: respaldos históricos, capturas de QA y evidencias antiguas no clasificadas. No participa en la compilación.

La infraestructura está en `../infrastructure/` y los microservicios se mantienen de forma independiente en `../sources/`.

## Compilación

Desde este directorio ejecute:

```text
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

El resultado es `main.pdf`. Los archivos auxiliares de LaTeX se regeneran y se excluyen mediante el `.gitignore` de la raíz.
