---
layout: post
title: How to Build a Chrome Extension (Part I)
---

This section (Part I) will provide an overview of the basic structure of a Google Chrome Extension.

# Introduction

Google Chrome Extensions (CE's) are quite powerful - the extent to which cannot be fully appreciated without having built one. CE's can access and manipulate virtually any element on the DOM, store data, send external HTTP, server, database requests, and (perhaps most usefully) tightly && elegantly integrate with Google Chrome's robost suite of API's. The main components on a CE are the **manifest.json** file, **popup.html, popup.css, && popup.js** files,  **browser actions** *or* **page actions**, **background pages** *and/or* **event pages**, and **content scripts**.

# Manifest.json

This file is the heart of any CE and provides important information about the extension's configuration. Below is a sample **manifest.json** file, with the required, recommended && optional fields specified:

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

If you would a menu to open when your Chrome extension is clicked, you must build the UI & logic for such through the use of **popup.html**, **popup.css**, and **popup.js** files. In-linen or internal style sheets (within **popup.html**) can be used in lieu of an actual **popup.css** file.

It is important to note that the code running within **popup.js** can only manipulate the popup. It has **no effect** on the DOM. The popup can be debugged by right-clicking on the CE's icon in the Chrome bar and selecting *Inspect Popup*. This will open a separate dev console specifically for the popup. Any console.log's from **popup.js** will appear here.

If you'd like to communicate between the popup and the DOM, you'll need to dispatch messages through the **chrome.tabs.sendMessage** API and listen to them by adding listener(s) to the **chrome.runtime.onMessage** API.

Ideally, the popup should be a dumb UI component, and the logic within **popup.js** should reflect this. State management and application behavior should instead be managed through **background** or **event pages**. Having said that, **popup.js** does have access to any variables or functions declared within the **background/event pages**. 

# Final Thoughts

I recommend using the functional instantiation pattern for code clarity and to avoid unnecessary usage of the **this** keyword. However, if many, many instances of an object are needed, and they share the same methods, then using the pseudoclassical pattern is your best bet.

[*Object.create()*]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create
[syntactic sugar]: https://en.wikipedia.org/wiki/Syntactic_sugar
