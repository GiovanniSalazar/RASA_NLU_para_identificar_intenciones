# RASA NLU para la identificación de intenciones | ES

Citaré la pagina web oficial "Rasa NLU is an open-source natural language processing tool for intent classification, response retrieval and entity extraction in chatbots. For example, taking a sentence like".Este git solo se enfocará en la extracción de la intención vía API HTTP al cual se le pasará una petición en json con la expresión . 

### Probado con :

* Ubuntu 18.04
* Python 3
* pip3

### Instalación de RASA :

```sh
$ apt-get update
$ pip3 install rasa-x --extra-index-url https://pypi.rasa.com/simple
```
Creamos las carpetas que contendran las carpetas y entrenamientos ...
```sh
$ cd /home/
$ mkdir SimpleRasa
$ cd SimpleRasa/
$ mkdir data
$ mkdir models
$ touch config.yml
$ touch data/nlu.md
```
Editamos los archivos de configuración de pipeline a elegir y las intenciones 

```sh
$ vim config.yml
#configuration for Rasa NLU.
#https://rasa.com/docs/rasa/nlu/components/
language: en
pipeline: supervised_embeddings

# Configuration for Rasa Core.
# https://rasa.com/docs/rasa/core/policies/
policies:
   - name: MemoizationPolicy
   - name: KerasPolicy
   - name: MappingPolicy
```
```sh
$ vim data/nlu.md
## intent:greet
- hey
- hello
- hi
- good morning
- good evening
- hey there

## intent:goodbye
- bye
- goodbye
- see you around
- see you later

## intent:affirm
- yes
- indeed
- of course
- that sounds good
- correct
```

Realizamos el entrenamiento ...(estar ubicado en la carpeta /home/SimpleRasa) , esto generará un archivo .tar.gz en la carpeta models.
```sh
$  rasa train nlu -c config.yml --out models
```
Y procedemos a correr el modelo habilitando el API : se observa que se especifica el modelo generado en el archivo tar.gz , adicional con estamos habilitando el API para que corra con por el puerto 5000
```sh
$  rasa run -m models/nlu-20190922-035116.tar.gz  --enable-api --log-file out.log -p 5000
```
Una vez que se encuentre corriendo el API , procedemos a mandar la petición con un texto de ejemplo :
```sh
$  curl localhost:5000/model/parse -d '{"text":"hello how are you"}'
```
o
```sh
$  curl mi_ip:5000/model/parse -d '{"text":"hello how are you"}'
```
El resultado es :
```sh
{
  "intent": {
    "name": "greet",
    "confidence": 0.812533021
  },
  "entities": [
    
  ],
  "intent_ranking": [
    {
      "name": "greet",
      "confidence": 0.812533021
    },
    {
      "name": "goodbye",
      "confidence": 0.1108943373
    },
    {
      "name": "mood_great",
      "confidence": 0.0
    },
    {
      "name": "affirm",
      "confidence": 0.0
    },
    {
      "name": "deny",
      "confidence": 0.0
    }
  ],
  "text": "hello how are you"
}
```
