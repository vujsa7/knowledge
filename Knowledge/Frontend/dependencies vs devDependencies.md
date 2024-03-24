Technically speaking, it really doesn’t matter. The separation of dependencies vs dev dependencies only matters for **published packages**, so that if you use this package as a dependency of your project, you don’t have to download unnecessary dependencies. 

**In other words, it’s useful for libraries, not apps.**

If you're developing an app you can place everything into devDependencies. When you bundle (build) the app for production, the compiler/bundler will compile your code and output `/dist` folder that will contain JavaScript code that will run on production.