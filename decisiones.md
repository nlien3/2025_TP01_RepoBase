# decisiones.md

## 1) Configuración del entorno y preparación del repositorio

1. Cloné el repositorio base utilizando **HTTPS**:

```bash
git clone https://github.com/ingsoft3ucc/2025_TP01_RepoBase.git
cd 2025_TP01_RepoBase
```

2. Luego verifiqué la identidad configurada en Git:

```bash
git config --get user.email
```

3. Como estaba asociada a otra cuenta, la reemplacé por la cuenta institucional de la facultad.
Configuré nombre de usuario y correo electrónico solo para este repositorio (sin --global para no afectar mis otros proyectos):

```bash
git config user.name "Nico"
git config user.email "2438538@ucc.edu.ar"
```

4. Finalmente, creé el archivo decisiones.md en la raíz del repositorio para ir documentando cada paso y justificación:

```bash
touch decisiones.md
```

### Justificación
- Elegí clonar con **HTTPS** porque es más directo en este entorno y no requiere configurar claves SSH.  
- La identidad de Git la configuré **a nivel local del repo** para mantener este proyecto académico separado de mis repositorios personales.  
- Documentar cada paso en `decisiones.md` asegura trazabilidad y me servirá de apoyo en la defensa oral del trabajo práctico.


## 2) Desarrollo de una funcionalidad

1. Me ubiqué en la rama principal:
```bash
   git switch main
```

2.	Creé una rama nueva para aislar el desarrollo de la funcionalidad:

```bash
git switch -c feature/nueva-funcionalidad
```

3.	Commit atómico #1: implementé la funcionalidad básica en un módulo nuevo.

```bash
mkdir -p src
echo 'console.log("Feature X habilitada");' > src/featureX.js
git add src/featureX.js
git commit -m "feat: implementa Feature X (módulo base en src/featureX.js)"
```


4.	Commit atómico #2: documenté la funcionalidad en el README de forma separada.

```bash
echo -e "\n## Feature X\n- Activa salida por consola." >> README.md
git add README.md
git commit -m "docs: documenta Feature X en README"
```

5.	Intenté pushear directamente al repo original, pero dio error (no tengo permisos).
Solución: hice un fork y apunté origin a mi propio fork (nlien3/2025_TP01_RepoBase), y agregué upstream al repo original:

```bash
git remote set-url origin https://github.com/nlien3/2025_TP01_RepoBase.git
git remote add upstream https://github.com/ingsoft3ucc/2025_TP01_RepoBase.git o git remote set-url origin https://nlien3@github.com/nlien3/2025_TP01_RepoBase.git (esto sirve para cuando ya tenes otra cuenta y necesitas que para esta te pida el access token)
git remote -v
```

6.	Finalmente, subí mi rama de trabajo a mi fork:

```bash
git push -u origin feature/nueva-funcionalidad
```

### Justificación
- Usé una rama de feature para no trabajar directamente sobre main.
- Hice commits atómicos: un commit implementa el código y otro documenta, lo cual mejora la trazabilidad.
- Configuré origin y upstream siguiendo las buenas prácticas de trabajo con forks.

## 3) Corregir un error (simulado) y aplicar el fix

1.	Simulé un bug en la rama main agregando una línea problemática al archivo src/app.txt:
```bash
git switch main
echo "BUG: rompe despliegue" >> src/app.txt
git add src/app.txt
git commit -m "chore: simula bug en producción en src/app.txt"
git push origin main
```

2.	Creé una rama hotfix/bug-critico desde main y apliqué la corrección eliminando la línea agregada:

```bash
git switch -c hotfix/bug-critico

# Corrección en macOS (BSD sed):
sed -i '' 's/BUG: rompe despliegue//g' src/app.txt

git add src/app.txt
git commit -m "fix: elimina línea que rompía producción (src/app.txt)"
```

3.	Integré el hotfix nuevamente en main mediante un merge con --no-ff para dejar un commit de fusión explícito en el historial:

```bash
git switch main
git merge --no-ff hotfix/bug-critico -m "merge: integra hotfix de bug crítico a main"
git push origin main
```
4. Apliqué el mismo fix también en mi rama de desarrollo `feature/nueva-funcionalidad`, utilizando **cherry-pick** para traer solo el commit de la corrección sin arrastrar otros cambios de `main`:

```bash
# Ver el hash del commit del fix
git log --oneline | head -n 10

# Cambiar a la rama de feature e importar ese commit
git switch feature/nueva-funcionalidad
git cherry-pick 6a75340
git push origin feature/nueva-funcionalidad
```

### Justificación
- Simulé un bug en main para recrear un escenario de error en producción.
- Usé una rama de hotfix separada para aislar la corrección urgente.
- El merge con --no-ff garantiza que en el historial quede un commit de merge, lo que facilita la trazabilidad de cuándo y cómo se resolvió el bug.
- Con este enfoque mantuve la historia clara: main muestra el error, la corrección y el merge del hotfix como un evento independiente.
- Elegí cherry-pick porque me permite traer únicamente el fix, sin fusionar toda la rama main con la feature.
- Esto mantiene la rama de desarrollo enfocada en su funcionalidad, pero ya con el bug corregido.




## 4) Pull Request y Merge

1. Verifiqué que mi rama de feature tenía commits que no estaban en `main`:
```bash
git log main..feature/nueva-funcionalidad --oneline
# a870a2c docs: documenta Feature X en README
# f0b7724 feat: implementa Feature X (módulo base en src/featureX.js)
```
2.	En GitHub, creé un Pull Request desde feature/nueva-funcionalidad hacia main.
-	Base: main
-	Compare: feature/nueva-funcionalidad
-	Título: feat: integrar Feature X
-	Descripción:
Integra los cambios de la rama feature/nueva-funcionalidad:
    Implementación de Feature X en src/featureX.js
    Documentación en README.md

3.	Acepté el PR en GitHub con Merge pull request → Confirm merge.
4.	Finalmente, actualicé mi rama local main para traer los cambios ya integrados:

```bash
git switch main
git pull origin main
```

### Justificación
- Usé Pull Request para integrar la rama de desarrollo de forma controlada y trazable.
- El PR deja un registro en GitHub del título, descripción y commits, lo que mejora la transparencia del proceso.
- Elegí merge vía PR en lugar de un merge local directo porque refleja un flujo de trabajo profesional en proyectos colaborativos.


## 5) Crear una versión etiquetada

1. Me aseguré de estar en `main` y actualizado:
```bash
git switch main
git pull origin main
```

2.	Creé un tag anotado v1.0:

```bash
git tag -a v1.0 -m "Versión estable inicial v1.0"
```

3.	Verifiqué que el tag estaba creado:

```bash
git tag
# v1.0
```

4.	Finalmente, subí el tag a GitHub:

```bash
git push origin v1.0
```

### Justificación
- Usé un tag anotado porque guarda información adicional (autor, fecha y mensaje), lo cual mejora la trazabilidad.
- Elegí v1.0 siguiendo la convención de versionado semántico (SemVer), donde la primera versión estable de un proyecto se marca como 1.0.
- Este tag marca un hito estable del desarrollo, lo que permite volver a este punto exacto en la historia si es necesario.


