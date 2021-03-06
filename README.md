<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [What's this project?](#whats-this-project)
- [What changes were made?](#what-changes-were-made)
  - [Adding in a service worker](#adding-in-a-service-worker)
  - [Adding in a Web App Manifest](#adding-in-a-web-app-manifest)
  - [Adding in a GitHub deployment step](#adding-in-a-github-deployment-step)
- [What additional changes might be needed?](#what-additional-changes-might-be-needed)
  - [I've added in React Router and now my URLs don't work offline](#ive-added-in-react-router-and-now-my-urls-dont-work-offline)
  - [I'm using cross-origin APIs or resources, and they aren't working while offline](#im-using-cross-origin-apis-or-resources-and-they-arent-working-while-offline)
- [How can I try out the Progressive Web App?](#how-can-i-try-out-the-progressive-web-app)
- [How can I report bugs?](#how-can-i-report-bugs)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## What's this project?

This is one approach to taking a project created with the
[`create-react-app`](https://github.com/facebookincubator/create-react-app) tool
and adding in some additional bits commonly found in
[Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/).

## What changes were made?

GitHub's visual diff shows
[all the changes needed](https://github.com/jeffposnick/create-react-pwa/compare/starting-point...pwa)
to make a Progressive Web App. They include:

### Adding in a service worker

The service worker that's generated will ensure that the local images,
JavaScript, CSS, and HTML for your web app will be cached and continue to work,
even when a user is offline. It also will save bandwidth and improve performance
while users are online, by only making network requests for those local
resources when there's actually an update that's been deployed.

A [`sw-precache`](https://github.com/GoogleChrome/sw-precache) dependency was added to `package.json`, and the
`npm run build` command has been updated to call the `sw-precache` command-line
interface after the `webpack` build process is completed.

`sw-precache` generates a `service-worker.js` file that will automatically
cache the other static files in the `build/` directory, and keep them up to
date when you deploy changes.

Code to register the service worker was also added to `index.html`.

The service worker is only generated as part of the production build, so the
development environment will continue to work as before. When running a
production server locally, make sure you use a different port than `3000`, to
ensure the service worker does not inadvertantly take control of the development
environment.

### Adding in a Web App Manifest

A [web app manifest](https://developers.google.com/web/updates/2014/11/Support-for-installable-web-apps-with-webapp-manifest-in-chrome-38-for-Android?hl=en)
provides metadata about your web app. Along with a service worker, your web
app needs a manifest in order to trigger the Add to Homescreen prompt (in
supported browsers).

### Adding in a GitHub deployment step

While not strictly necessary, a new `npm run gh-pages` script was added in,
relying on the [`gh-pages`](https://www.npmjs.com/package/gh-pages) module.

Additionally, the `"homepage": "./"` property was set in `package.json`, to
ensure that [relative paths](https://github.com/facebookincubator/create-react-app/pull/94)
are used in the generated output. This plays nicely with both GitHub Pages
hosting and should work well with other hosting environments, too.

## What additional changes might be needed?

By following the [changes made](https://github.com/jeffposnick/create-react-pwa/compare/starting-point...pwa),
you should end up with a Progressive Web App using React that's ready to be
deployed to any static hosting environment. However, if you add in additional
functionality to the starting point, you may need to update your `sw-precache`
settings to ensure the service worker behaves properly.

### I've added in React Router and now my URLs don't work offline

If you've followed the suggestions in the [`create-react-app` documentation](https://github.com/jeffposnick/create-react-pwa/blob/539cddae29062db0fc0896444869a6bf1939f266/README.md#github-pages)
and added in [React Router](https://github.com/reactjs/react-router) using the
History API to manage URLs, then you need to tell the service worker that
navigations to all the random URLs your web app now supports should actually
be fulfilled with the cached copy of your `index.html`. You can do this
with the [`navigateFallback`](https://github.com/GoogleChrome/sw-precache#navigatefallback-string)
option in `sw-precache`.

Assuming you're using the `sw-precache` command-line interface, the additional
flag would look like `--navigate-fallback='index.html'`.

### I'm using cross-origin APIs or resources, and they aren't working while offline

The service worker generated by default by `sw-precache` only handles requests
for local, static resources, like your images, JavaScript, CSS, and HTML.
Requests made at runtime for, e.g., APIs or images that live on other servers
won't be handled by the default `sw-precache` setup. This means they will stop
working offline.

The [`sw-toolbox`](https://github.com/GoogleChrome/sw-toolbox) allows you to
set up runtime caching strategies, using URL patterns to determine what
strategy and cache sizes to use. `sw-precache` provides an easy way to use
`sw-toolbox` via the [`runtimeCaching`](https://github.com/GoogleChrome/sw-precache#runtimecaching-arrayobject)
configuration option.

Because the configuration can get complex, it's best not to attempt to provide
it via a command-line flag. Instead, you can transition to a JSON config file to
set up runtime caching, along with all the other `sw-precache` options. This
[sample config file](https://github.com/GoogleChrome/sw-precache/blob/master/demo/sw-precache-config.json)
can be used as a starting point, and you'd add in the
`--config-file=sw-precache-config.json` flag to your `build` script to get
`sw-precache` to start using that file.

## How can I try out the Progressive Web App?

The code from this project is deployed at https://jeffy.info/create-react-pwa/

## How can I report bugs?

If you've found a bug in the code output by `create-react-app`, please let the
project maintainers know in their [issue tracker](https://github.com/facebookincubator/create-react-app/issues).

If you've found an issue specific to the Progressive Web App bits (the service
worker, the Web App Manifest, etc.), then please let us know in this project's
[issue tracker](https://github.com/jeffposnick/create-react-pwa/issues).
