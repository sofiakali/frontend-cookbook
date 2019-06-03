# 📊 Google Analytics

[The ultimate guide to Google Analytics for UX designers](https://uxdesign.cc/google-analytics-ux-alice-emma-walker-958d6f0f0af3)

## Tracking user interaction
Sometimes, it's good to track not only web page view, but other user interactions such as button clicks. This is especially important in Single Page Applications (SPA) as they often do not have traditional pages, but dynamic content.

### Virtual pages
[Documentation](https://developers.google.com/analytics/devguides/collection/analyticsjs/single-page-applications)

This is useful for tracking dynamic parts of SPA such as tabs, collapsible content or sliders (carousels). To track such part of an app add following line of code to `onClick` event of element that triggers the corresponding content.

```javascript
ga('set', 'page', page); // Set virtual page
ga('send', 'pageview'); // Send it to GA
```

### Events
[Documentation](https://developers.google.com/analytics/devguides/collection/analyticsjs/events)

Events are good for tracking interactive elements such as buttons, links, etc, that do not redirect to other subpages.

```javascript
ga('send', 'event', 'Homepage', 'Slider', 'Slide left');
```