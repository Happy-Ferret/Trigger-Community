# Trigger Community
Repository of custom commands shared by users of the Trigger browser extension. Please do not manualy submit pull requests. Please use Trigger dashboard to create and submit user scripts.

# API
Here is the documentation on how to write custom commands (user scripts) for Trigger.

| Interfaces                      |
|:--------------------------------|
| **[browser](#browser)**         |
| **[system](#system)**           |

## browser
The complete `browser.*` interface of WebExtensions is available. You can read about the details here at [MDN :: WebExtensions - APIs](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API).

There is two critical difference.

1. Callbacks cannot be used.
2. All methods return a promise, even if they MDN documentation says they do not.

For example, in the Trigger localization file I have a string called `error`. Normally you would obtain this string by doing `let str_error = browser.i18n.getMessage('error')`, becuase MDN docs says this returns a string synchronously. However we are in the Trigger user script scope, so it returns a promise. Here are some ways to do this:

  > *ES5*
  > ```
  > browser.i18n.getMessage('error').then(function(result) {
  >     let str_error = result;
  > });
  > ```
  >
  > *ES7*
  > ```
  > let str_error = await browser.i18n.getMessage('error');
  > ```

## system
This branch offers a collection of modules similar to the `browser` API. The functionality here is focused on operating system specifics.

| Modules                         |
|:--------------------------------|
| **[file](#systemfile)**         |
| **[keyboard](#systemkeyboard)** |
| **[path](#systempath)**         |

### system.file
This module enables manipulating files on the system.

| Methods                                                     |
|:------------------------------------------------------------|
| **[launch]()** - `boolean  system.file.launch(string path)` |

### system.keyboard
This module works with the keyboard on a platform level.

| Methods                                                                           |
|:----------------------------------------------------------------------------------|
| **[sendKey]()** - `void  system.keyboard.sendKey(integer code, object modifiers)` |
| **[sendString]()** - `void  system.keyboard.sendString(string str)`               |

### system.path
This module provides information and methods on the system file paths.

| Types                     |
|:--------------------------|
| **[PathName](#pathname)** |

| Methods                                                    |
|:-----------------------------------------------------------|
| **[get](#get)** - `string  system.path.get(PathName name)` |


#### Types
##### PathName
The first argument passed to `getPath`. Identifies that you want special path on the system.

| Enum                                                        |
|:------------------------------------------------------------|
| "desktop", "temp", "home", "user_app_data", "program_files" |

* *Windows Only* - "user_app_data", "program_files"
* *Linux Only* - "home"

#### Methods
##### get
`string system.path.get(PathName name)`
Returns a string of the special system path associated with the `name`. If `name` is not valid then it throws.
