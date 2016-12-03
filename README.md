## Omni Plus
Ultimate Omnibox tools for Chrome.

The goal of this extension is to provide some handy utilities that can be accessed right in the Chrome Omnibox.

#### Basic outline:

###### Core:

`namespace Omnibox`  

Input listener and dispatcher. Also handling new command register.

 `namespace Notification`

Send notifications about results. Provides pre-defined notification types.

 `namespace Messaging`

*Handling external command register.*

###### Pre-defined commands:

`namespace Management`

Extensions related stuff, launch, enable/disable.

`namespace BrowserBasic`

History, bookmark, top sites, tabs, *Chrome urls*.

`namespace Time`

*Set a reminder*.

`namespace Misc`

Exverything else.



#### How to extend:

###### `Omnibox.register()`

```javascript
Omnibox.register({
  key: string, //The command text
  reg?: RegExp, //The regEx for evaluating user input
  format?: string, //The help text of correct input format, if any
  description: string, //An explaination of what this command gonna do
  input?: (content: string, suggest: (suggesResults: chrome.omnibox.SuggestResult[]) => void) => void, //This function receive user input and return suggestions
  accept: (content?: string) => void, //Once user hit enter, this function handles everything else
  extensionId?: string //WIP Identify an external command
})
```

When providing suggestions, there're three possible situations:

1. `input() => void`

   Whenever `input()` function is provided, Omni Plus will use it to provide suggestions.

2. No `input()` but  `reg` && `format` 

   Omni Plus will check the user input using `reg` , when user got things wrong, it'll sugegst `Correct Fomat {format}`.

3. No `input()` and `reg` || `format`

   Omni Plus will use `description` as the only suggestion.

###### `providerItem`

All registered command will be presented as `new providerItem(info)`

```javascript
class providerItem {
  public key: string;
  public reg?: RegExp;
  public format?: string;
  public description: string;
  public input?: (content: string, suggest: (suggesResults: chrome.omnibox.SuggestResult[]) => void) => void;
  public accept: (content?: string) => void;
  public extensionId?: string;
  //misc properties and utility functions
  misc: {
    urlReg: RegExp,
    encodeXml (str: string): string
  }
  //search provided data and return formatted suggestions array
  search(data: any[], input: string, searchKeys: string[], suggestKeys: string[],
    callback: (response: chrome.omnibox.SuggestResult[]) => void, condition?: any[]) => void;
  //return suggestions array out of data without searching
  form(data: any[], suggestKeys: string[],
    callback: (response: chrome.omnibox.SuggestResult[]) => void) => void;
  //return input content by removing key text
  extract(content: string): string;
  //destroy provider
  _() => void;
}
```

When building the `input()` and `accept()` , there're some handy functions and misc stuff provided. Could be accessed using `this.` or `let self = this; self.`