# TrashMail.com public HTTP API - Reference

In this document, the backend endpoints and their behavior are documented. This description will subsequently serve as the basis for the development of the iOS and macOS app and for this reason should be as error-free as possible. Before pushing changes to this document, the Xcode project must be checked after using the endpoints and adjusted if necessary!

The information provided here is based on:

1. the source code of the TrashMail.com backend
2. the examined TrashMail-com frontend
3. existing TrashMail extensions
4. and the studied production backend using [Postman](https://www.postman.com).

To demonstrate the functionality in the best possible way, the test user "User" is used in the following examples. The requests and responses are not kept generic, but show the data of "User".

The structure of this document follows a clear structure. Each endpoint is introduced with.

1. its address (URL) introduced
2. defined with a short description of its functionality
3. the expected input parameters (e.g. the structure of the JSON) specified
4. specifies the expected output parameters (e.g. the structure of the JSON)
5. and specified with possible hints or special features.

Each endpoint follows the same structure:

```text
https://trashmail.com/?
api=1 &   // Enables API functionality
lang=x &  // en, de, fr
cmd=x     // Depending on the function to be called

// Parameters are not specified in the URL as in REST, but in the HTTP body.
```

Error messages always have the same structure:

```text
{
   "success":false,
   "error_code":x,    // Error code > 0
   "msg":"x"          // Describes the error (or the code)
}
```

Success messages always have the same structure:

```text
{
   "success":true,
   "error_code":0,    // Code 0 corresponds to success
   "msg":{...}        // Depending on the endpoint Response to the request
}
```

**The JSON property "msg" is returned in a different language depending on the HTTP GET(lang) set!**

## Available commands (cmd's)

### User data

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


### All TrashMails regarding

* [read\_dea](#read\_dea)
* [read\_dea (Suche)](#read\_dea\(Suche\))
* [set\_rewrite\_from](#set\_rewrite\_from)
* [set\_rewrite\_to](#set\_rewrite\_to)
* [set\_log\_emails](#set\_log\_emails)
* [set\_subject\_prefix](#set\_subject\_prefix)
* [set\_sim\_ml](#set\_sim\_ml)


### Each for one TrashMail

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


## Explanation of the endpoints


***

If the request is sent as `x-www-form-urlencoded`:  
The POST parameters must be [URL-encoded](https://en.wikipedia.org/wiki/Percent-encoding): `[1,2,3]` => `%5B1,2,3%5D`, `mail@domain.de` => `mail%40domain.com`, etc. (For Swift: `addingPercentEncoding`).

***




### login

> <https://trashmail.com/?api=1&lang=en&cmd=login>

*This endpoint enables login to the TrashMail.com server to subsequently access backend functionalities.*

```javascript
// Input

// HTTP method: POST
// Expected properties: fe-login-user, fe-login-pass
// Content-Type: application/json

{
   "fe-login-user":"User",          // String
   "fe-login-pass":"secret"         // String
}
```

```javascript
// Output

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

**Notes:** 


* The call of this endpoint must be made before the call of other endpoints providing functionalities! If this is not adhered to, the error message "Not logged in or expired session." will be returned.

***





### logout

> <https://trashmail.com/?api=1&lang=en&cmd=logout>

*This endpoint logs the user out of the TrashMail.com server and resets the server-side session. After calling this endpoint, the `login` endpoint must be called again to use the TrashMail.com server functionalities.*

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***






### list\_real\_emails

> <https://trashmail.com/?api=1&lang=en&cmd=list_real_emails>

*Returns all real email addresses stored in the account.*

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

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

**Notes:**

* None

***






### add\_real\_email

> <https://trashmail.com/?api=1&lang=en&cmd=add_real_email>

*Adds a real email address to the account.*

```javascript
// Input

// HTTP method: POST
// Expected properties: email
// Content-Type: application/json

{
   "email":"trashmail_2@example.email"      // String
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* If the request was successful, the email address will not yet appear among your own email addresses. A successful request will only trigger a confirmation email to the real email address and will only be assigned to the account afterwards.

***




### set\_default\_email

> <https://trashmail.com/?api=1&lang=en&cmd=set_default_email>

*Sets one of the real email addresses stored in the account as default.*


```javascript
// Input

// HTTP method: POST
// Expected properties: email
// Content-Type: application/json

{
   "email":"trashmail_2@example.email"
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* Non-existing email addresses will be rejected with the message "This email address needs to be confirmed first. Please check your email inbox for a confirmation email." rejected.

***




### set\_failover\_email

> <https://trashmail.com/?api=1&lang=en&cmd=set_failover_email>

*Sets one of the real email addresses stored in the account as failover.*

```javascript
// Input

// HTTP method: POST
// Expected properties: email
// Content-Type: application/json

{
   "email":"trashmail_2@example.email"
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* A failover address is optional. For this reason it can also be reset. For this purpose `"email":""` must be sent.

***




### del\_real\_email

> <https://trashmail.com/?api=1&lang=en&cmd=del_real_email>

*Removes one of the real email addresses stored in the account.*

```javascript
// Input

// HTTP method: POST
// Expected properties: email
// Content-Type: application/json

{
   "email":"trashmail_2@example.email"
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,                                  // Boolean
   "error_code":0,                                  // Integer
   "default_email":"trashmail_1@example.email"      // String
}
```

**Notes:**

* If the default email address is removed, another email address will automatically be set as default.

***




### add\_domain

> <https://trashmail.com/?api=1&lang=en&cmd=add_domain>

*Adds own domain to the account for personalized email addresses.

```javascript
// Input

// HTTP method: POST
// Expected properties: domain
// Content-Type: application/json

{
	"domain": "example.email"      // String
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,                                                                      // Boolean
   "error_code":0,                                                                      // Integer
   "status":"Couldn't find 2 MX records, no MX entries found., missing SPF entry"       // String
}
```

**Notes:**

* None

***





### status\_domains

> <https://trashmail.com/?api=1&lang=en&cmd=status_domains>

*Returns the current status for all domains stored in the account.

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

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

**Notes:**

* To get the status of a single domain, the corresponding domain must be extracted from the array. There is no endpoint that can be explicitly requested for a domain.

***






### del\_domain

> <https://trashmail.com/?api=1&lang=en&cmd=del_domain>

*Removes one of the domains stored in the account.*

```javascript
// Input

// HTTP method: POST
// Expected properties: domain
// Content-Type: application/json

{
	"domain": "example.email"      // String
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* When deleting a non-existent domain, no error description is returned: `{"success":false, "error_code":0}`.

***





### list\_domains

> <https://trashmail.com/?api=1&lang=en&cmd=list_domains>


*Returns all domains stored in the account.*

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

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

**Notes:**

* None

***




### del\_account

> <https://trashmail.com/?api=1&lang=en&cmd=del_account>

*Removes an existing user account on TrashMail.com.*

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,              // Boolean
   "error_code":0,              // Integer
   "msg":"Account deleted."     // String
}
```

**Notes:**

* The request does not specify which user should be removed. For security reasons only the logged in user can be removed (`{"success":false, "error_code":2, "msg": "Not logged in or expired session."}`).

***





### register\_account

> <https://trashmail.com/?api=1&lang=en&cmd=register_account>

*Registers a new user account on TrashMail.com.*

```javascript
// Input

// HTTP method: POST
// Expected properties: email, user, pass, newsletter
// Content-Type: application/json

{
   "email":"trashmail_1@example.email",     // String
   "user":"User",                           // String
   "pass":"secret",                         // String
   "newsletter":true                        // Boolean
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***





### reset\_password

> <https://trashmail.com/?api=1&lang=en&cmd=reset_password>

*Resets the password to an existing user account if it has been forgotten.

```javascript
// Input

// HTTP method: POST
// Expected properties: email
// Content-Type: application/json

{
   "email":"trashmail_1@example.email"      // String
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* This endpoint must not be confused with the "Change Password" function! This endpoint triggers an email with password reset instructions.

***




### change\_password

> <https://trashmail.com/?api=1&lang=en&cmd=change_password>

*Changes the password to an existing user account.

```javascript
// Input

// HTTP method: POST
// Expected properties: pass-cfrm
// Content-Type: application/x-www-form-urlencoded

"pass-cfrm:apfel112"        // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* The new password is expected on the server side as a simple POST parameter. For this reason `application/x-www-form-urlencoded` must be used here instead of JSON.

***





### set\_newsletter

> <https://trashmail.com/?api=1&lang=en&cmd=set_newsletter>

*Enables or disables the sending of newsletters to the real email address stored in the account.

```javascript
// Input

// HTTP method: POST
// Expected properties: newsletter
// Content-Type: application/json

{
   "newsletter":true        // Boolean
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***





### set\_status

> <https://trashmail.com/?api=1&lang=en&cmd=set_status>

*Sets the frequency (e.g. weekly) at which status messages should be sent to the real email address.

```javascript
// Input

// HTTP method: POST
// Expected properties: data
// Content-Type: application/x-www-form-urlencoded

"data: 7"       // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,              // Boolean
   "error_code":0,              // Integer
   "msg":"Status changed."      // String
}
```

**Notes:**

* The frequency is expected on the server side as a simple GET/POST parameter. For this reason `application/x-www-form-urlencoded` must be used here instead of JSON.

***





### send\_status

> <https://trashmail.com/?api=1&lang=en&cmd=send_status>

*Triggers the sending of the status message manually to the real email address stored in the account.

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,          // Boolean
   "error_code":0,          // Integer
   "msg":"Status sent."     // String
}
```

**Notes:**

* None

***





### download\_dea\_csv

> <https://trashmail.com/?api=1&lang=en&cmd=download_dea_csv>

*Returns all TrashMails belonging to the user account in CSV format.

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

// Content-Type: text/plain;charset=UTF-8

// CSV-R??ckgabe:
// Created at;Enabled;Disposable address;Destination;Description;Remaining forwards;Expire (days);Website;Whitelist;Reply-Masquerading;Notify;Last Received
// Aug 10, 2020;1;vivian_lohmann@0box.eu;trashmail_1@example.email;"";2;6;"";0;1;1;
// Aug 10, 2020;1;celia.rossler@0box.eu;trashmail_1@ example.email;"";2;6;"";0;1;1;
// Aug 10, 2020;1;teo27@0box.eu;trashmail_1@ example.email;"";2;6;"";0;1;1;
// Aug 10, 2020;1;josefin_habel77@0box.eu;trashmail_1@ example.email;"";2;6;"";0;1;1;
```

**Notes:**

* None

***





### save\_settings

> <https://trashmail.com/?api=1&lang=en&cmd=save_settings>

*Enables or disables sending a confirmation mail when a TrashMail is created.

```javascript
// Input

// HTTP method: POST
// Expected properties: key, value
// Content-Type: application/x-www-form-urlencoded

"key:notify-on-create-dea"      // Key-Value x-www-form-urlencoded
"value:true"                    // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***





### read\_user\_data

> <https://trashmail.com/?api=1&lang=en&cmd=read_user_data>

*Returns all relevant user settings. This endpoint can be used to display settings in the user interface.

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

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

**Notes:**

* If TrashMail Plus is not active (`is_trashmail_plus_user ` = false), then an empty string is returned for `trashmail_plus_expires_at`.
* `sim_ml` is short for "simulating mailing list" and represents the add mailing list unsubscribe option.

***




### read\_dea

> <https://trashmail.com/?api=1&lang=en&cmd=read_dea>

*Returns all TrashMails of the user account.

```javascript
// Input

// HTTP method: POST
// Expected properties: -
```

```javascript
// Output

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

**Notes:**

* None

***





### read\_dea\(Suche\)

> <https://trashmail.com/?api=1&lang=en&cmd=read_dea>

*Returns all TrashMails for a specific search criterion of the user account.

```javascript
// Input

// HTTP method: POST
// Expected properties: search
// Content-Type: application/x-www-form-urlencoded

"search:test"       // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:**

* None

***






### set\_rewrite\_from

> <https://trashmail.com/?api=1&lang=en&cmd=set_rewrite_from>

*Enables or disables rewriting of the "From" header in forwarded emails.

```javascript
// Input

// HTTP method: POST
// Expected properties: flag
// Content-Type: application/json

{
   "flag":true        // Boolean
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***






### set\_rewrite\_to

> <https://trashmail.com/?api=1&lang=en&cmd=set_rewrite_to>

*Enables or disables rewriting of the "To" header in forwarded emails.

```javascript
// Input

// HTTP method: POST
// Expected properties: flag
// Content-Type: application/json

{
   "flag":true        // Boolean
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***






### set\_log\_emails

> <https://trashmail.com/?api=1&lang=en&cmd=set_log_emails>

*Enables or disables logging of email content at log.

```javascript
// Input

// HTTP method: POST
// Expected properties: flag
// Content-Type: application/json

{
   "flag":true        // Boolean
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***



### set\_subject\_prefix

> <https://trashmail.com/?api=1&lang=en&cmd=set_subject_prefix>

*This can be used to set a prefix in the subject of each forwarded email.

```javascript
// Input

// HTTP method: POST
// Expected properties: prefix
// Content-Type: application/json

{
   "prefix":"[TrashMail]"       // String
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***




### set\_sim\_ml

> <https://trashmail.com/?api=1&lang=en&cmd=set_sim_ml>

*Enables or disables the mailing list unsubscribe function.

```javascript
// Input

// HTTP method: POST
// Expected properties: flag
// Content-Type: application/json

{
   "flag":true        // Boolean
}
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* `sim_ml` is the abbreviation for "simulating mailing list" and represents the option for "Add mailing list unsubscribe option".

***




### destroy\_dea

> <https://trashmail.com/?api=1&lang=en&cmd=destroy_dea>

*Removes TrashMails of the user account.

```javascript
// Input

// HTTP method: POST
// Expected properties: data
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
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* The deletion is not done by the name of the TrashMail, but by the ID, which returns e.g. at `read_dea`.

***




### send\_mail

_!!! TrashMail Plus Feature !!!_

> <https://trashmail.com/?api=1&lang=en&cmd=send_mail>

*Sends an email via the specified TrashMail.

**Update 10.09.20:**
Another JSON property `authenticate_key` is introduced in the HTTP request. It contains a string with an authentication code for the iOS app to send emails without captcha code.

```javascript
// Input

// HTTP method: POST
// Expected properties: from, to, cc, bcc, subject, body, send-copy
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
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***




### create\_dea

_!!! TrashMail-Plus feature (forwards > 10 and expiration > 1 month) !!!_

> <https://trashmail.com/?api=1&lang=en&cmd=create_dea>

*Creates a new TrashMail with the specified parameters.

```javascript
// Input

// HTTP method: POST
// Expected properties: destination, from_name, disposable_name, disposable_domain, desc, website, enabled, forwards, expire, cs, notify, masq, catchAll, preferences
// Content-Type: application/json

// Classic
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
      "cs":0, // Whitelist (0 Aus - 3 An, mit einmaligem Best??tigungscode)      // Integer
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

// Catch-All
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
// Output

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

**Notes:**

* Several TrashMails can be created in one array at the same time.

***




### update\_dea

_!!! TrashMail-Plus feature (forwards > 10 and expiration > 1 month) !!!_

> <https://trashmail.com/?api=1&lang=en&cmd=update_dea>

*Changes the settings of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: id (mandatory), enabled, from_name, disposable_name, disposable_domain, destination, desc, forwards, expire, website, cs, masq, notify, preferences
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
// Output

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

**Notes:** **

* Not all values have to be set! It is enough if the value to be changed is specified in the JSON.
* Several TrashMails can be changed at the same time in one array.

***




### read\_logging

> <https://trashmail.com/?api=1&lang=en&cmd=read_logging>

*Reads the log of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: id
// Content-Type: application/x-www-form-urlencoded

"id:13549165"       // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:**

* None

***




### read\_logging\_raw\_data

> <https://trashmail.com/?api=1&lang=en&cmd=read_logging_raw_data>

*Returns the email content of a specific log entry.

```javascript
// Input

// HTTP method: POST
// Expected properties: id, output
// Content-Type: application/x-www-form-urlencoded
// Accept: application/json

"id:1336740"        // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success": true,                             // Boolean
   "error_code": 0,                             // Integer
   "data": "From ida70@trashmail.com..."        // String
}
```

**Notes:**

* The content type is determined by the HTTP header `accept`. Currently available are `application/json` and `text/plain`.

***




### read\_logging\_events

> <https://trashmail.com/?api=1&lang=en&cmd=read_logging_events>

*Returns the events of a specific log entry.

```javascript
// Input

// HTTP method: POST
// Expected properties: id
// Content-Type: application/x-www-form-urlencoded

"id:1353059"        // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:**

* None

***




### del\_logging

> <https://trashmail.com/?api=1&lang=en&cmd=del_logging>

*Deletes a specific entry from the log list.

```javascript
// Input

// HTTP method: POST
// Expected properties: idList
// Content-Type: application/x-www-form-urlencoded

"idList:[1337278]"      // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* Multiple IDs can be specified, these are separated by commas `[1,2,3]`.

***




### send\_logging\_emails

> <https://trashmail.com/?api=1&lang=en&cmd=send_logging_emails>

*Resends the email from the log to the specified email address.

```javascript
// Input

// HTTP method: POST
// Expected properties: idList, fwd
// Content-Type: application/x-www-form-urlencoded

"idList:[1337275]"                  // Key-Value x-www-form-urlencoded
"fwd:trashmail_1@example.email"     // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* Multiple IDs can be specified, these are separated by commas `[1,2,3]`.
* The fwd mail address must be a real email address stored in the account.

***




### load\_cs\_messages

> <https://trashmail.com/?api=1&lang=en&cmd=load_cs_messages>

*Returns the individual notifications (e.g. if confirmation code is required for delivery) for a given TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: data
// Content-Type: application/x-www-form-urlencoded

'data:["39730821","wl-confirm"]'        // Key-Value x-www-form-urlencoded
// wl-confirm, confirm-every, confirm-off
```

```javascript
// Output

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

**Notes:** **

* The ID specified in the request is not the "ID" of the TrashMail, but the "UID" of the TrashMail! Contained in the return of the DEAs: `{"id": "13549165", "uid": "39730389", ...`.

***




### save\_cs\_messages

> <https://trashmail.com/?api=1&lang=en&cmd=save_cs_messages>

*Saves a different version of the individual notifications (e.g. if confirmation code for delivery is required) for a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: param, name, from, subject, body
// Content-Type: application/x-www-form-urlencoded

'param:["39730821","wl-confirm"]'       // Key-Value x-www-form-urlencoded
'name:Testroboter'                      // Key-Value x-www-form-urlencoded
'from:testrobot@trashmail.com'          // Key-Value x-www-form-urlencoded
'subject:Test'                          // Key-Value x-www-form-urlencoded
'body:Hallo!'                           // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:** **

* The ID specified in the request is not the "ID" of the TrashMail, but the "UID" of the TrashMail! Contained in the return of the DEAs: `{"id": "13549165", "uid": "39730389", ...`.

***




### read\_whitelist

> <https://trashmail.com/?api=1&lang=en&cmd=read_whitelist>

*Returns all entries of the whitelist of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: user_id
// Content-Type: application/x-www-form-urlencoded

"user_id:39730389"      // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:** **

* The ID specified in the request is not the "ID" of the TrashMail, but the "UID" of the TrashMail! Contained in the return of the DEAs: `{"id": "13549165", "uid": "39730389", ...`.

***




### read\_whitelist\(Suche\)

> <https://trashmail.com/?api=1&lang=en&cmd=read_whitelist>

*Returns the whitelist entries of a given TrashMail that match the search criterion.

```javascript
// Input

// HTTP method: POST
// Expected properties: user_id, search
// Content-Type: application/x-www-form-urlencoded

"user_id:39730389"      // Key-Value x-www-form-urlencoded
"search:test"           // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:** **

* The ID specified in the request is not the "ID" of the TrashMail, but the "UID" of the TrashMail! Contained in the return of the DEAs: `{"id": "13549165", "uid": "39730389", ...`.

***




### whitelist\_add

> <https://trashmail.com/?api=1&lang=en&cmd=whitelist_add>

*Adds an email address to the whitelist of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: data
// Content-Type: application/x-www-form-urlencoded

'data:["39730389","example@example.email"]'     // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:** **

* The ID specified in the request is not the "ID" of the TrashMail, but the "UID" of the TrashMail! Contained in the return of the DEAs: `{"id": "13549165", "uid": "39730389", ...`.

***




### whitelist\_delete

> <https://trashmail.com/?api=1&lang=en&cmd=whitelist_delete>

*Removes an email address from the whitelist of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: data
// Content-Type: application/x-www-form-urlencoded

"data:45618"        // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* The data id is the `id` returned by `read_whitelist`.

***




### add\_blacklist

> <https://trashmail.com/?api=1&lang=en&cmd=add_blacklist>

*Adds an email address to the blacklist of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: id, description, mail_from, subject, body
// Content-Type: application/x-www-form-urlencoded

"id:13544576"                           // Key-Value x-www-form-urlencoded
"description:Testregel"                 // Key-Value x-www-form-urlencoded
"mail_from:tester@example.email"        // Key-Value x-www-form-urlencoded
"subject:"                              // Key-Value x-www-form-urlencoded
"body:"                                 // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***




### del\_blacklist

> <https://trashmail.com/?api=1&lang=en&cmd=del_blacklist>

*Removes an email address from the blacklist of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: id
// Content-Type: application/x-www-form-urlencoded

"id:297"        // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* The `id` is returned for `read_blacklist`.

***




### read\_blacklist

> <https://trashmail.com/?api=1&lang=en&cmd=read_blacklist>

*Returns all blacklist entries of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: id
// Content-Type: application/x-www-form-urlencoded

"id:13544576"       // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:**

* None

***




### read\_blacklist\(Suche\)

> <https://trashmail.com/?api=1&lang=en&cmd=read_blacklist>

*Returns the blacklist entries of a given TrashMail that match the search criterion.

```javascript
// Input

// HTTP method: POST
// Expected properties: id, search
// Content-Type: application/x-www-form-urlencoded

"id:13544576"       // Key-Value x-www-form-urlencoded
"search:test"       // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:**

* None

***




### edit\_blacklist

> <https://trashmail.com/?api=1&lang=en&cmd=edit_blacklist>

*Edits an entry in the blacklist of a TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: id (einzig verpflichtend), description, mail_from, subject, body
// Content-Type: application/x-www-form-urlencoded

"id:299"                                            // Key-Value x-www-form-urlencoded
"description:Das ist eine neue Beschreibung!"       // Key-Value x-www-form-urlencoded
"subject:Hallo!"                                    // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* It is not always necessary to pass all values. It is sufficient if the value to be changed (e.g. `description`) is passed. However, it is mandatory to pass the `id` for server-side identification.

***




### read\_queue

> <https://trashmail.com/?api=1&lang=en&cmd=read_queue>

*Returns all queue entries of a given TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: user_id
// Content-Type: application/x-www-form-urlencoded

"user_id:39705691"      // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:** **

* The ID specified in the request is not the "ID" of the TrashMail, but the "UID" of the TrashMail! Contained in the return of the DEAs: `{"id": "13549165", "uid": "39730389", ...`.

***




### read\_queue\(Suche\)

> <https://trashmail.com/?api=1&lang=en&cmd=read_queue>

*Returns the queue entries of a given TrashMail that match the search criterion.

```javascript
// Input

// HTTP method: POST
// Expected properties: user_id, search
// Content-Type: application/x-www-form-urlencoded

"user_id:39705691"      // Key-Value x-www-form-urlencoded
"search:Zweiter"        // Key-Value x-www-form-urlencoded
```

```javascript
// Output

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

**Notes:** **

* The ID specified in the request is not the "ID" of the TrashMail, but the "UID" of the TrashMail! Contained in the return of the DEAs: `{"id": "13549165", "uid": "39730389", ...`.

***




### queue\_delete\_msg

> <https://trashmail.com/?api=1&lang=en&cmd=queue_delete_msg>

*Deletes a specific email from the queue of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: data
// Content-Type: application/x-www-form-urlencoded

"data:17797197"     // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* None

***





### queue\_delete\_all\_msg

> <https://trashmail.com/?api=1&lang=en&cmd=queue_delete_all_msg>

*Deletes all emails from the queue of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: data
// Content-Type: application/x-www-form-urlencoded

"data:39705691"     // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:** **

* The ID specified in the request is not the "ID" of the TrashMail, but the "UID" of the TrashMail! Contained in the return of the DEAs: `{"id": "13549165", "uid": "39730389", ...`.

***




### queue\_accept\_msg

> <https://trashmail.com/?api=1&lang=en&cmd=queue_accept_msg>

*Accepts an email from the queue of a specific TrashMail.

```javascript
// Input

// HTTP method: POST
// Expected properties: data
// Content-Type: application/x-www-form-urlencoded

"data:17797209"     // Key-Value x-www-form-urlencoded
```

```javascript
// Output

// Content-Type: application/json

{
   "success":true,      // Boolean
   "error_code":0       // Integer
}
```

**Notes:**

* All waiting emails from the same sender will be allowed through.

***
