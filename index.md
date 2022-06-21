## Présentation de l'application Star Wars Story
Une skill Alexa faisant appel à l'API de swapi.dev

Pour commencer, j'ai décider de créer une skill sur star wars donnant un accès au résumer
et aux informations importantes sur chaque épisodes.



### Code Source

Le code présenté en Node.JS est issue de l'IDE présent sur l'outil de développement Amazon.

```markdown
const Alexa = require('ask-sdk-core');
const https = require('https');

const httpGet = () => {
  return new Promise(((resolve, reject) => {
    var options = {
        host: 'swapi.dev',
        port: 443,
        path: '/api/films/1/',
        method: 'GET',
    };

    const request = https.request(options, (response) => {
      response.setEncoding('utf8');
      let returnData = '';

      response.on('data', (chunk) => {
        returnData += chunk;
      });

      response.on('end', () => {
        resolve(JSON.parse(returnData));
      });

      response.on('error', (error) => {
        reject(error);
      });
    });
    request.end();
  }));
}

const LaunchRequestHandler = {
    canHandle(handlerInput) {
        return Alexa.getRequestType(handlerInput.requestEnvelope) === 'LaunchRequest';
    },
    handle(handlerInput) {
        const speakOutput = 'Bonjour jeune padawan, que puis-je faire pour toi ?';

        return handlerInput.responseBuilder
            .speak(speakOutput)
            .reprompt(speakOutput)
            .getResponse();
    }
};
const GetFactIntentHandler = {
    canHandle(handlerInput) {
        return Alexa.getRequestType(handlerInput.requestEnvelope) === 'IntentRequest'
            && Alexa.getIntentName(handlerInput.requestEnvelope) === 'GetFactIntent';
    },
    async handle(handlerInput) {
        // const speakOutput = 'I will tell a fact!';
        const response = await httpGet();
        const speakOutput = "Le titre " + response.title + " a été réaliser par " + response.director + response.opening_crawl;
        return handlerInput.responseBuilder
            .speak(speakOutput)
            //.reprompt('add a reprompt if you want to keep the session open for the user to respond')
            .getResponse();
    }
};
```

```markdown
exports.handler = Alexa.SkillBuilders.custom()
    .addRequestHandlers(
        LaunchRequestHandler,
        HelloWorldIntentHandler,
        GetFactIntentHandler,
        HelpIntentHandler,
        CancelAndStopIntentHandler,
        FallbackIntentHandler,
        SessionEndedRequestHandler,
        IntentReflectorHandler)
    .addErrorHandlers(
        ErrorHandler)
    .withCustomUserAgent('sample/hello-world/v1.2')
    .lambda();
```

Je fais dans un premier temps un appele à l'API en indiquant :

    - l'adresse "host" : correspond à l'adresse de l'API
    
    - port :  ici 443 pour utiliser le protocol TCP
    
    - path : indique le chemin d'accès aux données JSON de l'API
    
    - GET : permet de récupérer les données du serveur depuis le navigateur
    
Dans un deuxième temps, je lance la requête de "bienvenue" -> "Bonjour jeune padawan, que puis-je faire pour toi ?"

Puis je construit ma réponse avec les données récupérer ! 

Vidéo de démonstration : 
[![Alt text for your video](http://img.youtube.com/vi/T-D1KVIuvjA/0.jpg)]([http://www.youtube.com/watch?v=T-D1KVIuvjA](https://www.canva.com/design/DAFEMHhA_EU/bbbTyZOmM8b4pNp4XLl_eA/watch))



