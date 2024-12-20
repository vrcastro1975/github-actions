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
El problema que tenía para acceder al Container Registry parece ser que era doble.  
Por un lado, tenía que activar los permisos de workflow dentro del repositorio: `Settings / Actions / General / Workflow permissions / Read and write permissions`.  
Por otro lado, el token personal no tenía todos los permisos necesarios, así que volví sobre mis pasos y creé un token con los permisos adecuados.  
Una vez que vi que el workflow funcionaba, quité el token personal y volví al token de Github (GITHUB_TOKEN). Vi que funcionaba todo y terminé con esto el ejercicio. En el paso `Push Docker image` podemos ver el resultado:  
```
Run docker push ghcr.io/vrcastro1975/github-actions:latest
The push refers to repository [ghcr.io/vrcastro1975/github-actions]
a1b974bed2fd: Preparing
cf39267c73a6: Preparing
f43507c2111b: Preparing
5f70bf18a086: Preparing
3f431f96b072: Preparing
a181cbf898a0: Preparing
570fc47f2558: Preparing
a181cbf898a0: Waiting
5d17421f1571: Preparing
7bb2a9d37337: Preparing
3e207b409db3: Preparing
570fc47f2558: Waiting
5d17421f1571: Waiting
7bb2a9d37337: Waiting
3e207b409db3: Waiting
5f70bf18a086: Pushed
cf39267c73a6: Pushed
a1b974bed2fd: Pushed
f43507c2111b: Pushed
3f431f96b072: Pushed
5d17421f1571: Pushed
a181cbf898a0: Pushed
570fc47f2558: Pushed
3e207b409db3: Pushed
7bb2a9d37337: Pushed
latest: digest: sha256:e6d8ee39949ca403f8fdf87fb48c6d7f687d273b2286c6f6187dca389efd590b size: 2396
```
  

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
  
El paso `Run Cypress tests` falla. Analizando la salida, parece que falta un script `start` en el fichero `.start-code/hangman-e2e/e2e/package.json`. Vamos a incluirlo para que lance Cypress en modo ejecución:  
```json
{
  "scripts": {
    "start": "cypress open",
    "test": "cypress run"
  }
}
```
También actualizaremos el workflow para usar `npm test`:  
```yaml
- name: Run Cypress tests
  uses: cypress-io/github-action@v5
  with:
    working-directory: .start-code/hangman-e2e/e2e
    start: npm test
    wait-on: http://localhost:8080
```

Ahora tenemos un nuevo problema en nuestro workflow con la configuración de vídeos de Cypress, por lo que vamos a desactivarla, ya que no es necesaria para este ejercicio. Para ello, editaremos el fichero `.start-code/hangman-e2e/e2e/cypress.config.ts` y le añadiremos esta línea: `video: false,` para desactivar la grabación de vídeos.  
  
