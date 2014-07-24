## Yii Module to handle translations

This extension collects all untranslated messages in the page and then you can add a button to translate, translate automatically using google translate, check for missing translations, edit translations and more.

Basic usage:


```php
echo Yii::t('app','Some text').' - '.Yii::t('app','some other text');
Yii::app()->translate->editLink('Translate');
```


## Setup

In your config :  

```php
<?php
return array(
    'preload'=>array('translate'),//either here or somewhere in the beginning
    //set up and import the module
    'modules'=>array(
        'translate'
    ),
    'import'=>array(
        'application.modules.translate.TranslateModule'
    ),
    'components'=>array(
        //define the class and its missingTranslation event
        'messages'=>array(
            'class'=>'CDbMessageSource',
            'onMissingTranslation' => array('TranslateModule', 'missingTranslation'),
        ),
        'translate'=>array(//if you name your component something else change TranslateModule
            'class'=>'translate.components.MPTranslate',
            //any avaliable options here
            'acceptedLanguages'=>array(
                  'en'=>'English',
                  'pt'=>'Portugues',
                  'es'=>'Español'
            ),
        ),
    )
);
```

Do not forget to create the tables like [described here](http://www.yiiframework.com/doc/api/1.1/CDbMessageSource "")  
#### the id of message source must be auto-incremental

the name of the tables can be modified  

```sql
CREATE TABLE SourceMessage
(
    id INTEGER PRIMARY KEY AUTO_INCREMENT,
    category VARCHAR(32),
    message TEXT
);
CREATE TABLE Message
(
    id INTEGER,
    language VARCHAR(16),
    translation TEXT,
    PRIMARY KEY (id, language),
    CONSTRAINT FK_Message_SourceMessage FOREIGN KEY (id)
         REFERENCES SourceMessage (id) ON DELETE CASCADE ON UPDATE RESTRICT
);

```



## How to use

Extract the file to protected/modules  

somewhere in your application define the language like the following:  

```php
//shortcut
$translate=Yii::app()->translate;
//in your layout add
echo $translate->dropdown();
//adn this
if($translate->hasMessages()){
  //generates a to the page where you translate the missing translations found in this page
  echo $translate->translateLink('Translate');
  //or a dialog
  echo $translate->translateDialogLink('Translate','Translate page title');
}
//link to the page where you edit the translations
echo $translate->editLink('Edit translations page');
//link to the page where you check for all unstranslated messages of the system
echo $translate->missingLink('Missing translations page');
```

## Options avaliable

#### string $defaultLanguage
defaults language to use if none is set.  

if null, it will receive Yii target language
#### array $dialogOptions
options of the dialog

#### string $googleApiKey
your google translate api key.  
set this if you wish to use googles translate service to translate the messages  

#### array $acceptedLanguages
an array containing the languages accepted by your application.  
if not set, it will receive an array containing the target language and the source language  

#### boolean $autoTranslate
wheter to auto translate the missing messages found on the page. Defaults to false.  
Needs google api key to set  

#### boolean $autoSetLanguage
wheter to automatically set the language on the initialization of the component. Defaults to true.  

##Methods avaliable

for the translate components  
use it like this:  
``` php
Yii::app()->translate->someMethod($someArg);
```

#### string getLanguage()
Returns the language in use.  
The language is determined by many variables: session, post, get, header in this order.  
It will filter the language by using accepted languages.  
If is set "en_US" and it is not avaliable, then it will check if "en" is avaliable.  

#### string setLanguage($language=null)  
If $language is null then it will use getLanguage to determine it.  
It doesn't check if the language is in the accepted languages.  
Returns the language setted.  

#### boolean hasMessage()
Return wheter there are messages to be translated in this page  

#### string dropdown()
Returns a dropdown containing all accepted languages with an onchange event that will change the application's language.  

#### string googleTranslate($message,$targetLanguage=null,$sourceLanguage=null)
Translate some message from $sourceLanguage to $targetLanguage using google translate api. 
Option GoogleApiKey must be defined to use this service.  

#### array getGoogleAcceptedLanguages($targetLanguage=null)
returns an array containing all languages accepted by google translate. 
Option googleApiKey must be defined to use this service.  

#### string translateLink($label,$type='link')
Generates a link or button to the page where you translate the missing translations found in this page  

#### string translateDialogLink($label,$title=null,$type='link')
Generates a link or button that generates a dialog to the page where you translate the missing translations found in this page.  

#### string editLink($label,$type='link')
Creates a link to the page where you edit the translations.  

#### string missingLink($label,$type='link')
Creates a link to the page where you check all missing translations  

_type for any link can be `button` or `link`_  

if you are running php 5.3 you can use any method like this  
```php
MPTranslate::someMethod($someArg);  
```

### For TranslateModule :

#### MPTranslate translator()
returns the translate component  

#### string t($message,$params=array())
used internally for the module to translate its content  

#### string $translateComponentId
the id of the translate component. Defaults to 'translate'   


##Version

0.2.4: 24/07/2014  

+ message and categories are now case-insensitive  
+ fix page count   


0.2.3: 18/08/2011  

+ LANGUAGE_KEY is now a constant named ID  
+ Google errors are now logged instead of beeing treated as an exception   

0.2.2: 23/05/2011  

+ Fixed pagination in dialog and textarea size   

0.2.1: 20/05/2011  

+ Fixed some minor bugs  

0.2: 18/05/2011  

+ complete rewrite of the code to allow easier customization  
+ added google translate support (thanks to Toshi)  
+ added defaultLanguage (thanks to duna)  
+ fixed a couple of minor bugs  
+ added a lof of new funcionalities  

0.1: 10/05/2011  

+ first release


##Resources

+ [Get a google API key](https://code.google.com/apis/console/?api=translate "Get a google api key")
+ [CDbMessageSource](http://www.yiiframework.com/doc/api/1.1/CDbMessageSource "CDbMessageSource")
+ [Forum support](http://www.yiiframework.com/forum/index.php?/topic/19302-translate-module/ "Forum support")



## Notes

#### This module will slow down your application, use it for development only
