---
layout: post
title:  "Web Speech API"
categories: javascript
---

The Web Speech API consists of two parts: SpeechSynthesis (or TTS) and SpeechRecognition. Chrome browser has the best implementation of both API. Therefore, the code sampels given below are on Chrome.

### Speech Synthesis

SpeechSynthesis or TTS is text to speech, namely, the web browser speaks out a text message. Currently, Chrome is able to speak in a variety of languages. By specifying the voice property, we are able to name the language. A smart way is to let the browser to decide which language to speak in by itself. Browser is able to do the speculation according to the language settings.

```javascript
// Get the browser's language setting
var acceptLanguage = $window.navigator.language || $window.navigator.userLanguage;

var selectedVoice = null;
window.speechSynthesis.onvoiceschanged = function() {
    var voices = window.speechSynthesis.getVoices();
    if (!!voices) {
        for (var i = 0; i < voices.length; i++) {
            var voice = voices[i];
            if (vocie.lang !== '' && acceptLanguage.indexOf(voice.lang) >= 0) {
               selectedVoice = voice;
               break;
            }
        }
    }
};
```

It's important to note that right after document is loaded, window.speechSynthesis.getVoices() is empty. The browser needs extra time to load the language voices. It's an asynchronous process. That's why we have to listen to onvoiceschanged event.

Once the voice is selected, the browser is able to speak in that language.

```javascript
function speak(text, callback, selectedVoice) {
    var msg,
        self = this,
        ended = false;

    msg = new SpeechSynthesisUtterance();
    msg.volume = 1; // 0 to 1
    msg.rate = 1; // 0.1 to 10
    msg.pitch = 1; //0 to 2
    msg.text = text;
    if (!!selectedVoice) {
       msg.voice = selectedVoice;
    }

    var onend = function(e) {
        if (ended) {
            return;
        }

        ended = true;
        if (typeof callback === 'function') {
            callback();
        }
    };
    msg.onend = onend;

    window.speechSynthesis.speak(msg);

    window.setTimeout(function(){
        if (!ended) {
            onend();
        }
    },10000);
}
```

The onend event is quite unstable. From times to times, it's not triggered. Hence, I've added a timeout timer to guarantee that the ending callback handler is invoked.

### Speech Recognition

SpeechRecognition takes settings like lang, which sets the language to recognize; continuous, whether single or continuous results are returned; and interimResults, whether intermediate results are returned.


```javascript
this.startSpeechRecognition = function(lang,
                                       continuous,
                                       interimResults,
                                       resultCallback,
                                       endCallback) {
    if (!window.webkitSpeechRecognition) {
        return;
    };

    var speechRecognizer = new window.webkitSpeechRecognition();
    speechRecognizer.lang = lang;
    speechRecognizer.continuous = continuous;
    speechRecognizer.interimResults = interimResults;

    var script = '';
    speechRecognizer.onresult = function(event) {
        script = '';

        for (var i = event.resultIndex; i < event.results.length; ++i) {
            if (event.results[i].isFinal) {
               script += event.results[i][0].transcript;
            }
        }

        if (typeof resultCallback == "function") {
            resultCallback(script);
        }
    };
    speechRecognizer.onend = function(event) {
        if (typeof endCallback == "function") {
            endCallback(script);
        }
    };

    speechRecognizer.start();
};
```