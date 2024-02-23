# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.\
You will also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.


/// Needs restructuring - but will do < > 

React (create-react-app)
Install react-app-rewired into your application
npm
Yarn
pnpm
npm install --save-dev react-app-rewired


Check for the missing libraries in your build and included packages, and accordingly polyfill them. For Web3Auth, you just need to polyfill the buffer and process libraries
npm
Yarn
pnpm
npm install --save-dev buffer process


WARNING
There might be a possibility that you might need to polyfill more libraries, in case you're using any other blockchain library alongside Web3Auth that requires them. Generally the libraries like crypto-browserify, stream-browserify, browserify-zlib, assert, stream-http, https-browserify, os-browserify, url are the ones that might be required. crypto-browserify and stream-browserify being the most common polyfills.

Create config-overrides.js in the root of your project folder with the content:
const webpack = require("webpack");

module.exports = function override(config) {
  const fallback = config.resolve.fallback || {};
  Object.assign(fallback, {
    crypto: false, // require.resolve("crypto-browserify") can be polyfilled here if needed
    stream: false, // require.resolve("stream-browserify") can be polyfilled here if needed
    assert: false, // require.resolve("assert") can be polyfilled here if needed
    http: false, // require.resolve("stream-http") can be polyfilled here if needed
    https: false, // require.resolve("https-browserify") can be polyfilled here if needed
    os: false, // require.resolve("os-browserify") can be polyfilled here if needed
    url: false, // require.resolve("url") can be polyfilled here if needed
    zlib: false, // require.resolve("browserify-zlib") can be polyfilled here if needed
  });
  config.resolve.fallback = fallback;
  config.plugins = (config.plugins || []).concat([
    new webpack.ProvidePlugin({
      process: "process/browser",
      Buffer: ["buffer", "Buffer"],
    }),
  ]);
  config.ignoreWarnings = [/Failed to parse source map/];
  config.module.rules.push({
    test: /\.(js|mjs|jsx)$/,
    enforce: "pre",
    loader: require.resolve("source-map-loader"),
    resolve: {
      fullySpecified: false,
    },
  });
  return config;
};


Within package.json change the scripts field for start, build and test. Instead of react-scripts replace it with react-app-rewired
before
after
"scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
    "eject": "react-scripts eject"
},


The missing Nodejs polyfills should be included now and your app should be functional with web3.

NOTE
If you're using craco, similar changes need to be made to craco.config.js

Angular
Check for the missing libraries in your build and included packages, and accordingly polyfill them. For Web3Auth, you just need to polyfill the buffer and process libraries. For the rest of the libraries, we are installing a dummy module called empty-module which helps us get rid of the warnings while building the project.
npm
Yarn
pnpm
npm install --save-dev buffer process empty-module


WARNING
There might be a possibility that you might need to polyfill more libraries, in case you're using any other blockchain library alongside Web3Auth that requires them. Generally the libraries like crypto-browserify, stream-browserify, browserify-zlib, assert, stream-http, https-browserify, os-browserify, url are the ones that might be required. crypto-browserify and stream-browserify being the most common polyfills.

Within tsconfig.json add the following paths in compilerOptions so Webpack can get the correct dependencies
{
  "compilerOptions": {
    "paths" : {
      "crypto": ["./node_modules/empty-module"], // crypto-browserify can be polyfilled here if needed
      "stream": ["./node_modules/empty-module"], // stream-browserify can be polyfilled here if needed
      "assert": ["./node_modules/empty-module"], // assert can be polyfilled here if needed
      "http": ["./node_modules/empty-module"], // stream-http can be polyfilled here if needed
      "https": ["./node_modules/empty-module"], // https-browserify can be polyfilled here if needed
      "os": ["./node_modules/empty-module"], // os-browserify can be polyfilled here if needed
      "url": ["./node_modules/empty-module"], // url can be polyfilled here if needed
      "zlib": ["./node_modules/empty-module"], // browserify-zlib can be polyfilled here if needed
      "process": ["./node_modules/process"],
    }
  }
}


Add the following lines to polyfills.ts file:
(window as any).global = window;
global.Buffer = global.Buffer || require("buffer").Buffer;
global.process = global.process || require("process");


Vue.js
Check for the missing libraries in your build and included packages, and accordingly polyfill them. For Web3Auth, you just need to polyfill the buffer and process libraries
npm
Yarn
pnpm
npm install --save-dev buffer process


WARNING
There might be a possibility that you might need to polyfill more libraries, in case you're using any other blockchain library alongside Web3Auth that requires them. Generally the libraries like crypto-browserify, stream-browserify, browserify-zlib, assert, stream-http, https-browserify, os-browserify, url are the ones that might be required. crypto-browserify and stream-browserify being the most common polyfills.

Add the following lines to vue.config.js
const { defineConfig } = require("@vue/cli-service");
const { ProvidePlugin } = require("webpack");
const { BundleAnalyzerPlugin } = require("webpack-bundle-analyzer");
module.exports = defineConfig({
  transpileDependencies: true,
  lintOnSave: false,
  configureWebpack: (config) => {
    config.devtool = "source-map";
    config.resolve.symlinks = false;
    config.resolve.fallback = {
      crypto: false, // crypto-browserify can be polyfilled here if needed
      stream: false, // stream-browserify can be polyfilled here if needed
      assert: false, // assert can be polyfilled here if needed
      os: false, // os-browserify can be polyfilled here if needed
      https: false, // https-browserify can be polyfilled here if needed
      http: false, // stream-http can be polyfilled here if needed
      url: "url", // url is needed if using `signer.provider.send` method for signing from ethers.js
      zlib: false, // browserify-zlib can be polyfilled here if needed
    };
    config.plugins.push(new ProvidePlugin({ Buffer: ["buffer", "Buffer"] }));
    config.plugins.push(new ProvidePlugin({ process: ["process/browser"] }));
    config.plugins.push(
      new BundleAnalyzerPlugin({
        analyzerMode: "disabled",
      }),
    );
  },
});


Gatsby
Can’t resolve object.assign/polyfill ?
Check for the missing libraries in your build and included packages, and accordingly polyfill them. For Web3Auth, you just need to polyfill the buffer and process libraries
npm
Yarn
pnpm
npm install --save-dev buffer process


WARNING
There might be a possibility that you might need to polyfill more libraries, in case you're using any other blockchain library alongside Web3Auth that requires them. Generally the libraries like crypto-browserify, stream-browserify, browserify-zlib, assert, stream-http, https-browserify, os-browserify, url are the ones that might be required. crypto-browserify and stream-browserify being the most common polyfills.

Add the following lines to gatsby-node.js
exports.onCreateWebpackConfig = ({ actions, plugins, getConfig }) => {
  const webpack = require("webpack");
  const path = require("path");
  const config = getConfig();
  if (config.externals && config.externals[0]) {
    config.externals[0]["node:crypto"] = require.resolve("crypto-browserify");
  }
  actions.setWebpackConfig({
    ...config,
    resolve: {
      fallback: {
        crypto: false,
        stream: false,
        assert: require.resolve("assert/"),
        http: false,
        https: false,
        os: false,
        url: false,
        zlib: false,
        "object.assign/polyfill": path.resolve("./node_modules/object.assign/polyfill.js"),
      },
    },
    plugins: [
      plugins.provide({ process: "process/browser" }),
      new webpack.ProvidePlugin({
        Buffer: ["buffer", "Buffer"],
      }),
    ],
  });
};
