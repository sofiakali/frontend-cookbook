# Deeplinks to mobile application

Generally term **deeplink** is defined as

> A deep link is a hypertext link to a page on a Web site other than its home page.

But in this recipe we will be talking about deep links in context of mobile applications, so definition would be 

> **hypertext link that opens linked location with related mobile app if installed in phone**.

## How to deeplink

All you need to do is to make few static files publicly accessible at your webpage.

There are two files:

* `apple-app-site-association.json` for iOS
* `assetlinks.json` for Android

which you should put, according to [the standard](https://en.wikipedia.org/wiki/List_of_/.well-known/_services_offered_by_webservers), to the `/.well-known/` folder at your website. 
Content of the files is up to iOS, Android developers, but you can view their [examples at the end of this recipe](#app-files-examples)


```jsx
<Link
    className={styles.link}
    to="https://microsite.flashsport.com"
    rel="nofollow"
>
    Get better experience with our app
</Link>
```

Where `https://microsite.flashsport.com` is fallback url that is opened if native application cannot been opened, eg. because it's not installed. **Its value is important, because Android/iOS app developers need to allow it in their apps.**

### Observations

* Attribute `rel="nofollow"` was key attribute to make automatic opening Android app. Without it, popup asking you which app you want to use for opening the link appear.
* At least iOS apps **don't allow deep link to lead to the same domain**, you need to use subdomain or completly different domain.  
For example if your're linking from `https://flashsport.com`, your link cannot be something like `https://flashsport.com/deeplink` but rather `https://deeplink.flashsport.com` or `https://fs-deeplink.com` which can be a microsite telling you there is native app.


### App Files examples

#### `assetlinks.json` (Android)
```json
[
  {
    "relation": [
      "delegate_permission/common.handle_all_urls"
    ],
    "target": {
      "namespace": "android_app",
      "package_name": "cz.cookbook.debug",
      "sha256_cert_fingerprints": [
        "2B:CB:7D:17:81:23:C7:BF:21:31:74:B1:CD:D9:06:7C:54:F0:1D:A7:DF:CB:BD:61:DB:03:18:C3:42:B1:B8:78"
      ]
    }
  },
  {
    "relation": [
      "delegate_permission/common.handle_all_urls"
    ],
    "target": {
      "namespace": "android_app",
      "package_name": "cz.cookbook.beta",
      "sha256_cert_fingerprints": [
        "2B:CB:7D:17:81:23:C7:BF:21:31:74:B1:CD:D9:06:7C:54:F0:1D:A7:DF:CB:BD:61:DB:03:18:C3:42:B1:B8:78"
      ]
    }
  }
]
```

#### `apple-app-site-association` (iOS)

```json
{
  "applinks": {
    "apps": [],
    "details": [
      {
        "appID": "PAGI69Y0CD.cz.ackee.enterprise.cookbook.dev",
        "paths": ["/*"]
      },
      {
        "appID": "PAGI69Y0CD.cz.ackee.enterprise.cookbook.beta",
        "paths": ["/*"]
      }
    ]
  }
}
```

----

### Resources
* https://medium.com/@ageitgey/everything-you-need-to-know-about-implementing-ios-and-android-mobile-deep-linking-f4348b265b49
* https://moz.com/blog/how-to-get-your-app-content-indexed-by-google
* https://www.deepcrawl.com/blog/best-practice/app-deep-linking-for-beginners-google-app-indexing-facebook-app-links/


