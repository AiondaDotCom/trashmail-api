# Schnittstellenbeschreibung

In diesem Dokument werden die Backend-Endpunkte und deren Verhalten dokumentiert. Diese Beschreibung dient anschließend als Grundlage für die Entwicklung der iOS- und macOS-App und sollte aus diesem Grund möglichst fehlerfrei sein. Bevor Änderungen an diesem Dokument gepusht werden, muss das Xcode-Projekt nach der Verwendung der Endpunkte kontrolliert und ggf. angepasst werden!

Die hier bereitgestellten Informationen basieren auf:

1. dem Quellcode des TrashMail.com-Backends
2. dem untersuchten TrashMail-com-Frontend
3. bestehenden TrashMail-Extensions
4. und dem untersuchten Production-Backend mithilfe von [Postman](https://www.postman.com)

Um die Funktionsweise bestmöglich zu demonstrieren, wird der Testnutzer "User" in den nachfolgenden Beispielen verwendet. Die Requests und Responses werden nicht generisch gehalten, sondern zeigen die Daten von "User".

Der Aufbau dieses Dokuments folgt einer klaren Struktur. Jeder Endpunkt wird mit...

1. seiner Adresse (URL) eingeführt
2. mit einer kurzen Beschreibung der Funktionalität definiert
3. den erwarteten Eingabeparametern (z.B. der Aufbau des JSON) spezifiziert
4. den erwarteten Ausgabeparametern (z.B. der Aufbau des JSON) spezifiziert
5. und mit möglichen Hinweisen bzw. Besonderheiten konkretisiert

Jeder Endpunkt folgt dem gleichen Aufbau:  

```text
https://trashmail.com/?
api=1 &   // Aktiviert API-Funktionalität
lang=x &  // en, de, fr
cmd=x     // Je nach aufzurufende Funktion

// Parameter werden nicht wie bei REST in der URL angegeben, sondern im HTTP-Body.
```

Fehlermeldungen haben immer den gleichen Aufbau:

```text
{
   "success":false,
   "error_code":x,    // Fehlercode > 0
   "msg":"x"          // Beschreibt den Fehler (bzw. den Code)
}
```

Erfolgsmeldungen haben immer den gleichen Aufbau:

```text
{
   "success":true,
   "error_code":0,    // Code 0 entspricht Erfolg
   "msg":{...}        // Je nach Endpunkt Antwort zur Anfrage
}
```

**Die JSON-Eigenschaft "msg" wird je nach gesetzem HTTP-GET(lang) in einer anderen Sprache zurückgegeben!**

## Verfügbare CMD's

### Benutzerdaten 

* [login](#login)
* [add\_real\_email](#add\_real\_email)
* [set\_default\_email](#set\_default\_email)
* [set\_failover\_email](#set\_failover\_email)
* [del\_real\_email](#del\_real\_email)
* [add\_domain](#add\_domain)
* [status\_domains](#status\_domains)
* [del\_domain](#del\_domain)
* [list\_domains](#list\_domains)
* [list\_real\_emails](#list\_real\_emails)
* [logout](#logout)
* [del\_account](#del\_account)
* [register\_account](#register\_account)
* [reset\_password](#reset\_password)
* [change\_password](#change\_password)
* [set\_newsletter](#set\_newsletter)
* [set\_status](#set\_status)
* [send\_status](#send\_status)
* [download\_dea\_csv](#download\_dea\_csv)
* [save\_settings](#save\_settings)
* [read\_user\_data](#read\_user\_data)


### Alle TrashMails betreffend

* [read\_dea](#read\_dea)
* [read\_dea (Suche)](#read\_dea\(Suche\))
* [set\_rewrite\_from](#set\_rewrite\_from)
* [set\_rewrite\_to](#set\_rewrite\_to)
* [set\_log\_emails](#set\_log\_emails)
* [set\_subject\_prefix](#set\_subject\_prefix)
* [set\_sim\_ml](#set\_sim\_ml)


### Jeweils für eine TrashMail 

* [create\_dea](#create\_dea)
* [update\_dea](#update\_dea)
* [destroy\_dea](#destroy\_dea)
* [read\_logging](#read\_logging)
* [read\_logging\_raw\_data](#read\_logging\_raw\_data)
* [read\_logging\_events](#read\_logging\_events)
* [del\_logging](#del\_logging)
* [send\_logging\_emails](#send\_logging\_emails)
* [load\_cs\_messages](#load\_cs\_messages)
* [save\_cs\_messages](#save\_cs\_messages)
* [read\_whitelist](#read\_whitelist)
* [read\_whitelist (Suche)](#read\_whitelist\(Suche\))
* [whitelist\_add](#whitelist\_add)
* [whitelist\_delete](#whitelist\_delete)
* [add\_blacklist](#add\_blacklist)
* [del\_blacklist](#del\_blacklist)
* [read\_blacklist](#read\_blacklist)
* [read\_blacklist (Suche)](#read\_blacklist\(Suche\))
* [edit\_blacklist](#edit\_blacklist)
* [read\_queue](#read\_queue)
* [read\_queue (Suche)](#read\_queue\(Suche\))
* [queue\_delete\_msg](#queue\_delete\_msg)
* [queue\_delete\_all\_msg](#queue\_delete\_all\_msg)
* [queue\_accept\_msg](#queue\_accept\_msg)
* [send\_mail](#send\_mail)


## Erklärung der Endpunkte


***

Wenn die Anfrage als `x-www-form-urlencoded` versendet wird:  
Die POST-Parameter müssen [URL-kodiert](https://en.wikipedia.org/wiki/Percent-encoding) werden: `[1,2,3]` => `%5B1,2,3%5D`, `mail@domain.de` => `mail%40domain.de` usw. (Bei Swift: `addingPercentEncoding`).

***




### login

> <https://trashmail.com/?api=1&lang=en&cmd=login>

*Dieser Endpunkt ermöglicht den Login am TrashMail.com-Server, um anschließend auf Funktionalitäten des Backends zuzugreifen.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: fe-login-user, fe-login-pass
// Content-Type: application/json

{
   "fe-login-user":"User",          // String
   "fe-login-pass":"secret"         // String
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                                  // Boolean
   "error_code":0,                                                  // Integer
   "msg":{
      "url":"https:\/\/trashmail.com\/?lang=en&cmd=manager",        // String
      "real_email_list":{
         "trashmail_1@example.email":true                           // Boolean
      },
      "domain_name_list":[
         "0box.eu",                                                 // String...
         "contbay.com",
         "damnthespam.com",
         "kurzepost.de",
         "objectmail.com",
         "proxymail.eu",
         "rcpt.at",
         "trash-mail.at",
         "trashmail.at",
         "trashmail.com",
         "trashmail.io",
         "trashmail.me",
         "trashmail.net",
         "wegwerfmail.de",
         "wegwerfmail.net",
         "wegwerfmail.org"
      ],
      "session_id":"bk32m20sab2c6844dq968ouag3",                            // String
      "preferences": {                                                      // Array
               "DefaultCS": 0,                                              // Integer
               "DefaultDomain": "trashmail.com",                            // String
               "DefaultFwds": 100,                                          // Integer
               "DefaultLifeSpan": 7,                                        // Integer
               "DefaultMasq": 1,                                            // Integer
               "DefaultNotify": 1,                                          // Integer
               "DefaultRealEmailAddress": "name@domain.tld",                // String
               "DefaultSortDir": "DESC",                                    // String
               "DefaultSortField": "ctime_text",                            // String
               "notify-on-create-dea": true                                 // Boolean
      }
   }
}
```

**Hinweise:** 

* Der Aufruf dieses Endpunkts muss vor dem Aufruf anderer Endpunkte, die Funktionalitäten bereitstellen, erfolgen! Wenn sich daran nicht gehalten wird, wird als Fehlermeldung "Not logged in or expired session." zurückgegeben.

***





### logout

> <https://trashmail.com/?api=1&lang=en&cmd=logout>

*Dieser Endpunkt meldet den Benutzer am TrashMail.com-Server ab und setzt die serverseitige Session zurück. Nach dem Aufruf dieses Endpunktes muss erneut der Endpunkt `login` aufgerufen werden, um die Funktionalitäten des TrashMail.com-Servers zu nutzen.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:** 

* Keine

***






### list\_real\_emails

> <https://trashmail.com/?api=1&lang=en&cmd=list_real_emails>

*Gibt alle im Account hinterlegten realen E-Mail Adressen zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                          // Boolean
   "error_code":0,                                          // Integer
   "data": {
       "default_account_email":"test1@example.email",       // String
       "real_email_list":[
             "test1@example.email",                         // String...
             "test2@example.email",
             "test3@example.email"
       ],
       "failover_address":"fail@aionda.com"                 // String
   }
}
```

**Hinweise:** 

* Keine

***






### add\_real\_email

> <https://trashmail.com/?api=1&lang=en&cmd=add_real_email>

*Fügt dem Account eine echte E-Mail Adresse hinzu.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: email
// Content-Type: application/json

{
   "email":"trashmail_2@example.email"      // String
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:** 

* Wenn die Anfrage erfolgreich war, erscheint die E-Mail Adresse noch nicht unter den eigenen E-Mail Adressen. Eine erfolgreiche Anfrage löst lediglich eine Bestätigungsmail an die echte E-Mail Adresse aus und wird erst danach dem Konto zugewiesen.

***




### set\_default\_email

> <https://trashmail.com/?api=1&lang=en&cmd=set_default_email>

*Setzt eine der im Account hinterlegten echten E-Mail Adressen als Standard.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: email
// Content-Type: application/json

{
   "email":"trashmail_2@example.email"
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:** 

* Nicht existierende E-Mail Adressen werden mit der Meldung "This email address needs to be confirmed first. Please check your email inbox for a confirmation email." abgelehnt.

***




### set\_failover\_email

> <https://trashmail.com/?api=1&lang=en&cmd=set_failover_email>

*Setzt eine der im Account hinterlegten echten E-Mail Adressen als Failover.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: email
// Content-Type: application/json

{
   "email":"trashmail_2@example.email"
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:** 

* Eine Failover-Adresse ist optional. Aus diesem Grund kann sie auch zurückgesetzt werden. Dafür muss `"email":""` gesendet werden.

***




### del\_real\_email

> <https://trashmail.com/?api=1&lang=en&cmd=del_real_email>

*Entfernt eine der im Account hinterlegten echten E-Mail Adressen.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: email
// Content-Type: application/json

{
   "email":"trashmail_2@example.email"
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                  // Boolean
   "error_code":0,                                  // Integer
   "default_email":"trashmail_1@example.email"      // String
}
```

**Hinweise:** 

* Wenn die Standard-Mail Adresse entfernt wird, wird automatisch eine andere E-Mail Adresse als Standard gesetzt.

***





### add\_domain

> <https://trashmail.com/?api=1&lang=en&cmd=add_domain>

*Fügt dem Account eine eigene Domain für personalisierte E-Mail Adressen hinzu.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: domain
// Content-Type: application/json

{
	"domain": "example.email"      // String
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                                                      // Boolean
   "error_code":0,                                                                      // Integer
   "status":"Couldn't find 2 MX records, no MX entries found., missing SPF entry"       // String
}
```

**Hinweise:**

* Keine

***





### status\_domains

> <https://trashmail.com/?api=1&lang=en&cmd=status_domains>

*Gibt den aktuellen Status für alle im Account hinterlegten Domains zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,          // Boolean
   "error_code":0,          // Integer
   "statusList":[
      [
         "example.com",     // String...
         "Error: Couldn't find 2 MX records, no MX entries found., missing SPF entry"
      ],
      [
         "example.net",
         "Error: Couldn't find 2 MX records, no MX entries found., missing SPF entry"
      ]
   ]
}
```

**Hinweise:**

* Um an den Status einer einzelnen Domain zu kommen, muss die entsprechende Domain aus dem Array extrahiert werden. Es gibt keinen Endpunkt der explizit für eine Domain angefragt werden kann.

***






### del\_domain

> <https://trashmail.com/?api=1&lang=en&cmd=del_domain>

*Entfernt eine der im Account hinterlegten Domains.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: domain
// Content-Type: application/json

{
	"domain": "example.email"      // String
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Beim Löschen einer nicht vorhandenen Domain kommt keine Fehlerbeschreibung zurück: `{"success":false,"error_code":0}`.

***





### list\_domains

> <https://trashmail.com/?api=1&lang=en&cmd=list_domains>

*Gibt alle im Account hinterlegten Domains zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0,      // Integer
   "domainList":[
      "0box.eu",        // String...
      "contbay.com",
      "damnthespam.com",
      "example.com",
      "example.de",
      "kurzepost.de",
      "objectmail.com",
      "proxymail.eu",
      "rcpt.at",
      "trash-mail.at",
      "trashmail.at",
      "trashmail.com",
      "trashmail.io",
      "trashmail.me",
      "trashmail.net",
      "wegwerfmail.de",
      "wegwerfmail.net",
      "wegwerfmail.org"
   ]
}
```

**Hinweise:**

* Keine

***





### del\_account

> <https://trashmail.com/?api=1&lang=en&cmd=del_account>

*Entfernt einen bestehenden Benutzeraccount auf TrashMail.com.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,              // Boolean
   "error_code":0,              // Integer
   "msg":"Account deleted."     // String
}
```

**Hinweise:**

* Im Request wird nicht angegeben, welcher Benutzer entfernt werden soll. Aus Sicherheitsgründen kann immer nur der eingeloggte Benutzer entfernt werden (`{"success":false,"error_code":2,"msg":"Not logged in or expired session."}`).

***





### register\_account

> <https://trashmail.com/?api=1&lang=en&cmd=register_account>

*Registriert einen neuen Benutzeraccount auf TrashMail.com.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: email, user, pass, newsletter
// Content-Type: application/json

{
   "email":"trashmail_1@example.email",     // String
   "user":"User",                           // String
   "pass":"secret",                         // String
   "newsletter":true                        // Boolean
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***





### reset\_password

> <https://trashmail.com/?api=1&lang=en&cmd=reset_password>

*Setzt das Passwort zu einem bestehenden Benutzeraccount zurück, falls dieses vergessen wurde.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: email
// Content-Type: application/json

{
   "email":"trashmail_1@example.email"      // String
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Dieser Endpunkt darf nicht mit der "Passwort ändern"-Funktion verwechselt werden! Dieser Endpunkt löst eine E-Mail mit Anweisungen zum Passwort-Reset aus.

***





### change\_password

> <https://trashmail.com/?api=1&lang=en&cmd=change_password>

*Ändert das Passwort zu einem bestehenden Benutzeraccount.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: pass-cfrm
// Content-Type: application/x-www-form-urlencoded

"pass-cfrm:apfel112"        // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Das neue Passwort wird serverseitig als einfacher POST-Parameter erwartet. Aus diesem Grund muss hier `application/x-www-form-urlencoded` statt JSON verwendet werden.

***





### set\_newsletter

> <https://trashmail.com/?api=1&lang=en&cmd=set_newsletter>

*Aktiviert oder deaktiviert das Zusenden von Newslettern auf die im Account hinterlegte echte E-Mail Adresse.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: newsletter
// Content-Type: application/json

{
   "newsletter":true        // Boolean
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***





### set\_status

> <https://trashmail.com/?api=1&lang=en&cmd=set_status>

*Setzt die Frequenz (z.B. wöchentlich), in der Statusnachrichten an die echte E-Mail Adresse verschickt werden sollen.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: data
// Content-Type: application/x-www-form-urlencoded

"data: 7"       // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,              // Boolean
   "error_code":0,              // Integer
   "msg":"Status changed."      // String
}
```

**Hinweise:**

* Die Frequenz wird serverseitig als einfacher GET/POST-Parameter erwartet. Aus diesem Grund muss hier `application/x-www-form-urlencoded` statt JSON verwendet werden.

***





### send\_status

> <https://trashmail.com/?api=1&lang=en&cmd=send_status>

*Löst das Versenden der Statusmeldung auf die im Account hinterlegte echte E-Mail Adresse manuell aus.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,          // Boolean
   "error_code":0,          // Integer
   "msg":"Status sent."     // String
}
```

**Hinweise:**

* Keine

***





### download\_dea\_csv

> <https://trashmail.com/?api=1&lang=en&cmd=download_dea_csv>

*Gibt alle zum Benutzeraccount gehörigen TrashMails im CSV-Format zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: text/plain;charset=UTF-8

// CSV-Rückgabe:
// Created at;Enabled;Disposable address;Destination;Description;Remaining forwards;Expire (days);Website;Whitelist;Reply-Masquerading;Notify;Last Received
// Aug 10, 2020;1;vivian_lohmann@0box.eu;trashmail_1@example.email;"";2;6;"";0;1;1;
// Aug 10, 2020;1;celia.rossler@0box.eu;trashmail_1@ example.email;"";2;6;"";0;1;1;
// Aug 10, 2020;1;teo27@0box.eu;trashmail_1@ example.email;"";2;6;"";0;1;1;
// Aug 10, 2020;1;josefin_habel77@0box.eu;trashmail_1@ example.email;"";2;6;"";0;1;1;
```

**Hinweise:**

* Keine

***





### save\_settings

> <https://trashmail.com/?api=1&lang=en&cmd=save_settings>

*Aktiviert bzw. deaktiviert das Senden einer Bestätigungsmail, wenn eine TrashMail erstellt wurde.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: key, value
// Content-Type: application/x-www-form-urlencoded

"key:notify-on-create-dea"      // Key-Value x-www-form-urlencoded
"value:true"                    // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***





### read\_user\_data

> <https://trashmail.com/?api=1&lang=en&cmd=read_user_data>

*Gibt alle relevanten Benutzereinstellungen zurück. Dieser Endpunkt kann für das Anzeigen von Einstellungen im User Interface verwendet werden.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                                          // Boolean
   "error_code":0,                                                          // Integer
   "data":{
        "status_repetition":7,                                              // Integer
        "notify_on_create_dea":true,                                        // Boolean
        "is_trashmail_plus_user":true,                                      // Boolean
        "trashmail_plus_expires_at":"Thu 10. Jun 10:40:55 CEST 2021",       // String
        "receive_newsletter":true,                                          // Boolean
        "rewrite_from": true,                                               // Boolean
        "rewrite_to": true,                                                 // Boolean
        "log_emails": true,                                                 // Boolean
        "sim_ml": true,                                                     // Boolean
        "subject_prefix":"[TrashMail]"                                      // String
   }
}
```

**Hinweise:**

* Wenn TrashMail Plus nicht aktiv ist (`is_trashmail_plus_user ` = false), dann wird ein leerer String für `trashmail_plus_expires_at` zurückgegeben.
* `sim_ml` ist die Abkürzung für "simulating mailing list" und repräsentiert die Option für "Add mailing list unsubscribe option".

***





### read\_dea

> <https://trashmail.com/?api=1&lang=en&cmd=read_dea>

*Gibt alle TrashMails des Benutzeraccounts zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: -
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                      // Boolean
   "error_code":0,                                      // Integer
   "total":2,                                           // Integer
   "enabled":1,                                         // Integer
   "disabled":1,                                        // Integer
   "fwd_errors":0,                                      // Integer
   "fwd":1,                                             // Integer
   "rejected":0,                                        // Integer
   "data":[
      {
         "id":13549167,                                 // Integer
         "uid":39730391,                                // Integer
         "ctime":1597054890,                            // Integer
         "ctime_text":"Aug 10, 2020",                   // String
         "last_received":0,                             // Integer
         "last_received_text":"",                       // String
         "from_name":"",                                // String
         "disposable_name":"celia.rossler",             // String
         "disposable_domain":"0box.eu",                 // String
         "destination":"trashmail_1@example.email",     // String
         "enabled":true,                                // Boolean
         "forwards":2,                                  // Integer
         "expire":6,                                    // Integer
         "website":"",                                  // String
         "cs":0,                                        // Integer
         "masq":true,                                   // Boolean
         "notify":true,                                 // Boolean
         "desc":"",                                     // String
         "blacklist_count":0,                           // Integer
         "whitelist_count":0,                           // Integer
         "queue_count":0,                               // Integer
         "logging_count":0,                             // Integer
         "fwd_errors":0,                                // Integer
         "stats_fwd":0,                                 // Integer
         "stats_rejected":0                             // Integer
      }
   ]
}
```

**Hinweise:**

* Keine

***





### read\_dea\(Suche\)

> <https://trashmail.com/?api=1&lang=en&cmd=read_dea>

*Gibt alle TrashMails zu einem bestimmten Suchkriterium des Benutzeraccounts zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: search
// Content-Type: application/x-www-form-urlencoded

"search:test"       // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                      // Boolean
   "error_code":0,                                      // Integer
   "total":2,                                           // Integer
   "enabled":1,                                         // Integer
   "disabled":1,                                        // Integer
   "fwd_errors":0,                                      // Integer
   "fwd":1,                                             // Integer
   "rejected":0,                                        // Integer
   "data":[
      {
         "id":13549167,                                 // Integer
         "uid":39730391,                                // Integer
         "ctime":1597054890,                            // Integer
         "ctime_text":"Aug 10, 2020",                   // String
         "last_received":0,                             // Integer
         "last_received_text":"",                       // String
         "from_name":"",                                // String
         "disposable_name":"test.rossler",              // String
         "disposable_domain":"0box.eu",                 // String
         "destination":"trashmail_1@example.email",     // String
         "enabled":true,                                // Boolean
         "forwards":2,                                  // Integer
         "expire":6,                                    // Integer
         "website":"",                                  // String
         "cs":0,                                        // Integer
         "masq":true,                                   // Boolean
         "notify":true,                                 // Boolean
         "desc":"",                                     // String
         "blacklist_count":0,                           // Integer
         "whitelist_count":0,                           // Integer
         "queue_count":0,                               // Integer
         "logging_count":0,                             // Integer
         "fwd_errors":0,                                // Integer
         "stats_fwd":0,                                 // Integer
         "stats_rejected":0                             // Integer
      }
   ]
}
```

**Hinweise:**

* Keine

***






### set\_rewrite\_from

> <https://trashmail.com/?api=1&lang=en&cmd=set_rewrite_from>

*Aktiviert oder deaktiviert das Umschreiben des "From"-Headers in weitergeleiteten E-Mails.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: flag
// Content-Type: application/json

{
   "flag":true        // Boolean
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***






### set\_rewrite\_to

> <https://trashmail.com/?api=1&lang=en&cmd=set_rewrite_to>

*Aktiviert oder deaktiviert das Umschreiben des "To"-Headers in weitergeleiteten E-Mails.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: flag
// Content-Type: application/json

{
   "flag":true        // Boolean
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***






### set\_log\_emails

> <https://trashmail.com/?api=1&lang=en&cmd=set_log_emails>

*Aktiviert oder deaktiviert das Loggen des E-Mail Inhalts beim Protokoll.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: flag
// Content-Type: application/json

{
   "flag":true        // Boolean
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***






### set\_subject\_prefix

> <https://trashmail.com/?api=1&lang=en&cmd=set_subject_prefix>

*Hierüber kann ein Präfix im Betreff jeder weitergeleiteten E-Mail gesetzt werden.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: prefix
// Content-Type: application/json

{
   "prefix":"[TrashMail]"       // String
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***






### set\_sim\_ml

> <https://trashmail.com/?api=1&lang=en&cmd=set_sim_ml>

*Aktiviert oder deaktiviert die Mailing-List-Deabonnieren-Funktion.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: flag
// Content-Type: application/json

{
   "flag":true        // Boolean
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* `sim_ml` ist die Abkürzung für "simulating mailing list" und repräsentiert die Option für "Add mailing list unsubscribe option".

***








### destroy\_dea

> <https://trashmail.com/?api=1&lang=en&cmd=destroy_dea>

*Entfernt TrashMails des Benutzeraccounts.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: data
// Content-Type: application/json

{
	"data": "13549167"                     // String
}

// Mehrere TrashMails

{
	"data": ["13549167", "13549165"]       // String...
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Das Löschen erfolgt nicht über den Namen der TrashMail, sondern über die ID, die z.B. bei `read_dea` zurückkommt.

***





### send\_mail

_!!! TrashMail-Plus Feature !!!_

> <https://trashmail.com/?api=1&lang=en&cmd=send_mail>

*Versendet eine E-Mail über die angegebene TrashMail.*

**Update 10.09.20:**
Im HTTP-Request wird eine weitere JSON-Eigenschaft `authenticate_key` eingeführt. Sie enthält einen String mit einem Authentifizierungscode für die iOS-App, damit diese ohne Captcha-Code E-Mails versenden kann.

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: from, to, cc, bcc, subject, body, send-copy
// Content-Type: application/json

{
   "from":"veronika_kopf27@trashmail.com",      // String
   "to":"trashmail_1@example.email",            // String
   "cc":"",                                     // String
   "bcc":"",                                    // String
   "subject":"Testnachricht",                   // String
   "body":"<b>Hallo</b> zusammen!",             // String
   "send-copy":true,                            // Boolean
   "authenticate_key":"fepqivnbk..."            // String
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***





### create\_dea

_!!! TrashMail-Plus Feature (Weiterleitungen > 10 und Ablauf > 1 Monat) !!!_

> <https://trashmail.com/?api=1&lang=en&cmd=create_dea>

*Erstellt eine neue TrashMail mit den angegebenen Parametern.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: destination, from_name, disposable_name, disposable_domain, desc, website, enabled, forwards, expire, cs, notify, masq, catchAll, preferences
// Content-Type: application/json

// Klassisch
{
   "data":{
      "destination":"trashmail_1@example.email",                                // String
      "from_name":"Max Mustermann",                                             // String
      "disposable_name":"api_8",                                                // String
      "disposable_domain":"trashmail.com",                                      // String
      "desc":"Automatisch via Postman erstellt.",                               // String
      "website":"postman.com",                                                  // String
      "enabled":true,                                                           // Boolean
      "forwards":10, // -1 entspricht unendlich!                                // Integer
      "expire":12,                                                              // Integer
      "cs":0, // Whitelist (0 Aus - 3 An, mit einmaligem Bestätigungscode)      // Integer
      "notify":true, // Ablaufmeldung                                           // Boolean
      "masq":true, // Antwort-Maskierung                                        // Boolean
      "catchAll":false, // Auto-Empfang-Email                                   // Boolean
      "preferences": {                                                      // Array
               "DefaultCS": 0,                                              // Integer
               "DefaultDomain": "trashmail.com",                            // String
               "DefaultFwds": 100,                                          // Integer
               "DefaultLifeSpan": 7,                                        // Integer
               "DefaultMasq": 1,                                            // Integer
               "DefaultNotify": 1,                                          // Integer
               "DefaultRealEmailAddress": "name@domain.tld",                // String
               "DefaultSortDir": "DESC",                                    // String
               "DefaultSortField": "ctime_text",                            // String
               "notify-on-create-dea": true                                 // Boolean
      }
   }
}

// Auto-Empfang
{
   "data":{
      "destination":"trashmail_1@example.email",                                // String
      "from_name":"Max Mustermann",                                             // String
      "disposable_name":"*.api_9",                                              // String
      "disposable_domain":"trashmail.com",                                      // String
      "desc":"Automatisch via Postman erstellt.",                               // String
      "website":"postman.com",                                                  // String
      "enabled":true,                                                           // Boolean
      "forwards":10,                                                            // Integer
      "expire":12,                                                              // Integer
      "cs":0,                                                                   // Integer
      "notify":true,                                                            // Boolean
      "masq":true,                                                              // Boolean
      "catchAll":true,                                                          // Boolean
      "preferences": {                                                      // Array
               "DefaultCS": 0,                                              // Integer
               "DefaultDomain": "trashmail.com",                            // String
               "DefaultFwds": 100,                                          // Integer
               "DefaultLifeSpan": 7,                                        // Integer
               "DefaultMasq": 1,                                            // Integer
               "DefaultNotify": 1,                                          // Integer
               "DefaultRealEmailAddress": "name@domain.tld",                // String
               "DefaultSortDir": "DESC",                                    // String
               "DefaultSortField": "ctime_text",                            // String
               "notify-on-create-dea": true                                 // Boolean
      }
   }
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                          // Boolean
   "error_code":0,                                          // Integer
   "message":"Created disposable address",                  // String
   "data":[
      {
         "id":13549797,                                     // Integer
         "uid":12345,                                       // Integer
         "destination":"trashmail_1@example.email",         // String
         "cs":0,                                            // Integer
         "disposable_name":"api_8",                         // String
         "disposable_domain":"trashmail.com",               // String
         "expire":12,                                       // Integer
         "forwards":10,                                     // Integer
         "masq":true,                                       // Boolean
         "notify":true,                                     // Boolean
         "website":"postman.com",                           // String
         "preferences": {
            // TODO
         }
      }
   ]
}
```

**Hinweise:**

* Es können in einem Array mehere TrashMails gleichzeitig erstellt werden.

***





### update\_dea

_!!! TrashMail-Plus Feature (Weiterleitungen > 10 und Ablauf > 1 Monat) !!!_

> <https://trashmail.com/?api=1&lang=en&cmd=update_dea>

*Ändert die Einstellungen einer bestimmten TrashMail.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id (einzig verpflichtend), enabled, from_name, disposable_name, disposable_domain, destination, desc, forwards, expire, website, cs, masq, notify, preferences
// Content-Type: application/json

// Zum Beispiel
{
   "data":{
      "id":"13549165",      // String
      "enabled":false,      // Boolean
      "preferences": {                                                      // Array
               "DefaultCS": 0,                                              // Integer
               "DefaultDomain": "trashmail.com",                            // String
               "DefaultFwds": 100,                                          // Integer
               "DefaultLifeSpan": 7,                                        // Integer
               "DefaultMasq": 1,                                            // Integer
               "DefaultNotify": 1,                                          // Integer
               "DefaultRealEmailAddress": "name@domain.tld",                // String
               "DefaultSortDir": "DESC",                                    // String
               "DefaultSortField": "ctime_text",                            // String
               "notify-on-create-dea": true                                 // Boolean
      }
   }
}
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0,      // Integer
   "preferences": {                                                     // Array
           "DefaultCS": 0,                                              // Integer
           "DefaultDomain": "trashmail.com",                            // String
           "DefaultFwds": 100,                                          // Integer
           "DefaultLifeSpan": 7,                                        // Integer
           "DefaultMasq": 1,                                            // Integer
           "DefaultNotify": 1,                                          // Integer
           "DefaultRealEmailAddress": "name@domain.tld",                // String
           "DefaultSortDir": "DESC",                                    // String
           "DefaultSortField": "ctime_text",                            // String
           "notify-on-create-dea": true                                 // Boolean
  }
}
```

**Hinweise:**

* Es müssen nicht alle Werte gesetzt werden! Es reicht, wenn der Wert, der geändert werden soll, im JSON angegeben wird.
* Es können in einem Array mehere TrashMails gleichzeitig geändert werden.

***





### read\_logging

> <https://trashmail.com/?api=1&lang=en&cmd=read_logging>

*Liest das Protokoll einer bestimmten TrashMail aus.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id
// Content-Type: application/x-www-form-urlencoded

"id:13549165"       // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                                              // Boolean
   "error_code":0,                                                              // Integer
   "data":[
      {
         "id":1336740,                                                          // Integer
         "ctime":1597144087,                                                    // Integer
         "ctime_text":"Aug 11, 2020, 1:08:07 PM GMT+2",                         // String
         "from":"ida70@trashmail.com",                                          // String
         "message_id":"<21FAB5EA-7957-4F64-B088-05C248317652@aionda.com>",      // String
         "fwd":"trashmail_1@example.email",                                     // String
         "subject":"Hallo",                                                     // String
         "status":"STATUS_BOUNCED",                                             // String
         "status_text":"Error"                                                  // String
      }
   ]
}
```

**Hinweise:**

* Keine

***





### read\_logging\_raw\_data

> <https://trashmail.com/?api=1&lang=en&cmd=read_logging_raw_data>

*Gibt den E-Mail Inhalt eines bestimmten Protokolleintrags zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id, output
// Content-Type: application/x-www-form-urlencoded
// Accept: application/json

"id:1336740"        // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success": true,                             // Boolean
   "error_code": 0,                             // Integer
   "data": "From ida70@trashmail.com..."        // String
}
```

**Hinweise:**

* Der Inhaltstyp wird durch den HTTP-Header "Accept" ermittelt. Aktuell verfügbar sind `application/json` und `text/plain`.

***





### read\_logging\_events

> <https://trashmail.com/?api=1&lang=en&cmd=read_logging_events>

*Gibt die Ereignisse eines bestimmten Protokolleintrags zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id
// Content-Type: application/x-www-form-urlencoded

"id:1353059"        // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                                  // Boolean
   "error_code":0,                                                  // Integer
   "data":[
      {
         "id":3943009,                                              // Integer
         "ctime":1597223356,                                        // Integer
         "ctime_text":"Aug 12, 2020, 11:09:16 AM GMT+2",            // String
         "event":"NOT_ON_WHITELIST_HOLD_EMAIL",                     // String
         "event_data": "",                                          // String
         "event_text":"Not on whitelist, hold email in queue.",     // String
         "envelope_sender":"fail@aionda.com",                       // String
         "to":"fail@aionda.com",                                    // String
         "relay":"mail.aionda.com[2a01:4f8:c0c:a2d2::1]:25",        // String
         "sender":"a.mxout.trashmail.com"                           // String
      }
   ]
}
```

**Hinweise:**

* Keine

***





### del\_logging

> <https://trashmail.com/?api=1&lang=en&cmd=del_logging>

*Löscht einen bestimmten Eintrag aus der Protokollliste.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: idList
// Content-Type: application/x-www-form-urlencoded

"idList:[1337278]"      // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Es können mehrere IDs angegeben werden, diese werden durch Komma getrennt `[1,2,3]`.

***





### send\_logging\_emails

> <https://trashmail.com/?api=1&lang=en&cmd=send_logging_emails>

*Sendet die E-Mail aus dem Protokoll erneut an die angegebene E-Mail Adresse.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: idList, fwd
// Content-Type: application/x-www-form-urlencoded

"idList:[1337275]"                  // Key-Value x-www-form-urlencoded
"fwd:trashmail_1@example.email"     // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Es können mehrere IDs angegeben werden, diese werden durch Komma getrennt `[1,2,3]`.
* Die fwd-Mail-Adresse muss eine im Account hinterlegte echte E-Mail Adresse sein. 

***





### load\_cs\_messages

> <https://trashmail.com/?api=1&lang=en&cmd=load_cs_messages>

*Gibt die individuellen Benachrichtigungen (z.B. wenn Bestätigungscode für Zustellung notwendig ist) für eine bestimmte TrashMail zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: data
// Content-Type: application/x-www-form-urlencoded

'data:["39730821","wl-confirm"]'        // Key-Value x-www-form-urlencoded
// wl-confirm, confirm-every, confirm-off
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                              // Boolean
   "error_code":0,                              // Integer
   "data":{
      "from_name":"TrashMail.com",              // String
      "from_email":"robot@trashmail.com",       // String
      "subject":"$REPLY",                       // String
      "body":"Hello,\n\nthis email address $USER@$DOMAIN requires manual confirmation.\nPlease confirm your sent email by going on the following URL address:\n$CONFIRMATION_URL\n\nFor more information: Please visit https:\/\/trashmail.com\/\n\n-- Sent message follows --\n$EMAIL\n-- End of sent message --\n\n-- \nThe TrashMail robot.\nhttps:\/\/trashmail.com\/\n"        // String
   }
}
```

**Hinweise:**

* Die im Request angegebene ID ist nicht die "ID" der TrashMail, sondern die "UID" der TrashMail! Enthalten in der Rückgabe der DEAs: `{"id":"13549165","uid":"39730389", ...`.

***





### save\_cs\_messages

> <https://trashmail.com/?api=1&lang=en&cmd=save_cs_messages>

*Speichert eine andere Version der individuellen Benachrichtigungen (z.B. wenn Bestätigungscode für Zustellung notwendig ist) für eine bestimmte TrashMail.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: param, name, from, subject, body
// Content-Type: application/x-www-form-urlencoded

'param:["39730821","wl-confirm"]'       // Key-Value x-www-form-urlencoded
'name:Testroboter'                      // Key-Value x-www-form-urlencoded
'from:testrobot@trashmail.com'          // Key-Value x-www-form-urlencoded
'subject:Test'                          // Key-Value x-www-form-urlencoded
'body:Hallo!'                           // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Die im Request angegebene ID ist nicht die "ID" der TrashMail, sondern die "UID" der TrashMail! Enthalten in der Rückgabe der DEAs: `{"id":"13549165","uid":"39730389", ...`.

***





### read\_whitelist

> <https://trashmail.com/?api=1&lang=en&cmd=read_whitelist>

*Gibt alle Einträge der Whitelist einer bestimmten TrashMail zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: user_id
// Content-Type: application/x-www-form-urlencoded

"user_id:39730389"      // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                              // Boolean
   "error_code":0,                              // Integer
   "data":[
      {
         "id":45617,                            // Integer
         "email":"sender@example.email",        // String
         "ctime":1597155087,                    // Integer
         "ctime_text":"8\/11\/20, 4:11 PM"      // String
      }
   ]
}
```

**Hinweise:**

* Die im Request angegebene ID ist nicht die "ID" der TrashMail, sondern die "UID" der TrashMail! Enthalten in der Rückgabe der DEAs: `{"id":"13549165","uid":"39730389", ...`.

***





### read\_whitelist\(Suche\)

> <https://trashmail.com/?api=1&lang=en&cmd=read_whitelist>

*Gibt die Einträge der Whitelist einer bestimmten TrashMail zurück, die das Suchkriterium erfüllen.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: user_id, search
// Content-Type: application/x-www-form-urlencoded

"user_id:39730389"      // Key-Value x-www-form-urlencoded
"search:test"           // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                              // Boolean
   "error_code":0,                              // Integer
   "data":[
      {
         "id":45619,                            // Integer
         "email":"test@example.email",          // String
         "ctime":1597155486,                    // Integer
         "ctime_text":"8\/11\/20, 4:18 PM"      // String
      }
   ]
}
```

**Hinweise:**

* Die im Request angegebene ID ist nicht die "ID" der TrashMail, sondern die "UID" der TrashMail! Enthalten in der Rückgabe der DEAs: `{"id":"13549165","uid":"39730389", ...`.

***





### whitelist\_add

> <https://trashmail.com/?api=1&lang=en&cmd=whitelist_add>

*Fügt eine E-Mail Adresse der Whitelist einer bestimmten TrashMail hinzu.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: data
// Content-Type: application/x-www-form-urlencoded

'data:["39730389","example@example.email"]'     // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Die im Request angegebene ID ist nicht die "ID" der TrashMail, sondern die "UID" der TrashMail! Enthalten in der Rückgabe der DEAs: `{"id":"13549165","uid":"39730389", ...`.

***





### whitelist\_delete

> <https://trashmail.com/?api=1&lang=en&cmd=whitelist_delete>

*Entfernt eine E-Mail Adresse aus der Whitelist einer bestimmten TrashMail.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: data
// Content-Type: application/x-www-form-urlencoded

"data:45618"        // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Die Data-ID ist die `id`, die bei `read_whitelist` zurückgegeben wird.

***





### add\_blacklist

> <https://trashmail.com/?api=1&lang=en&cmd=add_blacklist>

*Fügt eine E-Mail Adresse der Blacklist einer bestimmten TrashMail hinzu.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id, description, mail_from, subject, body
// Content-Type: application/x-www-form-urlencoded

"id:13544576"                           // Key-Value x-www-form-urlencoded
"description:Testregel"                 // Key-Value x-www-form-urlencoded
"mail_from:tester@example.email"        // Key-Value x-www-form-urlencoded
"subject:"                              // Key-Value x-www-form-urlencoded
"body:"                                 // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***





### del\_blacklist

> <https://trashmail.com/?api=1&lang=en&cmd=del_blacklist>

*Entfernt eine E-Mail Adresse aus der Blacklist einer bestimmten TrashMail.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id
// Content-Type: application/x-www-form-urlencoded

"id:297"        // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Die `id` wird bei `read_blacklist` zurückgegeben.

***





### read\_blacklist

> <https://trashmail.com/?api=1&lang=en&cmd=read_blacklist>

*Gibt alle Einträge der Blacklist einer bestimmten TrashMail zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id
// Content-Type: application/x-www-form-urlencoded

"id:13544576"       // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                              // Boolean
   "error_code":0,                              // Integer
   "data":[
      {
         "id":299,                              // Integer
         "description":"TestA",                 // String
         "mail_from":"spam@example.email",      // String
         "subject":"",                          // String
         "body":"",                             // String
         "rejected":0,                          // Integer
         "last_rejection_time_text":"-",        // String
         "last_rejection_body":"",              // String
         "ctime_text":"8\/11\/20",              // String
         "ctime": 123456                        // Integer
      }
   ]
}
```

**Hinweise:**

* Keine

***





### read\_blacklist\(Suche\)

> <https://trashmail.com/?api=1&lang=en&cmd=read_blacklist>

*Gibt die Einträge der Blacklist einer bestimmten TrashMail zurück, die das Suchkriterium erfüllen.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id, search
// Content-Type: application/x-www-form-urlencoded

"id:13544576"       // Key-Value x-www-form-urlencoded
"search:test"       // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                              // Boolean
   "error_code":0,                              // Integer
   "data":[
      {
         "id":299,                              // Integer
         "description":"TestA",                 // String
         "mail_from":"spam@example.email",      // String
         "subject":"",                          // String
         "body":"",                             // String
         "rejected":0,                          // Integer
         "last_rejection_time_text":"-",        // String
         "last_rejection_body":"",              // String
         "ctime_text":"8\/11\/20",              // String
         "ctime": 123456                        // Integer
      }
   ]
}
```

**Hinweise:**

* Keine

***





### edit\_blacklist

> <https://trashmail.com/?api=1&lang=en&cmd=edit_blacklist>

*Bearbeitet einen Eintrag in der Blacklist einer TrashMail.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: id (einzig verpflichtend), description, mail_from, subject, body
// Content-Type: application/x-www-form-urlencoded

"id:299"                                            // Key-Value x-www-form-urlencoded
"description:Das ist eine neue Beschreibung!"       // Key-Value x-www-form-urlencoded
"subject:Hallo!"                                    // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Es müssen nicht immer alle Werte übergeben werden. Es reicht aus, wenn der zu ändernde Wert (z.B. `description`) übergeben wird. Zwingend angegeben werden muss aber die `id` zur serverseitigen Identifizierung.

***





### read\_queue

> <https://trashmail.com/?api=1&lang=en&cmd=read_queue>

*Gibt alle Einträge der Warteschlange einer bestimmten TrashMail zurück.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: user_id
// Content-Type: application/x-www-form-urlencoded

"user_id:39705691"      // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                              // Boolean
   "error_code":0,                                              // Integer
   "data":[
      {
         "id":17797197,                                         // Integer
         "ctime":1597219948,                                    // Integer
         "ctime_text":"8\/12\/20, 10:12 AM",                    // String
         "sender_addr":"tester@example.email",                  // String
         "subject":"Test"                                       // String
      }
   ]
}
```

**Hinweise:**

* Die im Request angegebene ID ist nicht die "ID" der TrashMail, sondern die "UID" der TrashMail! Enthalten in der Rückgabe der DEAs: `{"id":"13549165","uid":"39730389", ...`.

***





### read\_queue\(Suche\)

> <https://trashmail.com/?api=1&lang=en&cmd=read_queue>

*Gibt die Einträge der Warteschlange einer bestimmten TrashMail zurück, die das Suchkriterium erfüllen.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: user_id, search
// Content-Type: application/x-www-form-urlencoded

"user_id:39705691"      // Key-Value x-www-form-urlencoded
"search:Zweiter"        // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,                                              // Boolean
   "error_code":0,                                              // Integer
   "data":[
      {
         "id":17797197,                                         // Integer
         "ctime":1597219948,                                    // Integer
         "ctime_text":"8\/12\/20, 10:12 AM",                    // String
         "sender_addr":"tester@example.email",                  // String
         "subject":"Zweiter Test"                               // String
      }
   ]
}
```

**Hinweise:**

* Die im Request angegebene ID ist nicht die "ID" der TrashMail, sondern die "UID" der TrashMail! Enthalten in der Rückgabe der DEAs: `{"id":"13549165","uid":"39730389", ...`.

***





### queue\_delete\_msg

> <https://trashmail.com/?api=1&lang=en&cmd=queue_delete_msg>

*Löscht eine bestimmte E-Mail aus der Warteschlange einer bestimmten TrashMail.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: data
// Content-Type: application/x-www-form-urlencoded

"data:17797197"     // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Keine

***





### queue\_delete\_all\_msg

> <https://trashmail.com/?api=1&lang=en&cmd=queue_delete_all_msg>

*Löscht alle E-Mails aus der Warteschlange einer bestimmten TrashMail.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: data
// Content-Type: application/x-www-form-urlencoded

"data:39705691"     // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Die im Request angegebene ID ist nicht die "ID" der TrashMail, sondern die "UID" der TrashMail! Enthalten in der Rückgabe der DEAs: `{"id":"13549165","uid":"39730389", ...`.

***





### queue\_accept\_msg

> <https://trashmail.com/?api=1&lang=en&cmd=queue_accept_msg>

*Akzeptiert eine E-Mail aus der Warteschlange einer bestimmten TrashMail.*

```javascript
// Eingabe

// HTTP-Methode: POST
// Erwartete Eigenschaften: data
// Content-Type: application/x-www-form-urlencoded

"data:17797209"     // Key-Value x-www-form-urlencoded
```

```javascript
// Ausgabe

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Hinweise:**

* Es werden alle wartenden E-Mails des gleichen Absenders durchgelassen.

***
