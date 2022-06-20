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

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/pierre370/StarWars_Story/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
