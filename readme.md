# Guía Completa de Git y GitHub — Ejercicio Práctico Secuencial

> Objetivo: partir de cero y llegar a dominar el flujo completo de Git y GitHub,
> incluyendo ramas, fusiones, conflictos, rebase, Pull Requests y comandos avanzados.
> Cada sección tiene: **Teoría breve** + **Práctica (comandos a ejecutar en orden)**.
>
> Se construirá un proyecto ficticio llamado `mi-proyecto-git` a lo largo de todo el ejercicio.
> Ejecuta los comandos en tu terminal, en el orden en que aparecen, sin saltarte pasos.

---

## Índice

1. [Configuración inicial](#1-configuración-inicial)
2. [Crear el repositorio local](#2-crear-el-repositorio-local)
3. [Flujo básico: add, commit, status, log](#3-flujo-básico-add-commit-status-log)
4. [Conectar con GitHub (remoto)](#4-conectar-con-github-remoto)
5. [push, pull, fetch, clone](#5-push-pull-fetch-clone)
6. [.gitignore](#6-gitignore)
7. [Ramas (branches)](#7-ramas-branches)
8. [Fusiones (merge)](#8-fusiones-merge)
9. [Conflictos de fusión](#9-conflictos-de-fusión)
10. [Rebase](#10-rebase)
11. [Pull Requests en GitHub](#11-pull-requests-en-github)
12. [Deshacer cambios (restore, reset, revert)](#12-deshacer-cambios-restore-reset-revert)
13. [Stash](#13-stash)
14. [Cherry-pick](#14-cherry-pick)
15. [Tags y releases](#15-tags-y-releases)
16. [Comandos de inspección avanzados](#16-comandos-de-inspección-avanzados)
17. [Buenas prácticas y convenciones](#17-buenas-prácticas-y-convenciones)
18. [Ejercicio integrador final](#18-ejercicio-integrador-final)

---

## 1. Configuración inicial

### Teoría
Git guarda tu identidad (nombre y correo) en cada commit. Esto se configura una sola vez por máquina (`--global`) o por proyecto (sin `--global`).

### Práctica
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@correo.com"

# Ver toda la configuración actual
git config --list

# (Opcional) Editor por defecto para mensajes de commit
git config --global core.editor "nano"

# (Opcional) Que la rama por defecto se llame "main"
git config --global init.defaultBranch main
```

---

## 2. Crear el repositorio local

### Teoría
`git init` convierte una carpeta normal en un repositorio Git, creando la carpeta oculta `.git` donde se guarda todo el historial.

### Práctica
```bash
mkdir mi-proyecto-git
cd mi-proyecto-git
git init

# Verifica que se creó el repositorio
ls -la
```

Deberías ver una carpeta `.git/`. Eso es el "cerebro" del repositorio.

---

## 3. Flujo básico: add, commit, status, log

### Teoría
Git tiene 3 zonas:

| Zona | Descripción |
|---|---|
| **Working directory** | Tus archivos tal cual los ves y editas |
| **Staging area (index)** | Cambios marcados con `git add`, listos para el commit |
| **Repository (historial)** | Cambios guardados permanentemente con `git commit` |

### Práctica
```bash
# Crea un archivo
echo "# Mi Proyecto Git" > README.md

# Ver el estado (archivo sin trackear)
git status

# Añadir el archivo al staging area
git add README.md

# Ver el estado de nuevo (ahora está "staged")
git status

# Confirmar el cambio (crear un commit)
git commit -m "Commit inicial: agrega README"

# Ver historial de commits
git log

# Ver historial resumido en una línea
git log --oneline
```

Agrega más contenido y repite el ciclo:
```bash
echo "console.log('Hola mundo');" > app.js
git add app.js
git commit -m "Agrega archivo app.js"

# Atajo: añade TODOS los cambios rastreados y nuevos
git add .
git status
```

---

## 4. Conectar con GitHub (remoto)

### Teoría
Un **remoto** es una copia del repositorio alojada en un servidor (GitHub). `origin` es el nombre convencional que se le da al remoto principal.

### Práctica
1. Ve a GitHub.com → **New repository** → nómbralo `mi-proyecto-git` → **no** inicialices con README (ya tienes uno local) → Create repository.
2. Copia la URL (HTTPS o SSH) que te da GitHub.

```bash
# Vincular tu repo local con el remoto de GitHub
git remote add origin https://github.com/TU-USUARIO/mi-proyecto-git.git

# Verificar remotos configurados
git remote -v
```

---

## 5. push, pull, fetch, clone

### Teoría
- **push**: sube tus commits locales al remoto.
- **fetch**: descarga los cambios del remoto pero NO los mezcla con tu rama local.
- **pull**: es `fetch` + `merge` en un solo paso (descarga y mezcla automáticamente).
- **clone**: descarga un repositorio completo (historial incluido) por primera vez.

### Práctica
```bash
# Subir tu rama local "main" al remoto y vincularla (-u = upstream)
git push -u origin main

# A partir de aquí, con -u ya configurado, basta con:
git push

# Simular que alguien más (o tú desde otra máquina) hizo cambios en GitHub:
# edita el README.md directamente en la web de GitHub y confirma el cambio (commit).

# Descargar esos cambios sin mezclarlos aún
git fetch origin
git log origin/main --oneline   # ver qué trae el remoto

# Descargar y mezclar en un solo paso
git pull origin main
```

Clonar un repositorio existente (útil en otra máquina o para un compañero de equipo):
```bash
git clone https://github.com/TU-USUARIO/mi-proyecto-git.git carpeta-clonada
cd carpeta-clonada
```

---

## 6. .gitignore

### Teoría
Le dice a Git qué archivos o carpetas **nunca** debe rastrear (dependencias, archivos temporales, credenciales, etc.).

### Práctica
```bash
cd ../mi-proyecto-git   # regresa a tu repo original si saliste

cat > .gitignore << 'EOF'
node_modules/
*.log
.env
.DS_Store
EOF

git add .gitignore
git commit -m "Agrega .gitignore"
git push
```

---

## 7. Ramas (branches)

### Teoría
Una rama es una línea independiente de desarrollo. `main` suele ser la rama estable; el trabajo nuevo se hace en ramas separadas (`feature/…`, `fix/…`) y luego se integra.

### Práctica
```bash
# Ver ramas existentes
git branch

# Crear una nueva rama
git branch feature/login

# Cambiarte a esa rama
git checkout feature/login

# Atajo moderno: crear y cambiar en un solo comando
git checkout -b feature/registro
# o el comando más nuevo (recomendado):
git switch -c feature/registro

# Confirmar en qué rama estás
git branch
```

Haz cambios en la nueva rama:
```bash
echo "function login() { return true; }" > login.js
git add login.js
git commit -m "Agrega función de login"

# Sube la rama nueva a GitHub
git push -u origin feature/registro
```

Volver a `main`:
```bash
git switch main
# o: git checkout main
```

---

## 8. Fusiones (merge)

### Teoría
`git merge` integra los commits de una rama dentro de otra. Si no hay cambios en conflicto, Git hace un **fast-forward** o crea un **merge commit** automáticamente.

### Práctica
```bash
# Estando en main, fusiona la rama feature/registro
git switch main
git merge feature/registro

# Verifica el historial fusionado
git log --oneline --graph --all

# Sube main actualizado
git push

# (Opcional) Elimina la rama ya fusionada, local y remota
git branch -d feature/registro
git push origin --delete feature/registro
```

---

## 9. Conflictos de fusión

### Teoría
Un conflicto ocurre cuando dos ramas modificaron **la misma línea** de un archivo de forma distinta. Git no puede decidir por ti: debes resolverlo manualmente.

### Práctica
Provoquemos un conflicto a propósito:

```bash
# En main, edita README.md
git switch main
echo "Versión desde MAIN" >> README.md
git add README.md
git commit -m "Actualiza README desde main"

# Crea una rama desde un punto anterior y edita la misma línea distinto
git switch -c feature/conflicto
echo "Versión desde FEATURE" >> README.md
git add README.md
git commit -m "Actualiza README desde feature"

# Vuelve a main e intenta fusionar
git switch main
git merge feature/conflicto
```

Git marcará el conflicto dentro del archivo así:
```
<<<<<<< HEAD
Versión desde MAIN
=======
Versión desde FEATURE
>>>>>>> feature/conflicto
```

**Resolverlo:**
1. Abre `README.md` y edita manualmente, dejando el contenido final que quieras (borra las marcas `<<<<<<<`, `=======`, `>>>>>>>`).
2. Marca el archivo como resuelto y confirma:
```bash
git add README.md
git commit -m "Resuelve conflicto en README.md"
git push
```

> Tip: `git merge --abort` cancela la fusión y regresa todo al estado anterior si te arrepientes a mitad del conflicto.

---

## 10. Rebase

### Teoría
`git rebase` "reescribe" el historial moviendo tus commits para que parezcan creados a partir de la última versión de otra rama, generando un historial **lineal** (sin merge commits). Útil para mantener limpio el historial antes de un Pull Request. **Regla de oro:** nunca hagas rebase de ramas ya compartidas/públicas sin avisar al equipo.

### Práctica
```bash
git switch -c feature/perfil
echo "function perfil() {}" > perfil.js
git add perfil.js
git commit -m "Agrega función perfil"

# Mientras tanto, imagina que main avanzó (simulamos otro commit en main)
git switch main
echo "Nota de main" >> README.md
git add README.md
git commit -m "Otro cambio en main"

# Vuelve a tu rama feature y aplica rebase sobre main actualizado
git switch feature/perfil
git rebase main

# Si hay conflicto durante el rebase:
#   1. Resuelve el archivo igual que en un merge
#   2. git add archivo-resuelto
#   3. git rebase --continue
#   (o git rebase --abort para cancelar todo)

git push -u origin feature/perfil
# Si ya habías hecho push antes del rebase, necesitarás:
git push --force-with-lease
```

---

## 11. Pull Requests en GitHub

### Teoría
Un **Pull Request (PR)** es una solicitud para fusionar los cambios de una rama hacia otra (normalmente `feature → main`), permitiendo revisión de código, comentarios y validación (CI) antes de integrarlos.

### Práctica (interfaz web de GitHub)
1. Sube tu rama si no lo has hecho: `git push -u origin feature/perfil`.
2. En GitHub, entra al repositorio → verás un aviso **"Compare & pull request"** → clic ahí.
3. Define:
   - **base**: `main` (a dónde quieres fusionar)
   - **compare**: `feature/perfil` (la rama con tus cambios)
4. Escribe un título y descripción clara de qué cambia y por qué.
5. (Opcional) Asigna revisores, etiquetas (labels) y un proyecto.
6. Clic en **Create pull request**.
7. Espera revisión / comentarios. Puedes seguir subiendo commits a la misma rama y el PR se actualiza solo.
8. Cuando esté aprobado, clic en **Merge pull request** (elige estrategia: *Merge commit*, *Squash and merge*, o *Rebase and merge*).
9. Elimina la rama desde el botón que aparece tras el merge (o con `git push origin --delete feature/perfil`).

### Sincronizar tu copia local tras el merge en GitHub
```bash
git switch main
git pull origin main
git branch -d feature/perfil
```

---

## 12. Deshacer cambios (restore, reset, revert)

### Teoría
| Comando | Qué hace | Nivel de "peligro" |
|---|---|---|
| `git restore <archivo>` | Descarta cambios no confirmados en working directory | Bajo |
| `git restore --staged <archivo>` | Saca un archivo del staging area (sin borrar cambios) | Bajo |
| `git reset --soft <commit>` | Mueve HEAD, mantiene cambios en staging | Medio |
| `git reset --mixed <commit>` | Mueve HEAD, mantiene cambios sin stage (default) | Medio |
| `git reset --hard <commit>` | Mueve HEAD y **borra** cambios sin confirmar | Alto (irreversible) |
| `git revert <commit>` | Crea un commit nuevo que deshace otro (seguro para historial compartido) | Bajo |

### Práctica
```bash
# Descartar un cambio no confirmado
echo "prueba" >> README.md
git restore README.md

# Sacar del staging sin perder el cambio
echo "prueba2" >> README.md
git add README.md
git restore --staged README.md

# Ver commits anteriores
git log --oneline

# Revertir un commit específico (crea un commit nuevo que lo anula)
git revert <HASH_DEL_COMMIT>

# Reset "mixed" a un commit anterior (cambios locales se conservan, sin stage)
git reset <HASH_DEL_COMMIT>

# Reset "hard" (¡CUIDADO! borra cambios no confirmados)
git reset --hard <HASH_DEL_COMMIT>
```

---

## 13. Stash

### Teoría
`git stash` guarda temporalmente cambios no confirmados sin hacer commit, dejando el working directory limpio. Útil para cambiar de rama rápidamente sin perder trabajo en progreso.

### Práctica
```bash
echo "trabajo a medias" >> app.js

# Guardar cambios en el stash
git stash

# Ver la lista de stashes guardados
git stash list

# Working directory queda limpio, puedes cambiar de rama, etc.
git status

# Recuperar el último stash y eliminarlo de la lista
git stash pop

# (Alternativa: aplicar sin eliminar de la lista)
git stash apply

# Eliminar un stash específico
git stash drop stash@{0}
```

---

## 14. Cherry-pick

### Teoría
`git cherry-pick <hash>` toma **un commit específico** de otra rama y lo aplica sobre tu rama actual, sin traer todo el historial de esa rama.

### Práctica
```bash
git log feature/login --oneline    # localiza el hash del commit que quieres

git switch main
git cherry-pick <HASH_DEL_COMMIT>

# Si hay conflicto: resuelve igual que en merge/rebase y luego:
git add <archivo-resuelto>
git cherry-pick --continue
```

---

## 15. Tags y releases

### Teoría
Los **tags** marcan puntos específicos del historial, normalmente versiones (`v1.0.0`). GitHub puede convertir un tag en un **Release** formal con notas de versión.

### Práctica
```bash
# Tag simple (ligero)
git tag v1.0.0

# Tag anotado (recomendado: incluye mensaje, autor y fecha)
git tag -a v1.0.0 -m "Primera versión estable"

# Ver tags
git tag

# Subir un tag específico
git push origin v1.0.0

# Subir todos los tags
git push origin --tags
```
En GitHub: **Releases → Draft a new release** → selecciona el tag → agrega notas → **Publish release**.

---

## 16. Comandos de inspección avanzados

### Teoría
Comandos para entender qué pasó, cuándo y por quién.

### Práctica
```bash
# Historial gráfico de todas las ramas
git log --oneline --graph --all --decorate

# Ver diferencias entre working directory y último commit
git diff

# Ver diferencias entre dos ramas
git diff main feature/login

# Ver quién modificó cada línea de un archivo (line-blame)
git blame README.md

# Buscar en qué commit se introdujo un texto
git log -S "función de login" --oneline

# Ver detalles de un commit específico
git show <HASH_DEL_COMMIT>

# Ver ramas ya fusionadas a main (candidatas a borrar)
git branch --merged main
```

---

## 17. Buenas prácticas y convenciones

### Teoría (resumen)
- **Commits pequeños y frecuentes**, con mensajes claros. Convención recomendada (Conventional Commits):
  ```
  feat: agrega login de usuario
  fix: corrige validación de email
  docs: actualiza README
  refactor: simplifica lógica de perfil
  ```
- **Una rama por tarea**: `feature/nombre`, `fix/nombre`, `hotfix/nombre`.
- **Nunca** hacer `push --force` a `main` ni a ramas compartidas (usa `--force-with-lease` solo en tus propias ramas de feature).
- Mantén `main` siempre desplegable/estable; el trabajo en progreso vive en ramas.
- Revisa con `git status` y `git diff` antes de cada commit.
- Escribe un buen `.gitignore` desde el inicio del proyecto.
- Usa Pull Requests incluso trabajando solo: deja registro de por qué se hizo cada cambio.

---

## 18. Ejercicio integrador final

Con todo lo aprendido, realiza este flujo completo de principio a fin en `mi-proyecto-git`:

1. Crea una rama `feature/carrito`.
2. Agrega un archivo `carrito.js` con una función simple y haz 2 commits distintos.
3. Sube la rama a GitHub (`git push -u origin feature/carrito`).
4. Abre un Pull Request de `feature/carrito` hacia `main`.
5. Simula un conflicto: mientras tanto, edita el mismo archivo directamente en `main` (vía web de GitHub) en la misma línea.
6. Intenta actualizar tu rama con `git rebase main` (o `git merge main`) y resuelve el conflicto localmente.
7. Sube la resolución (`git push --force-with-lease` si usaste rebase).
8. Fusiona el Pull Request desde GitHub.
9. En tu máquina: `git switch main && git pull` y elimina la rama local y remota ya fusionada.
10. Crea un tag `v1.0.0` marcando este punto y súbelo a GitHub.
11. Revisa el historial final con `git log --oneline --graph --all`.

Si completas estos 11 pasos sin ayuda externa, dominas el flujo completo de Git y GitHub: básico, ramas, fusiones, conflictos, rebase y Pull Requests. 🎉

---

## Referencia rápida (cheat sheet)

```bash
git init                          # crear repo
git clone <url>                   # clonar repo
git status                        # ver estado
git add <archivo>                 # stage
git commit -m "mensaje"           # confirmar
git log --oneline --graph --all   # ver historial
git remote add origin <url>       # vincular remoto
git push -u origin <rama>         # subir rama (primera vez)
git pull                          # descargar + fusionar
git fetch                         # solo descargar
git branch                        # listar ramas
git switch -c <rama>              # crear y cambiar de rama
git merge <rama>                  # fusionar rama
git rebase <rama>                 # reescribir historial sobre otra rama
git stash / git stash pop         # guardar/recuperar cambios temporales
git cherry-pick <hash>            # traer un commit específico
git tag -a v1.0.0 -m "msg"        # crear tag
git restore <archivo>             # descartar cambios locales
git reset --hard <hash>           # volver a un commit (borra cambios)
git revert <hash>                 # deshacer un commit con uno nuevo
```