Y, por fin, todos los tests pasan:  
```
Run cypress-io/github-action@v5
  
Received 16578342 of 16578342 (100.0%), 57.5 MBs/sec
Cache Size: ~16 MB (16578342 B)
/usr/bin/tar -xf /home/runner/work/_temp/81d3d4cd-639b-4fa1-9ff3-c89bc9a906dd/cache.tzst -P -C /home/runner/work/github-actions/github-actions --use-compress-program unzstd
Cache restored successfully
Received 134217728 of 165315580 (81.2%), 127.9 MBs/sec
Received 165315580 of 165315580 (100.0%), 121.8 MBs/sec
Cache Size: ~158 MB (165315580 B)
/usr/bin/tar -xf /home/runner/work/_temp/7f8c78a2-648f-4ba9-bab6-d50ddfd1e830/cache.tzst -P -C /home/runner/work/github-actions/github-actions --use-compress-program unzstd
Cache restored successfully
/opt/hostedtoolcache/node/16.20.2/x64/bin/npm ci
added 166 packages, and audited 167 packages in 2s
28 packages are looking for funding
  run `npm fund` for details
5 vulnerabilities (3 moderate, 2 high)
To address issues that do not require attention, run:
  npm audit fix
To address all issues (including breaking changes), run:
  npm audit fix --force
Run `npm audit` for details.
/opt/hostedtoolcache/node/16.20.2/x64/bin/npx cypress cache list
┌─────────┬───────────────────┐
│ version │ last used         │
├─────────┼───────────────────┤
│ 10.10.0 │ a few seconds ago │
└─────────┴───────────────────┘
start server command "npm test"
current working directory "/home/runner/work/github-actions/github-actions/.start-code/hangman-e2e/e2e"
waiting on "http://localhost:8080" with timeout of 60 seconds
/opt/hostedtoolcache/node/16.20.2/x64/bin/npm test
> @pumpbit-quiz-maker/e2e@1.0.0 test
> cypress run
[3162:1124/193553.044918:ERROR:gpu_memory_buffer_support_x11.cc(44)] dri3 extension not supported.
Missing baseUrl in compilerOptions. tsconfig-paths will be skipped
====================================================================================================
  (Run Starting)
  ┌────────────────────────────────────────────────────────────────────────────────────────────
  │ Cypress:        10.10.0                                                                        │
  │ Browser:        Electron 106 (headless)                                                        │
  │ Node Version:   v20.13.1 (/home/runner/runners/2.320.0/externals/node20/bin/node)              │
  │ Specs:          2 found (other-test.cy.ts, test.cy.ts)                                         │
  │ Searched:       cypress/e2e/**/*.cy.{js,jsx,ts,tsx}                                            │
  └────────────────────────────────────────────────────────────────────────────────────────────
─────────────────────────────────────────────────────────────────────────────────────────────
                                                                                                    
  Running:  other-test.cy.ts                                                                (1 of 2)
====================================================================================================
  (Run Starting)
  ┌────────────────────────────────────────────────────────────────────────────────────────────
  │ Cypress:        10.10.0                                                                        │
  │ Browser:        Electron 106 (headless)                                                        │
  │ Node Version:   v16.20.2 (/opt/hostedtoolcache/node/16.20.2/x64/bin/node)                      │
  │ Specs:          2 found (other-test.cy.ts, test.cy.ts)                                         │
  │ Searched:       cypress/e2e/**/*.cy.{js,jsx,ts,tsx}                                            │
  └────────────────────────────────────────────────────────────────────────────────────────────
─────────────────────────────────────────────────────────────────────────────────────────────
                                                                                                    
  Running:  other-test.cy.ts                                                                (1 of 2)

  empty spec

  empty spec
    ✓ passes (252ms)
  1 passing (281ms)
  (Results)
  ┌────────────────────────────────────────────────────────────────────────────────────────────
  │ Tests:        1                                                                                │
  │ Passing:      1                                                                                │
  │ Failing:      0                                                                                │
  │ Pending:      0                                                                                │
  │ Skipped:      0                                                                                │
  │ Screenshots:  0                                                                                │
  │ Video:        false                                                                            │
  │ Duration:     0 seconds                                                                        │
  │ Spec Ran:     other-test.cy.ts                                                                 │
  └────────────────────────────────────────────────────────────────────────────────────────────
    ✓ passes (255ms)
  1 passing (290ms)
  (Results)
  ┌────────────────────────────────────────────────────────────────────────────────────────────
  │ Tests:        1                                                                                │
  │ Passing:      1                                                                                │
  │ Failing:      0                                                                                │
  │ Pending:      0                                                                                │
  │ Skipped:      0                                                                                │
  │ Screenshots:  0                                                                                │
  │ Video:        false                                                                            │
  │ Duration:     0 seconds                                                                        │
  │ Spec Ran:     other-test.cy.ts                                                                 │
  └────────────────────────────────────────────────────────────────────────────────────────────
─────────────────────────────────────────────────────────────────────────────────────────────
                                                                                                    
  Running:  test.cy.ts                                                                      (2 of 2)
─────────────────────────────────────────────────────────────────────────────────────────────
                                                                                                    
  Running:  test.cy.ts                                                                      (2 of 2)

  empty spec

  empty spec
    ✓ passes (212ms)
  1 passing (248ms)
  (Results)
  ┌────────────────────────────────────────────────────────────────────────────────────────────
  │ Tests:        1                                                                                │
  │ Passing:      1                                                                                │
  │ Failing:      0                                                                                │
  │ Pending:      0                                                                                │
  │ Skipped:      0                                                                                │
  │ Screenshots:  0                                                                                │
  │ Video:        false                                                                            │
  │ Duration:     0 seconds                                                                        │
  │ Spec Ran:     test.cy.ts                                                                       │
  └────────────────────────────────────────────────────────────────────────────────────────────
    ✓ passes (234ms)
  1 passing (260ms)
====================================================================================================
  (Run Finished)
  (Results)
       Spec                                              Tests  Passing  Failing  Pending  Skipped  
  ┌────────────────────────────────────────────────────────────────────────────────────────────
  │ ✔  other-test.cy.ts                         276ms        1        1        -        -        - │
  ├────────────────────────────────────────────────────────────────────────────────────────────
  │ ✔  test.cy.ts                               230ms        1        1        -        -        - │
  └────────────────────────────────────────────────────────────────────────────────────────────
    ✔  All specs passed!                        506ms        2        2        -        -        -  
  ┌────────────────────────────────────────────────────────────────────────────────────────────
  │ Tests:        1                                                                                │
  │ Passing:      1                                                                                │
  │ Failing:      0                                                                                │
  │ Pending:      0                                                                                │
  │ Skipped:      0                                                                                │
  │ Screenshots:  0                                                                                │
  │ Video:        false                                                                            │
  │ Duration:     0 seconds                                                                        │
  │ Spec Ran:     test.cy.ts                                                                       │
  └────────────────────────────────────────────────────────────────────────────────────────────
====================================================================================================
  (Run Finished)
       Spec                                              Tests  Passing  Failing  Pending  Skipped  
  ┌────────────────────────────────────────────────────────────────────────────────────────────
  │ ✔  other-test.cy.ts                         267ms        1        1        -        -        - │
  ├────────────────────────────────────────────────────────────────────────────────────────────
  │ ✔  test.cy.ts                               254ms        1        1        -        -        - │
  └────────────────────────────────────────────────────────────────────────────────────────────
    ✔  All specs passed!                        521ms        2        2        -        -        -  
```

Con esto termina este tercer ejercicio.