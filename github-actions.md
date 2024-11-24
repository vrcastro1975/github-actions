# **Tarea Github Actions**
## Por Víctor Castro Gálvez

## Primer ejercicio:  
El primer ejercicio consiste en crear un workflow CI para el proyecto de frontend.  
Como, al igual que en el resto de las tareas de este módulo de CI/CD, voy a seguir usando el directorio `exercises` para presentar los trabajos, lo primero que voy a hacer es copiar todo el proyecto en ese subdirectorio, creando un subdirectorio que se va a llamar: `exercises/github-actions`.  
Una vez creada la estructura del directorio de trabajo, en el raíz creo la carpeta .github/workflows y me creo el workflow de ci, al que voy a llamar `ci-hangman-front.yml`. El workflow se debe disparar cuando haya cambios en hangman-front y se cree una nueva pull request.  
Ahora añado los pasos del workflow. Como el proyecto usa npm, añado los pasos para instalar las dependencias, añadir el build del proyecto y los tests unitarios. El resultado final puede verse en el fichero ci-hangman-front.yml.  
