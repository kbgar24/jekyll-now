---
layout: post
title: How to Build a Chrome Extension (Part I)
---

This section (Part I) will provide a high-level overview of the basic structure of a Google Chrome Extension.

# Introduction

Google Chrome Extensions (Chrome extensions) are quite powerful - the extent to which cannot be fully appreciated without having built one. Chrome extensions can access and manipulate virtually any element on the DOM, send desktop notifications, store data, send external HTTP, server && database requests, and (perhaps most usefully) tightly && elegantly integrate with Google Chrome's robust suite of API's. They can also run in the background, regardless of whether Chrome itself has been opened by the user. The main components on a Chrome extensions are the **manifest.json** file, **popup.html, popup.css, && popup.js** files, **background pages** *and/or* **event pages**, **browser actions** *or* **page actions**, and **content scripts**.

# Manifest.json

This file is the heart of any Chrome extension and provides important information about the extension's configuration. Below is a sample **manifest.json** file, with the required, recommended && optional fields specified:

```
{
  // Required
  "manifest_version": 2,
  "name": "My Extension",
  "version": "versionString",

  // Recommended
  "default_locale": "en",
  "description": "A plain text description",
  "icons": {...},

  // Pick one (or none)
  "browser_action": {...},
  "page_action": {...},

  // Optional
  "author": ...,
  "automation": ...,
  "background": {
    // Recommended
    "persistent": false
  },
  "background_page": ...,
  "chrome_settings_overrides": {...},
  "chrome_ui_overrides": {
    "bookmarks_ui": {
      "remove_bookmark_shortcut": true,
      "remove_button": true
    }
  },
  "chrome_url_overrides": {...},
  "commands": {...},
  "content_capabilities": ...,
  "content_scripts": [{...}],
  "content_security_policy": "policyString",
  "converted_from_user_script": ...,
  "current_locale": ...,
  "declarative_net_request": ...,
  "devtools_page": "devtools.html",
  "event_rules": [{...}],
  "externally_connectable": {
    "matches": ["*://*.example.com/*"]
  },
  "file_browser_handlers": [...],
  "file_system_provider_capabilities": {
    "configurable": true,
    "multiple_mounts": true,
    "source": "network"
  },
  "homepage_url": "http://path/to/homepage",
  "import": [{"id": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"}],
  "incognito": "spanning, split, or not_allowed",
  "input_components": ...,
  "key": "publicKey",
  "minimum_chrome_version": "versionString",
  "nacl_modules": [...],
  "oauth2": ...,
  "offline_enabled": true,
  "omnibox": {
    "keyword": "aString"
  },
  "optional_permissions": ["tabs"],
  "options_page": "options.html",
  "options_ui": {
    "chrome_style": true,
    "page": "options.html"
  },
  "permissions": ["tabs"],
  "platforms": ...,
  "plugins": [...],
  "requirements": {...},
  "sandbox": [...],
  "short_name": "Short Name",
  "signature": ...,
  "spellcheck": ...,
  "storage": {
    "managed_schema": "schema.json"
  },
  "system_indicator": ...,
  "tts_engine": {...},
  "update_url": "http://path/to/updateInfo.xml",
  "version_name": "aString",
  "web_accessible_resources": [...]
}
```

# Popup Files

If you would like a menu to open when your Chrome extension is clicked, you must build the UI & logic for such through the use of **popup.html**, **popup.css**, and **popup.js** files. In-line or internal style sheets (within **popup.html**) can be used in lieu of an actual **popup.css** file.

It is important to note that the code running within **popup.js** can only manipulate the popup. It has **no effect** on the DOM. The popup can be debugged by right-clicking on the Chrome extensions icon in the Chrome bar and selecting *Inspect Popup*. This will open a separate dev console specifically for the popup. Any console.log's or errors from **popup.js** will appear here.

If you'd like to communicate between the popup and the DOM, you'll need to dispatch messages through the **chrome.tabs.sendMessage** API and listen to them by adding listener(s) to the **chrome.runtime.onMessage** API.

