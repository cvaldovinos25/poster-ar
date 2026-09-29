# Póster AR

Experiencia de realidad aumentada con reconocimiento de imagen: al apuntar la cámara del celular a un póster impreso, se reproduce un video superpuesto exactamente sobre él.

🔗 **Demo:** https://cvaldovinos25.github.io/poster-ar/

## Cómo funciona

1. La persona escanea un QR (o abre el link) que carga la página.
2. La página pide permiso de cámara y empieza a buscar el póster en la imagen en vivo.
3. Cuando la cámara reconoce el póster (`targetFound`), se reproduce un video anclado sobre él, como si el afiche cobrara vida.
4. Si el póster sale de cuadro (`targetLost`), el video se pausa y se muestra un aviso en pantalla pidiendo volver a apuntar.

No requiere apps ni instalación: funciona directo desde el navegador (Safari en iPhone, Chrome en Android).

## Tecnología

- **[A-Frame](https://aframe.io/)** (1.5.0) — motor de escena 3D/WebXR.
- **[MindAR](https://hiukim.github.io/mind-ar-js-doc/)** (1.2.5) — reconocimiento y tracking de imagen (image target).
- Sin build ni dependencias locales: todo corre en un único `index.html`, cargando las librerías desde CDN.

## Estructura del proyecto

```
poster-ar/
├── index.html              # Toda la lógica y el markup de la experiencia
├── assets/
│   ├── targets.mind        # Póster "compilado" para que MindAR lo reconozca
│   └── animacion.mp4       # Video que se reproduce sobre el póster
└── README.md
```

## Cómo reemplazar el póster o el video

### 1. Cambiar el póster (imagen que se reconoce)

MindAR necesita una versión "compilada" (`.mind`) de la imagen del póster, no la imagen tal cual:

1. Ir a la herramienta oficial de compilación: https://hiukim.github.io/mind-ar-js-doc/tools/compile
2. Subir la imagen del póster (con buen contraste y detalle: los logos lisos o de un solo color no trackean bien).
3. Descargar el archivo generado y reemplazar `assets/targets.mind`.

### 2. Cambiar el video

Reemplazar `assets/animacion.mp4` (debe llamarse igual, o si cambia el nombre hay que actualizar el `src` del `<video id="video-poster">` en `index.html`). Requisitos:

- Formato `.mp4` (H.264), para compatibilidad con iPhone y Android.
- Debe partir **silenciado** (`muted`) para que el navegador permita reproducirlo automáticamente sin que la persona toque la pantalla primero. Al primer toque se activa el sonido si el video lo tiene.

### 3. Ajustar la proporción del video sobre el póster

En `index.html`, el `<a-video>` tiene `width="1"` fijo (representa el ancho completo del póster) y `height` según la proporción del afiche:

| Formato del póster | `height` |
|---|---|
| Vertical A3/A4 (1:1.414) | `1.414` |
| Vertical 2:3 | `1.5` |
| Cuadrado | `1` |
| Horizontal 16:9 | `0.5625` |

## Mensajes en pantalla

- **Al cargar / cámara lista:** invita a apuntar al póster.
- **Al perder el rastreo:** pide volver a apuntar; desaparece recién cuando el video *realmente* comienza a reproducirse (no apenas se intenta), para evitar mensajes que se ocultan de más.
- **Errores** (cámara denegada, archivos faltantes, video no soportado): se muestran en un aviso aparte, abajo de la pantalla, con el motivo específico.

## Despliegue

Se publica con **GitHub Pages** directo desde este repositorio. Cualquier cambio en `main` se refleja en la URL pública después de que GitHub reconstruye el sitio (puede tardar uno o dos minutos).

## Requisitos para probarlo

- Un teléfono real (la cámara y el tracking no funcionan igual —o no funcionan— en un navegador de escritorio sin cámara trasera).
- Conexión a internet (carga A-Frame y MindAR desde CDN).
- Permiso de cámara otorgado al sitio.
- El póster impreso, bien iluminado y sin reflejos fuertes.

## Problemas conocidos

- Hay una condición de carrera poco frecuente donde el evento `playing` del video puede llegar justo después de perder el rastreo del póster, ocultando el aviso de "vuelve a apuntar" antes de tiempo. Pendiente de corregir.
- `animacion.mp4` pesa ~14 MB, lo que puede demorar la primera carga en datos móviles. Conviene comprimirlo si el video final crece mucho más.
