## Lesson 2: Tweaking the build to support Typescript

What we are going to do to convert the project from Javascript to TypeScript is to move the actions directory from the cli templated project setup and rename that folder action-src.  Then we convert all the exiting template code to TypeScript.  Next we add in a pre app run hook that will transpile the TypeScript into Javascript and put the results back in the /actions directory.  The stock build process will take it from there and deploy the actions.  

## Step one: add in project dependencies 
Add in [shx](https://www.npmjs.com/package/shx)
Shx is a npm package that helps us use shell commands in a cross-platform manner.  We will be using shx to help us clean up the actions directory between builds.
To install this npm module issue the following command from inside your projects home directory
```bash
npm install shx --save-dev
```

Add in typescript module
```bash
npm install typescript --save-dev
```

**Optional**
Install some standard type librarys
```bash
npm install "@types/mime-types" --save-dev
npm install "@types/node-fetch" --save-dev
npm install "@types/node" --save-dev
```

Install the TypeScript lint plugin
```bash
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

Add lint config and ignore
Create a new file in your project root named **.eslintrc**
In the new **.eslintrc** file put the following content  
```javascript
{
    "root": true,
    "parser": "@typescript-eslint/parser",
    "plugins": [
      "@typescript-eslint"
    ],
    "extends": [
      "eslint:recommended",
      "plugin:@typescript-eslint/eslint-recommended",
      "plugin:@typescript-eslint/recommended"
    ]
  }
```

Create a new file in your project root named **.eslintignore**
In the new **.eslintignore** file put the following content  
```javascript
node_modules
actions

# ignore web src if it exists
web-src
```

Add lint scripts to package.json
In the projects **package.json** add in the following lines to the scripts
```javascript
    "lint": "eslint \"actions-src/**/*.{js,ts}\" ",
    "lint-fix": "eslint \"actions-src/**/*.{js,ts}\" --quiet --fix"
```


## Step two: modify the build hooks
TypeScript will need some instructions on how you want it to transpile your TypeScript into Javascript and where to output it and all the nifty options to use.
In your project directory create the following file **tsconfig.json** 
Then in this new file put the following config.  I have included a lot of the options in the file but they are commented out.  This file is intended to be a starting point and it would be something you tune and modify over time. 
```javascript
{
    "compilerOptions": {
        /* Basic Options */
        "module": "commonjs",  /*Specify module code generation: "None", "CommonJS", "AMD", "System", "UMD", "ES6", "ES2015" or "ESNext".  Only "AMD" and "System" can be used in conjunction with --outFile. "ES6" and "ES2015" values may be used when targeting "ES5" or lower.*/
        "target": "es5",  /*Specify ECMAScript target version: "ES3" (default),"ES5","ES6"/"ES2015","ES2016","ES2017","ES2018","ES2019","ES2020","ESNext"*/
        //"lib": [ "es2015", "dom" ],  /*List of library files to be included in the compilation. see https://www.typescriptlang.org/docs/handbook/compiler-options.html */
        //"removeComments": true,
        //"resolveJsonModule": true,
        //"esModuleInterop": true,
        //"typeRoots" : [
        //   "./node_modules/@types/",
        //    "./typings/"
        //],
        // "lib": [],                             /* Specify library files to be included in the compilation. */
        "allowJs": false,                       /* Allow javascript files to be compiled. */
        "checkJs": false,                       /* Report errors in .js files. */
        // "jsx": "preserve",                     /* Specify JSX code generation: 'preserve', 'react-native', or 'react'. */
        "declaration": false,                   /* Generates corresponding '.d.ts' file. */
        // "declarationMap": true,                /* Generates a sourcemap for each corresponding '.d.ts' file. */
        "sourceMap": false,                     /* Generates corresponding '.map' file. */
        // "outFile": "./",                       /* Concatenate and emit output to single file. */
        "outDir": "actions",                        /* Redirect output structure to the directory. */
        // "rootDir": "./",                       /* Specify the root directory of input files. Use to control the output directory structure with --outDir. */
        // "composite": true,                     /* Enable project compilation */
        // "removeComments": true,                /* Do not emit comments to output. */
        // "noEmit": true,                        /* Do not emit outputs. */
        //"importHelpers": true,                 /* Import emit helpers from 'tslib'. */
        // "downlevelIteration": true,            /* Provide full support for iterables in 'for-of', spread, and destructuring when targeting 'ES5' or 'ES3'. */
        // "isolatedModules": true,               /* Transpile each file as a separate module (similar to 'ts.transpileModule'). */

        /* Strict Type-Checking Options */
        //"strict": true,                           /* Enable all strict type-checking options. */
        "noImplicitAny": false,                 /* Raise error on expressions and declarations with an implied 'any' type. */
        //"strictNullChecks": false,              /* Enable strict null checks. */
        // "strictFunctionTypes": true,           /* Enable strict checking of function types. */
        // "strictPropertyInitialization": true,  /* Enable strict checking of property initialization in classes. */
        // "noImplicitThis": true,                /* Raise error on 'this' expressions with an implied 'any' type. */
        // "alwaysStrict": true,                  /* Parse in strict mode and emit "use strict" for each source file. */
        
        /* Additional Checks */
        // "noUnusedLocals": true,                /* Report errors on unused locals. */
        // "noUnusedParameters": true,            /* Report errors on unused parameters. */
        // "noImplicitReturns": true,             /* Report error when not all code paths in function return a value. */
        // "noFallthroughCasesInSwitch": true,    /* Report errors for fallthrough cases in switch statement. */
        
        /* Module Resolution Options */
        "moduleResolution": "node",            /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
        // "baseUrl": "./",                       /* Base directory to resolve non-absolute module names. */
        // "paths": {},                           /* A series of entries which re-map imports to lookup locations relative to the 'baseUrl'. */
        // "rootDirs": [],                        /* List of root folders whose combined content represents the structure of the project at runtime. */
        // "typeRoots": [],                       /* List of folders to include type definitions from. */
        // "types": ["jest", "node"],                           /* Type declaration files to be included in compilation. */
        // "allowSyntheticDefaultImports": true,  /* Allow default imports from modules with no default export. This does not affect code emit, just typechecking. */
        "esModuleInterop": true                   /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */
        // "preserveSymlinks": true,              /* Do not resolve the real path of symlinks. */
        
        /* Source Map Options */
        // "sourceRoot": "",                      /* Specify the location where debugger should locate TypeScript files instead of source locations. */
        // "mapRoot": "",                         /* Specify the location where debugger should locate map files instead of generated locations. */
        // "inlineSourceMap": true,               /* Emit a single file with source maps instead of having a separate file. */
        // "inlineSources": true,                 /* Emit the source alongside the sourcemaps within a single file; requires '--inlineSourceMap' or '--sourceMap' to be set. */
        
        /* Experimental Options */
        // "experimentalDecorators": true,        /* Enables experimental support for ES7 decorators. */
        // "emitDecoratorMetadata": true,         /* Enables experimental support for emitting type metadata for decorators. */
    },
    "include": [
        "./actions-src/**/*"
    ],
    "exclude": [
        "dev-keys"
    ]
}
```
The importiant bits of this config file are the **include** telling it to include TypeScript found **/actions-src/** directory. The other importiant bit is telling the transpiler what **module** type, the target type **target**, and the **output** directory for the transpiled code.  

## Step three: modify the build hooks
Inside your project directory you will see a file named package.json.  Inside that file you will see a section named "scripts".  We are going to modify this to include two new scripts.  One for cleaning up the directorys we build into and another script to run before the aio run command.  
In package.json add in the following lines.
```javascript
"pre-app-run": "npm run clean && tsc"
```
pre-app-run: this is a hook that gets run before aio run.  Here we are telling it to run the clean script then run the TypeScript compiler.  To find out more about the application tooling lifecycle event hooks [read this](https://github.com/AdobeDocs/project-firefly/blob/master/guides/app-hooks.md).

and add this line to handle cleanup 
```javascript
"clean": "shx rm -rf actions && shx echo Action Cleaning Done"
```
This will use shx to remove recursivly the actions directory so we can put our new freshly transpiled TypeScript in there. 

When your done your package.json scripts section should look simular to this
```javascript
"scripts": {
    "pre-app-run": "npm run clean && tsc",
    "test": "jest --passWithNoTests --verbose ./test",
    "e2e": "jest --collectCoverage=false --testRegex ./e2e",
    "clean": "shx rm -rf actions && shx echo Action Cleaning Done"
  }
```

## Step four: test and modify gitignore

To avoid checking in transpiled output you will want to modify **.gitignore** to include the TypeScript output directory.  In this case its the  actions directory so we will add that to the **.gitignore** config.
The contents of **.gitignore** should look simular to this when your done.
```javascript

# package directories
node_modules
jspm_packages

# build
build
dist
.manifest-dist.yml

# Config
config.json
.env*

# Adobe I/O console config
console.json

# Test output
junit.xml

# IDE & Temp
.cache
.idea
.nyc_output
.vscode
coverage
.aws.tmp.creds.json
.wskdebug.props.tmp

# OSX
.DS_Store

# yeoman
.yo-repository

# logs folder for aio-run-detached
logs

# ignore the build output
actions

```

Now lets try out the build by running the command **aio app run**
If you installed the optional lint plugins you will also be able to run **npm run lint** or **npm run lint-fix**

[Next](welldone.md)
