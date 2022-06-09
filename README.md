# Stream Array to JS

## What is this?

This is a pair of functions to transform a block of code into an array of byte streams to include JavaScript in an obfuscated format

### Converting Original Code into Stream Array

```ts
const JSToStreamArray = js =>
  (
    js
      .split('\n')
      .join('')
      .match(/.{1,48}/g) || []
  ).map(chunk =>
    chunk
      .split('')
      .map(char => char.charCodeAt(0).toString(16).padStart(2, '0'))
      .join(''),
  )
```


Given original code of

```js
function HelloWorld() { console.log("Hello, World!"); }
```

And the conversion process

```js
const originalCode = HelloWorld.toString()
const streamArray = JSToStreamArray(originalCode)
```

The resulting Stream Array looks like this:

```json
[
  "66756e6374696f6e2048656c6c6f576f726c642829207b20202020202020202020636f6e736f6c652e6c6f6728224865",
  "6c6c6f2c20576f726c642122293b20202020202020207d"
]

```

### Converting Stream Array into Original Code

```ts
const streamArrayToJS = (stream: string[]): string =>
  stream
    .map(code =>
      (code.match(/.{2,2}/g) || [])
        .map(hex => String.fromCharCode(parseInt(hex, 16)))
        .join(''),
    )
    .join('')
```

### Using Converted Code

```js
const HelloWorld = Function(streamArrayToJS([
  "66756e6374696f6e2048656c6c6f576f726c642829207b20202020202020202020636f6e736f6c652e6c6f6728224865",
  "6c6c6f2c20576f726c642122293b20202020202020207d"
]))

HelloWorld()
```

And like magic, calling `HelloWorld()` will console log `"Hello, World!"`
