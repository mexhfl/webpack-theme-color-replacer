This plugin can extract theme color styles from all the outputed css files (such as element-ui theme colors), and make a 'theme-colors.css' file which only contains color styles. At runtime in your web page, the client part will help you to download this css file, and then replace the colors into new customized colors dynamicly.

This is a sample:
https://hzsrc-vue-webpack4-elementui.netlify.com/themeColor.html

Implementation (Chinese):
https://segmentfault.com/a/1190000016061608

# 1.Install
npm i -D webpack-theme-color-replacer

# 2.Cofig for webpack

````js

const ThemeColorReplacer = require('webpack-theme-color-replacer')

module.exports = {
    // ..... other config
    plugins: [
        new ThemeColorReplacer({
            matchColors: ['#ed4040', '#4b0', '255,80,80'], // colors array for extracting css file
            fileName: 'css/theme-colors-[contenthash:8].css', //optional. output css file name, suport [contenthash] and [hash].
            // resolveCss(resultCss) { // optional. Resolve result css code as you wish.
            //     return resultCss.replace(/#ccc/g, '#eee')
            // },
            externalCssFiles: ['./node_modules/element-ui/lib/theme-chalk/index.css'], // optional, String or string array. Set external css files (such as cdn css) to extract colors.
            // changeSelector(selector, util) { // optional, Funciton. Changing css selectors, in order to raise css priority, to resolve lazy-loading problems.
            //     return util.changeEach(selector, '.el-button--default')
            // },
            injectCss: false, // optional. Inject css text in js file, not need to download `theme-colors-xxx.css` any more.
            isJsUgly: process.env.NODE_ENV !== 'development', // optional. Set to `true` if your js is uglified. Default is set by process.env.NODE_ENV.
        })
    ],
}
````

You can view this sample:
https://github.com/hzsrc/vue-element-ui-scaffold-webpack4/blob/master/build/webpack.base.conf.js

---
* Other build tool(not webpack)

Otherwise, if you use other build tool(like gulp) but not webpack, you can use this plugin by nodejs:
````js
var nodeRun = require('webpack-theme-color-replacer/src/nodeRun');
var {code, outFile} = nodeRun({
    src: 'src/css/**/*.css', // source css files. string or string array, as `glob`.
    matchColors: ['#ed4040', '#4b0', '255,80,80'],
    fileName: 'css/theme-colors-[contenthash:8].css',
    // ...
})
// do sth with outFile
````
See this sample: https://github.com/hzsrc/webpack-theme-color-replacer/blob/master/src/nodeRun.js

# 3.Usage in your runtime web page
Like this:

````js
import replacer from 'webpack-theme-color-replacer/client'

// change theme colors at runtime.
export function changeColor() {
  var options = {
    newColors: ['#f67a17', '#f67a17', '160,20,255'], // new colors array, one-to-one corresponde with `matchColors`
    // appendToEl: 'head', //optional. The element selector for appending child with `<style>`, default is 'body'. Using `appendToEl: 'body'` can make the css priority higher than any css in <head>
    // changeUrl(cssUrl) {
    //   return `/${cssUrl}`; // while router is not `hash` mode, it needs absolute path
    // },
  }

  replacer.changer.changeColor(options, Promise).then(() => {
      console.log('Theme colors changed!')
  })
}



````

You can view this sample:
https://github.com/hzsrc/vue-element-ui-scaffold-webpack4/blob/master/src/js/themeColorClient.js

# issues report
If you have issues with this plugin, please run your command with `--theme_debug` option, such as `npm run dev --theme_debug`, then upload the outputed `_tmp_xxx` files while reporting issues. Thanks!

#  update log
### 1.3.2
Default to append `<style>...</style>` to `<body>`, not to `<head>` any more, in order to resolve most priority problems in lazy loading pages. Thanks to [iota9star](https://github.com/iota9star) : [15](https://github.com/hzsrc/webpack-theme-color-replacer/issues/15)
