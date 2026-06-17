# Estudio I2 EDD

Repositorio para construir un sitio Quarto de apoyo al estudio de la evaluación I2 de Estructuras de Datos y Algoritmos.

El contenido se desarrollará página por página. Esta primera versión deja configurada la estructura del sitio, la navegación y las plantillas base para completar cada tema.

## Render local

Para generar el sitio en la carpeta `docs/`:

```bash
quarto render
```

## Previsualización local

Para levantar una vista local con recarga automática:

```bash
quarto preview
```

## Publicación en GitHub Pages

En la configuración del repositorio en GitHub, GitHub Pages debe publicarse desde:

- rama: `main`
- carpeta: `docs`

No se deben subir PDFs del curso al repositorio. Las fuentes usadas como apoyo conceptual se registran internamente en `apoyo/fuentes.md`.
