# Gatsby Segment Js - plugin


*This plugin extends the core gatsby-plugin-segment-js and adds an option to stop the analytics.load() happening
on page render. This allows you to create functionality for the web user to set their cookie preferences, in compliance with the EU Cookie Law.*



A lightweight & feature-rich Gatsby plugin to easily add [Segment JS snippet](https://segment.com/docs/sources/website/analytics.js/quickstart/) to your site.

## Features

Packed with features:

-   use multiple write keys (one for prod env, another optional one for dev)
-   disable page view tracking (just in case you want to add it later manually)
-   up to date (Segment snippet 4.1.0)

## Install

-   NPM: `$ yarn add gatsby-segment-js`
-   YARN: `$ npm install --save gatsby-segment-js`

## How to use

### Setup

In your gatsby-config.js file:

```javascript
plugins: [
    {
        resolve: `gatsby-segment-js`,
        options: {
            // your segment write key for your production environment
            // when process.env.NODE_ENV === 'production'
            // required; non-empty string
            prodKey: `SEGMENT_PRODUCTION_WRITE_KEY`,

            // if you have a development env for your segment account, paste that key here
            // when process.env.NODE_ENV === 'development'
            // optional; non-empty string
            devKey: `SEGMENT_DEV_WRITE_KEY`,

            // boolean (defaults to false) on whether you want
            // to include analytics.page() automatically
            // if false, see below on how to track pageviews manually
            trackPage: false
            // change this to true for standard settings
            // or keep as false to call this manually
            // this effectively pauses the scripts and cookies form loading
            loadOnRender: false,
        }
    }
];
```

### Track Events

Manually begin the load scripts by calling window.analytics.load(writeKey)
If you have a more complex configuration which enables users to select
between different levels of cookies (analytics / tracking / marketing)
you can pass an object to load which sets individual apps.

analytics.load('writekey', { integrations: { All: false, 'Google Analytics': true, 'Segment.io': true } })

[See Segment docs for more info](https://segment.com/docs/sources/website/analytics.js/#load-options)

### Track Events

If you want to track events, you simply invoke Segment as normal in your React components — (`window.analytics.track('Event Name', {...})` — and you should see the events within your Segment debugger! For example, if you wanted to track events on a click, it may look something like this:

```javascript
class IndexPage extends React.Component {
    ...
    _handleClick() {
        window.analytics.track("Track Event Fired", {
            userId: user.id,
            gender: 'male',
            age: 33,
        });
    }
    render() {
        return (
            <p>
                <Link onClick={this._handleClick} to="/">
                    Click here
                </Link>{" "}
                to see a track event
            </p>
        );
    }
}
```

### Track Pageviews

If you want to track pageviews automatically, set `trackPage` to `true` in your `gatsby-config.js` file. What we mean by _"automatically"_ is that whenever there is a route change, we leverage Gatsby's `onRouteUpdate` API in the `gatsby-browser.js` file ([link](https://www.gatsbyjs.org/docs/browser-apis/#onRouteUpdate)) to invoke `window.analytics.page()` on each route change. But if you want to pass in properties along with the pageview call (ie, it's common to see folks pass in some user or account data with each page call), then you'll have to set `trackPage: false` and call it yourself in your `gatsby-browser.js` file, like this:

```javascript
// gatsby-browser.js
exports.onRouteUpdate = () => {
    window.analytics && window.analytics.page();
};
```
