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
Sospecho que el problema se debe a un fichero que he creado por error. Borro el fichero, hago `git add .`, `git commit`, y `git push`. Luego voy a Github y le doy a rerun a todos los jobs del workflow, pero me vuelve a dar el mismo fallo (run unit tests, línea 39 (línea 16 del fichero .start-code/hangman-front/src/components/start-game.spec.tsx, que espera un elemento y le llegan dos)). Lo arreglo y subo los cambios.  
Ahora ya funciona correctamente el workflow, pues ha pasado correctamente los tests unitarios, y puedo mergear la rama de trabajo con la rama main. Para ello, clico en `Merge pull request` y luego `Confirm merge`. Como ya no voy a necesitar más mi rama de trabajo, la borro seleccionando esa opción (en local sí va a seguir existiendo, y tendremos que borrarla también).  
Vuelvo ahora a mi local y me cambio a la rama `main`. Borro la rama de trabajo con un `git branch -D test-branch`. Luego hago un `git fetch origin` y un `git pull origin main` y ya estoy actualizado en local.  
Con esto acaba el primer ejercicio.  
  

## Segundo ejercicio:  
El segundo ejercicio consiste en crear un workflow CD para el proyecto de frontend. Este nuevo workflow debe dispararse manualmente y hacer lo siguiente:  
* Crear una nueva imagen de Docker
* Publicar dicha imagen en el [container registry de GitHub](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)  
  
Para ello, vamos a crear el nuevo workflow en la carpeta .github/workflows/ y con el nombre `cd-hangman-front.yml`.  
Al poner `workfow_dispatch`, hacemos que el workflow se dispare desde la interfaz de Github (pestaña Actions). Para acceder al Container Registry de Github, usaremos el token GITHUB_TOKEN. Para crear la etiqueta de la imagen, usaremos el nombre del propietario del repositorio (repository_owner). Con docker push se sube la imagen al registro de Github. Podemos ver todo esto en el fichero cd-hangman-front.yml.  
Ahora volvemos a la raíz del repositorio y subimos los cambios:  
```bash
git add .
git commit -m "Añadido el workflow CD para hangman-front"
git push origin main
```
Ahora nos vamos a Github y, en la sección "Actions", clicamos sobre el nuevo workflow `CD Hangman Front` y, a continuación, clicamos en `Run workflow`. Seleccionamos la rama `main` y `Run workflow`.  
Me da un error `denied: installation not allowed to Create organization package`.  
Buscando en la [documentación oficial] (https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry), me aparece que, para autenticarse con el Container Registry hay que hacerlo con personal access token.  
Por tanto, vamos a probar con un `Personal Access Token (PAT)`. Por tanto, en el panel izquierdo de Githib nos vamos a `<> Developer settings / Personal access tokens / Tokens (classic)`. Una vez ahí, seleccionamos `Generate new token (classic)`.  
Marcamos al menos estas ocpiones:  
*write:packages
*read:packages (se marcará automáticamente al seleccionar el anterior)
*delete:packages
En `Note` escribimos por ejemplo `github-actions-exercise` y las demás opciones las dejamos tal cual, y clicamos en `Generate token`. Copiamos el token en un lugar seguro. Yo lo pongo aquí, porque cuando acabe el ejercicio lo borraré: `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`.  
Ahora vamos a añadir el token como un secreto en el repositorio:  
Vamos al repositorio en Github. Nos vamos a `Settings / Secrets and variables / Actions / New repository secret`:  
*Name = GHCR_PAT
*Secret = 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
A continuación, clicamos en `Add secret`.
Ahora queda modificar el fichero `cd-hangman-frontend.yml` para que use el PAT en lugar del GITHUB_TOKEN, comitear y subir los cambios a Github. El cambio consistirá en cambiar la línea `password: ${{ secrets.GITHUB_TOKEN }}` por `password: ${{ secrets.GHCR_PAT }}`.  
Ahora grabamos los cambios los subimos a Github.  
```bash
git add .
git commit -m "Cambiamos el fichero cd-hangman-front-yml para usar GHCR_PAT en lugar de GITHUB_TOKEN"
git push
```
(Nota: Al haber un token en código, no me deja hacer push. Como se trata de una prueba, voy a permitir el push en Github. Para ello, voy a https://github.com/vrcastro1975/github-actions/security/secret-scanning/unblock-secret y marco `It's used in tests / Allow me to expose this secret`).  
Ahora vamos a verificar el workflow en Github. Vamos a la pestaña `Actions` en el repositorio, buscamos de nuevo el workflow `CD Hangman Front` y seleccionamos `Run workflow / Rama main / Run workflow`. (Y nos vuelve a dar error).  
Si intento loguearme en el container registry también me da error:  
```bash
docker login ghcr.io -u vrcastro1975 -p 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
```
(PIDO AYUDA A JOAQUIN POR SLACK. SÓLO FALTARÍA ARREGLAR EL POR QUÉ NO PUEDO ACCEDER AL CONTAINER REGISTRY)
  

## Tercer ejercicio (Opcional 1):
En este tercer ejercicio hay que crear un workflow que ejecute los tests e2e que se encuentran en este enlance: 03-github-actions/.start-code/hangman-e2e/e2e. Vamos a optar por usar [Cypress action](https://github.com/cypress-io/github-action) para ejecutar los tests.Para ejecutar los tests e2e:
* Tanto el front como la api se deben estar ejecutando
```bash
docker run -d -p 3001:3000 hangman-api
docker run -d -p 8080:8080 -e API_URL=http://localhost:3001 hangman-front
```
* Los tests se ejecutan desde el directorio hangman-e2e/e2e haciendo uso del comando npm run open
```bash
cd hangman-e2e/e2e
npm run open
```

Para resolver el ejercicio vamos a crear, dentro del directorio `.github/workflows/`, el fichero `e2e-tests.yml`.  
En dicho fichero, hemos hecho uso de `workflow_dispatch`, al igual que en el ejercicio anterior, para poder lanzar los workflows directamente desde la interfaz de Github. Hemos preparado el entorno, configurando Node.js para que Cypress pueda instalarse y ejecutarse correctamente (actions/setup-node@v3). También hemos ejecutado los contenedores que vamos a necesitar (front y api). Por último, hemos instalado las dependencias para los tests e2e (npm install) y hemos ejecutado los tests e2e con Cypress (cypress-io/github-action@v5).  
  
Ahora subimos el workflow al repositorio remoto:  
```bash
git add .
git commit -m "Añadimos E2E tests workflow con Cypress"
git push origin main
```
Y ahora probamos el workflow. Para ello, nos vamos a la pestaña `Actions` en nuestro repositorio de Github, buscamos el workflow `E2E Tests` y lo ejecutamos con `Run workflow`.  
Por último, revisamos los resultados en los logs del workflow.  
Con esto termina este tercer ejercicio.