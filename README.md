# React Native using shadow-cljs in 3 minutes

The fastest way a [ClojureScript](https://clojurescript.org/) coder can get started with React Native. *Prove me wrong.*

This is an example project using: [shadow-cljs](https://github.com/thheller/shadow-cljs), [React Native](https://facebook.github.io/react-native/), [Expo](https://expo.io/), [Reagent](https://reagent-project.github.io/), and [re-frame](https://github.com/Day8/re-frame).

Here follows instructions for getting started either using the command line or [Calva](http://github.com/BetterThanTomorrow/calva), assuming you have stuff like XCode, or whatever is the Android equivalents, installed:

## Command line
```sh
$ npm install -g expo-cli
$ yarn
$ shadow-cljs watch app
# wait for first compile to finish or expo gets confused
$ yarn start
```

Then use your editor of choice to hook up the REPL and such.

## Using Calva

Open the project and some `cljs` or `edn` file. Then:

1. In a Terminal pane, execute `npm install -g expo-cli`, then `yarn` and wait for it to finish.
1. Start build task **Watch CLJS** and wait for it to build the project.
1. Start build task **Start Expo** and wait for it to fire up Expo in your browser.
   1. Start the app on your phone or in a simulator.
   1. In the Expo settings for your app (shake or force touch with two fingers), disable Live Reloadinhg and Hot Reloading. (Don't worry, shadow-cljs will take care of that for you.)
1. Connect Calva to the shadow-cljs app (`ctrl+alt+v c`) and choose to connect the `:app` build.


## Production builds

A production build invloves first asking shadow-cljs to build a release, then to ask Expo to work in Production Mode.

**NB**: Currently there's a [bug in the metro bundler](https://github.com/facebook/metro/issues/291) that causes release builds to fail in Production Mode. This project includes a way to patch it (nicked from [here](https://github.com/drapanjanas/re-natal/issues/203)). Patch by executing: `patch node_modules/metro/src/JSTransformer/worker.js ./etc/metro-bundler.patch`

1. Kill the watch and expo tasks.
1. Execute `shadow-cljs release app`
1. Start the expo task (as per above)
   1. Enable Production mode.
   1. Start the app.

If you get complaints about [Module HMRClient is not a registered callable module](https://github.com/expo/expo/issues/916)*, you probably have **Hot reloading** enabled. Disable it and try again.

## Some notes from Thomas Heller

(This project is built from this example of his: https://github.com/thheller/reagent-expo)

The `:app` build will create an `app/index.js`. In `release` mode that is the only file needed. In dev mode the `app` directory will contain many more `.js` files.

`:init-fn` is called after all files are loaded and in the case of `expo` must render something synchronously as it will otherwise complain about a missing root component. The `shadow.expo/render-root` takes care of registration and setup.

You should disable the `expo` live reload stuff and let `shadow-cljs` handle that instead as they will otherwise interfere with each other.

Source maps don't seem to work properly. `metro` propably doesn't read input source maps when converting sources as things are correctly mapped to the source .js files but not their sources.

Initial load in dev is quite slow since `metro` processes the generated `.js` files.

`reagent.core` loads `reagent.dom` which will load `react-dom` which we don't have or need. Including the `src/main/reagent/dom.cljs` to create an empty shell. Copied from [re-natal](https://github.com/drapanjanas/re-natal/blob/master/resources/cljs-reagent6/reagent_dom.cljs).