Ideally, the popup should be a dumb UI component, and the logic within **popup.js** should reflect this. State management and application behavior should instead be managed through **background** or **event pages**. Having said that, **popup.js** does have access to any variables or functions declared within the **background/event pages**.

# Background and Event Pages

**Background** or **event pages** are the workhouse of a Chrome Extension, able to communicate with the popup, outside resources (such as HTTP requests), and the DOM (via communication with **content scripts**). This is where the state of the application is managed, as well as where most tasks should be performed.

There is an important distinction between the two - **background pages** are always active in memory, whereas **event pages** persist only when actively performing some action. When not in use, **event pages** are unloaded, freeing memory and other system resources. This can prove to be significantly more performant that always-on **background pages**. Chrome recommends using **event pages** wherever possible, reserving the use of **background pages** for cases wherein such always-on functionality is critical.

**Background/Event pages** are registered in the **manifest.json** file as such:

```
{
  "name": "My extension",
  ...
  "background": {
    "scripts": ["eventPage.js"],
    "persistent": false
  },
  ...
}
```
If no *persistent* property is provided, or if it is set to *true*, then the script specified in the *scripts* array will be a **background page**. Otherwise, the script will be run as an **event page**.

While performant, **event pages** do require that any global variables contained within them be stored in storage, such as the Chrome Storage API, if such variables need to be accessed while the **event page** is not in active.

# Browser Actions and Page Actions

You have the option of specifying your Chrome extension to be either a **browser action**, a **page action**, or neither. A **browser action** is intended to run on all (permissible) webpages. A **page action** is designed to run only on webpages. If your Chrome extension would not be useful to the User on all pages, it is preferable to use a **page action**.

Per Google Chrome's specs, all installed Chrome extensions must be visible in the Chrome bar (or in the Chrome extension overflow menu), however inactive **page action** extensions will be greyed out.

Both **browser actions** and **page actions** should include, in the **manfiest.json** file, *default_icon*, *default_popup*, and *default_title* properties:

```
{
  "name": "My extension",
  ...
  "page_action": {
    "default_icon": "icon16.png",
    "default_popup": "popup.html",
    "default_title": "PageFontStyle"
  },
  ...
}
```

# Content Scripts

**Content scripts** are powerful tools for injecting JavaScript (as well as CSS) directly into the DOM. They run in a 'protected' environment that prevents them from directly accessing most outside resources. They can, however, dispatch/receive messages to/from the **background/event pages** and **popup.js** files using the *chrome.runtime.sendMessage* and *chrome.runtime.onMessage* APIs.

**Content scripts** are declared within the **manifest.json** file as such:

```
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["http://www.google.com/*"],
      "css": ["mystyles.css"],
      "js": ["jquery.js", "myscript.js"]
    }
  ],
  ...
}
```
The *matches* properties defines on which domains the **content scripts** should be injected. Of note, *all_urls* (placed between two chevrons) is the expression for injecting your **content scripts** into all pages the User might visit.

Given the broad capabilities that **content scripts** offer developers, it is little wonder that using them requires specific permission from the User. This permission is 'requested' by including the matched domains in the *permissions* array in the **manifest.json** file:

```
{
  "name": "My extension",
  ...
  "content_scripts": [
    {
      "matches": ["http://www.google.com/*"],
      "css": ["mystyles.css"],
      "js": ["jquery.js", "myscript.js"]
    }
  ],
  ...
  "permissions": [
    "http://www.google.com"
  ]
}
```

Because of their expressive power, it is important to ensure that your **content scripts** do not introduce security vulnerabilities. This is particularly the case when the **content script** receives data from an external source, such as another website.

# Final Thoughts

Hopefully this broad overview of the main pieces that make up a Chrome Extension was helpful in laying a general framework for how  extensions operate. In Part II of this post, we will be employing these concepts to build our own Chrome Extension from scratch.

[*Object.create()*]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create
[syntactic sugar]: https://en.wikipedia.org/wiki/Syntactic_sugar
