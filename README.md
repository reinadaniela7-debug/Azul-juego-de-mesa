# Azul — juego de mosaicos para 2–4 jugadores

Juego web (no oficial, hecho por fans) basado en las reglas de **Azul**. Se juega en el navegador, sin instalar nada:

- Contra la computadora (1 a 3 rivales, tres niveles) o con amigos por enlace (online, hasta 4 jugadores).
- Incluye la variante del lado gris del tablero.
- Animaciones que se pueden apagar o saltar, perfiles con estadísticas y recuperación de contraseña por correo (opcional).

## Archivos

| Archivo | Para qué sirve |
|---|---|
| `index.html` | El juego completo (no necesitas tocarlo). |
| `firebase-config.js` | Aquí pegas los datos de Firebase si quieres activar los perfiles. Si lo dejas vacío, el juego funciona igual, solo como invitado. |
| `README.md` | Esta guía. |

No hay nada que instalar ni compilar: son archivos estáticos.

---

## 1) Publicarlo en GitHub Pages (gratis)

1. En GitHub: **New repository** (por ejemplo `azul`). Con una cuenta gratuita, el repositorio debe ser **público** para usar Pages.
2. Sube los tres archivos (**Add file → Upload files**, arrastrando `index.html`, `firebase-config.js` y `README.md` a la raíz del repositorio) y pulsa **Commit changes**.
3. Ve a **Settings → Pages**. En *Build and deployment* elige **Deploy from a branch**, rama **main** y carpeta **/ (root)**, y guarda.
4. Espera 1–2 minutos. Arriba de esa misma pantalla aparecerá tu dirección:
   `https://TU-USUARIO.github.io/azul/`
5. Ábrela y listo. Para jugar con un amigo: **Jugar con un amigo → copiar el enlace** y se lo mandas. Ese enlace ya incluye el código de la sala (`#sala=XXXXX`).

Cada vez que cambies un archivo en el repositorio, Pages se actualiza solo (tarda un minuto).

**Importante para jugar online:** el anfitrión debe mantener su pestaña abierta durante la partida (la conexión es directa entre navegadores, sin servidor propio). En algunas redes muy restrictivas (wifi de empresa o universidad) puede no conectar.

---

## 2) Activar los perfiles (opcional)

Con los perfiles, cada persona puede **crear una cuenta**, ver sus **victorias, derrotas y empates** en "Mi perfil" y **recuperar su contraseña** con un correo. Quien reciba una invitación puede **crear un perfil o jugar como invitado**.

Se usa **Firebase** (de Google), que es gratis para este uso (plan *Spark*). Solo se configura una vez:

### a) Crear el proyecto
1. Entra a <https://console.firebase.google.com> con tu cuenta de Google → **Crear un proyecto** (puedes desactivar Google Analytics).

### b) Activar el inicio de sesión con correo
1. En el menú: **Compilación (Build) → Authentication → Comenzar**.
2. Pestaña **Sign-in method** → **Correo electrónico/contraseña** → **Habilitar** → Guardar.

### c) Crear la base de datos de estadísticas
1. **Compilación → Firestore Database → Crear base de datos** (elige una ubicación cercana; modo producción).
2. Pestaña **Reglas** → borra lo que haya, pega esto y pulsa **Publicar**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

Estas reglas hacen que **cada persona solo pueda leer y escribir su propio perfil**. Son lo que protege los datos, así que no las omitas.

### d) Conectar la web con tu proyecto
1. En **Configuración del proyecto** (el engranaje) → **Tus apps** → icono web **`</>`** → ponle un nombre y registra la app.
2. Firebase te mostrará un bloque `firebaseConfig`. Copia `apiKey`, `authDomain`, `projectId` y `appId` dentro de **`firebase-config.js`** (puedes editarlo directamente en GitHub con el lápiz ✏️ y hacer *Commit*).

### e) Autorizar tu dirección de GitHub Pages
1. **Authentication → Settings → Authorized domains → Add domain** y agrega `TU-USUARIO.github.io`.

### f) El correo de recuperación de contraseña
- Se envía solo, desde Firebase, al correo con el que la persona se registró.
- Para que llegue en español: **Authentication → Templates → Restablecer contraseña** → cambia el idioma a Español (y, si quieres, personaliza el texto).
- A veces llega a la carpeta de **spam**; la pantalla de recuperación ya lo avisa.

### ¿Es seguro dejar los datos de Firebase en un repositorio público?
Sí: los datos de `firebase-config.js` **no son contraseñas**, están pensados para ir en páginas públicas (cualquiera que abra tu juego los puede ver). Lo que protege tu información son las **reglas de Firestore** (paso c) y los **dominios autorizados** (paso e). **Nunca** subas a GitHub claves de "cuenta de servicio" ni archivos de administrador.

---

## Notas

- **Estadísticas:** cada jugador guarda su propio resultado al terminar una partida (victoria, derrota o empate compartido). Si alguien abandona antes del final, esa partida no se cuenta.
- **Invitados:** quien juega como invitado no guarda estadísticas; al final de la partida se le recuerda que puede crear un perfil.
- **Deshacer (contra la computadora):** el botón "↩ Deshacer" (o Ctrl+Z) devuelve tu última jugada, junto con lo que la computadora hizo después. Se puede retroceder varias jugadas seguidas, pero no a través de una ronda nueva.
- **Confirmar (online):** en partidas con amigos no se puede retroceder una jugada que otros ya vieron, así que antes de colocar aparece una confirmación con vista previa. Se coloca sola a los 10 segundos; puedes confirmar al instante, cancelar o tocar otra línea para cambiar el destino.
- **Aviso de última ronda:** el juego avisa cuando la ronda actual terminará la partida (🏁), o cuando podría ser la última (⚠).
- **Marca:** "Azul" es un juego de Michael Kiesling publicado por Plan B Games. Esta es una versión de fans, con arte propio, para uso personal y sin fines comerciales. Si algún día la usaras comercialmente, tendrías que cambiar el nombre y contar con los derechos.

## Foto de perfil
Con la sesión iniciada, en **Mi perfil → Subir foto de perfil** puedes elegir una imagen. Se recorta y se reduce a 96×96 px y se guarda en tu perfil (Firestore, no hace falta Firebase Storage). Aparece junto a tu nombre en la barra de puntos y en tu tablero, y tus amigos la ven también en partidas en línea.

## Consejos (apagados por defecto)
Durante la partida, el botón **🎓 Consejos** los activa o desactiva. Al activarlos aparecen **💡 Pista** (resalta la mejor jugada) y **📒 Mis jugadas** (tu registro). Cada jugada se compara con la que elegiría la computadora en modo difícil; al final de la partida ves un repaso con consejos. Con sesión iniciada, el repaso se guarda en tu perfil (**Ver jugadas** en el historial). Todo se calcula en tu navegador.
