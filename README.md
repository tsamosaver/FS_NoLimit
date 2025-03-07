# FS_NoLimit for tampermonkey
Removes FS Image Download throttle limit

```javascript
// ==UserScript==
// @name         Modify SERVER_DATA
// @namespace    http://tampermonkey.net/
// @version      1.0
// @description  Change parameters in SERVER_DATA.initialDefaultEx
// @match        *://familysearch.org/*
// @match        *://www.familysearch.org/*
// @match        *://beta.familysearch.org/*
// @run-at       document-start
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    function modifyData() {
        if (window.SERVER_DATA && window.SERVER_DATA.initialDefaultEx) {
            let data = window.SERVER_DATA.initialDefaultEx;

            // tweaking
            data.apps["search-react"].features.llsDetailsAttach = true;
            data.apps["search-react"].features.proxyCatalogDetails = true;
            data.apps["search-react"].features.removeImageDownloadThrottle = true;
            data.apps["search-react"].features.showDevFeaturesEx = true;

            console.log("Tampermonkey: SERVER_DATA modified", data);
        }
    }

    // make Proxy foe SERVER_DATA
    const handler = {
        set(target, property, value) {
            target[property] = value;
            if (property === 'initialDefaultEx') {
                modifyData();
            }
            return true;
        }
    };

    // watch for SERVER_DATA
    const proxy = new Proxy(window.SERVER_DATA || {}, handler);
    Object.defineProperty(window, 'SERVER_DATA', {
        get() {
            return proxy;
        },
        set(value) {
            Object.assign(proxy, value);
        },
        configurable: true
    });

    // once it created - change it
    if (window.SERVER_DATA && window.SERVER_DATA.initialDefaultEx) {
        modifyData();
    }

    // some logs
    document.addEventListener('DOMContentLoaded', () => {
        console.log("Tampermonkey: I'm here");
    });
})();

```
