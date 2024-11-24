# **Tarea Github Actions**
## Por Víctor Castro Gálvez

## Primer ejercicio:  
El primer ejercicio consiste en crear un workflow CI para el proyecto de frontend.  
Como, al igual que en el resto de las tareas de este módulo de CI/CD, voy a seguir usando el directorio `exercises` para presentar los trabajos, lo primero que voy a hacer es copiar todo el proyecto en ese subdirectorio, creando un subdirectorio que se va a llamar: `exercises/github-actions`.  
Una vez creada la estructura del directorio de trabajo, en el raíz creo la carpeta .github/workflows y me creo el workflow de ci, al que voy a llamar `ci-hangman-front.yml`. El workflow se debe disparar cuando haya cambios en hangman-front y se cree una nueva pull request.  
Ahora añado los pasos del workflow. Como el proyecto usa npm, añado los pasos para instalar las dependencias, añadir el build del proyecto y los tests unitarios. El resultado final puede verse en el fichero ci-hangman-front.yml.  
Pero antes de seguir, vamos a iniciar git en el subdirectorio `github-actions` para poder empezar a trabajar en él. Para ello:  
Me voy al repositorio en local (carpeta exercises/github-actions/).  
Inicializo git ahí:  
```bash
git init
```
Me llevo los cambios:  
```
git add .
git commit -m "Commit inicial para el proyecto Github actions"
```
Ahora, sin hacer `push`, creo el repositorio en github:  
https://github.com/vrcastro1975/github-actions.git  
Y lo vinculo con mi local:  
```bash
git remote add origin https://github.com/tu-usuario/github-actions.git
```
Y, ahora sí, subo los cambios al repositorio remoto:  
```bash
git branch -M main
git push -u origin main
```
Ahora vamos a probar el workflow. Para ello, vamos a crear una rama de trabajo `test-branch`:  
```bash
git checkout -b test-branch
```
Vamos a hacer un ligero cambio en un fichero para poder pushear cambios, ya que, si no hay cambios, no nos va a dejar hacer push. Posteriormente, haremos una pull request:  
```bash
echo "// Test change" >> .start-code/hangman-front/test.js
git add .
git commit -m "Testeando cambio para hacer pull request"
git push origin test-branch
```
Ahora me voy al repositorio en Github y voy a crear una pull request: `Pull requests / Compare and pull request / Create pull request`.  
Ahora vamos a `Actions` y vemos cómo se ha iniciado el workflow.  
El workflow acaba mal y se señala con una "X". Vamos a revisar qué ha salido mal clicando sobre el workflow.  
Arreglo el problema en código (borro un fichero que he creado y que no debería estar ahí), git add ., git commit, y git push. Luego voy a Github y le doy a rerun a todos los jobs del workflow, pero me vuelve a dar el mismo fallo (run unit tests, línea 39 (línea 16 del fichero .start-code/hangman-front/src/components/start-game.spec.tsx, que espera un elemento y le llegan dos)). Lo arreglo y subo los cambios.  
