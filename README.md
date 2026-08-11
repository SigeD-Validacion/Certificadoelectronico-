# Proyecto: Certificado Electrónico - Validación

Sistema para generar folio, sello digital y QR para documentos (certificados, constancias, etc.) con campos personalizables, y validar públicamente el documento al escanear el QR.

## Archivos del proyecto (los únicos que necesitas en el repositorio)

- `generador.html` — página con login (correo/contraseña) donde capturas los campos del documento y generas folio + sello + QR. Solo tú (con tu usuario autorizado) puedes usarla.
- `validar.html` — página pública que cualquiera ve al escanear el QR. Muestra los datos del documento y si el sello es válido.

Ya **no se necesitan** `login.html` ni `config.js` — el login y la configuración de Firebase están integrados directamente dentro de `generador.html` y `validar.html`.

## Cómo funciona

1. En `generador.html`, defines los campos que quieras (Nombre, CURP, Escuela, Promedio, etc. — los que necesites, puedes agregar o quitar).
2. Al generar, se crea:
   - **Folio**: identificador único de 36 caracteres en bloques (ej. `AIzaSyDzAId7SfCUZp0kcHlx9CVOmUF7Cvu8wCI`)
   - **Sello digital**: 256 caracteres, calculado con SHA-512 a partir del folio y los campos — si alguien altera un dato guardado, el sello ya no coincide.
   - **Fecha y hora de timbrado**
   - **Código QR** que apunta a la URL de validación con el folio.
3. Todo se guarda en Firebase Realtime Database, en `documentos/{folio}`.
4. Al escanear el QR, se abre `validar.html?folio=...`, que consulta esos datos y recalcula el sello para confirmar que no fueron alterados.

## Configuración actual

- **Firebase (Realtime Database)**: proyecto `Certificadoelectronico-`, base en `https://certificadoelectronico-89f1e-default-rtdb.firebaseio.com/`
- **Reglas**: lectura pública en `documentos/`, escritura solo con usuario autenticado (Firebase Authentication, correo/contraseña)
- **GitHub Pages**: `https://sigedvalidacion.github.io/Certificadoelectronico-/`
  - Generador: `.../generador.html`
  - Validación: `.../validar.html?folio=...`

## Si necesitas cambiar algo en el futuro

- **Cambiar de usuario o repositorio en GitHub**: hay que actualizar la constante `URL_VALIDACION` dentro de `generador.html` (es la única parte del código que depende de esa URL).
- **Cambiar la configuración de Firebase**: el objeto `firebaseConfig` está repetido en `generador.html` y `validar.html` — hay que actualizarlo en los dos.
- **Agregar/quitar campos del documento**: no requiere tocar código, se hace directamente en el formulario de `generador.html`.

## Notas de seguridad

- Solo un usuario autenticado en Firebase (correo/contraseña que tú diste de alta) puede generar documentos nuevos.
- El sello digital detecta alteraciones en los datos guardados, pero no sustituye el control de acceso — la protección real está en las reglas de Firestore/Database que impiden escrituras no autorizadas.
- Este sistema es propio: no imita ni hace referencia a ningún sistema gubernamental oficial (SEP, SAT, SIGED, etc.).
- 
