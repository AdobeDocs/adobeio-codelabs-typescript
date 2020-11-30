## Lesson 4: Add TypeScript support for web-src

By default, [ParcelJS](https://parceljs.org/) is used under the hood to bundle your frontend code. 
If you're using [React Spectrum](https://react-spectrum.adobe.com/) which is written in TypeScript, it makes sense to write your frontend code in TypeScript as well to benefit from TypeScript features.

To use Typescript + React + JSX in your your `web-src` folder, you'll need to:                                                             
* Use the `.tsx` extension
* Properly require React
* Add a tsconfig with a [special option](https://www.typescriptlang.org/docs/handbook/jsx.html) `"jsx": "react"` at the root of your Firefly project

```json
// tsconfig.json
{
  "compilerOptions": {
    "jsx": "react"
  }
} 
```

See the [ParcelJS](https://parceljs.org/typeScript.html) documentation for more information and the [Firefly app with TypeScript support](https://github.com/AdobeDocs/adobeio-samples-typescript) for a full example.

[Next](welldone.md)