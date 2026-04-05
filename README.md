# rn-zip-archive-v2 [![npm](https://img.shields.io/npm/v/rn-zip-archive-v2.svg)](https://www.npmjs.com/package/rn-zip-archive-v2)

Zip archive utility for React Native — a maintained fork of [`react-native-zip-archive`](https://github.com/mockingbot/react-native-zip-archive).

## Why this fork?

The original `react-native-zip-archive` is no longer actively maintained. This fork includes critical bug fixes:

- **Fix null error codes** — replaced all `promise.reject(null, ...)` with proper error codes (`ERR_UNZIP`, `ERR_FILE_NOT_FOUND`, etc.)
- **Fix double-settlement crashes** — added missing `return` statements after `reject` in `unzipWithPassword`, `zipWithPassword`, `processZip` to prevent double promise settlement on Android

## For Expo Users

The only way to make this work with Expo is to create a [dev build](https://docs.expo.dev/workflow/overview/#development-builds), Expo Go is not supported.

## Compatibility

| React Native | rn-zip-archive-v2 | iOS deployment target |
| --- | --- | --- |
| >= 0.60 | ^7.0.0 | >= 15.5 |

## Installation

```bash
npm install rn-zip-archive-v2 --save
```


## Linking

For iOS, run the command below in your app's root folder once the package has been installed

````bash
cd ./ios && pod install
````

For Android, it's ready to go.


## Usage

import it into your code

```js
import { zip, unzip, unzipAssets, subscribe } from 'rn-zip-archive-v2'
```

you may also want to use something like [react-native-fs](https://github.com/johanneslumpe/react-native-fs) to access the file system (check its repo for more information)

```js
import { MainBundlePath, DocumentDirectoryPath } from 'react-native-fs'
```


## API

**`zip(source: string | string[], target: string): Promise<string>`**

> zip source to target

***NOTE: the string version of source is for folder, the string[] version is for file, so if you want to zip a single file, use zip([file]) instead of zip(file)***

Example

```js
const targetPath = `${DocumentDirectoryPath}/myFile.zip`
const sourcePath = DocumentDirectoryPath

zip(sourcePath, targetPath)
.then((path) => {
  console.log(`zip completed at ${path}`)
})
.catch((error) => {
  console.error(error)
})
```

**`zipWithPassword(source: string | string[], target: string, password: string, encryptionType: string): Promise<string>`**

> zip source to target

***NOTE: the string version of source is for folder, the string[] version is for file, so if you want to zip a single file, use zip([file]) instead of zip(file)***

***NOTE: encryptionType is not supported on iOS yet, so it would be igonred on that platform.***

Example

```js
const targetPath = `${DocumentDirectoryPath}/myFile.zip`
const sourcePath = DocumentDirectoryPath
const password = 'password'
const encryptionType = 'STANDARD'; //possible values: AES-256, AES-128, STANDARD. default is STANDARD

zipWithPassword(sourcePath, targetPath, password, encryptionType)
.then((path) => {
  console.log(`zip completed at ${path}`)
})
.catch((error) => {
  console.error(error)
})
```

**`unzip(source: string, target: string): Promise<string>`**

> unzip from source to target

Example

```js
const sourcePath = `${DocumentDirectoryPath}/myFile.zip`
const targetPath = DocumentDirectoryPath
const charset = 'UTF-8'
// charset possible values: UTF-8, GBK, US-ASCII and so on. If none was passed, default value is UTF-8


unzip(sourcePath, targetPath, charset)
.then((path) => {
  console.log(`unzip completed at ${path}`)
})
.catch((error) => {
  console.error(error)
})
```

**`unzipWithPassword(source: string, target: string, password: string): Promise<string>`**

> unzip from source to target

Example

```js
const sourcePath = `${DocumentDirectoryPath}/myFile.zip`
const targetPath = DocumentDirectoryPath
const password = 'password'

unzipWithPassword(sourcePath, targetPath, password)
.then((path) => {
  console.log(`unzip completed at ${path}`)
})
.catch((error) => {
  console.error(error)
})
```

**`unzipAssets(assetPath: string, target: string): Promise<string>`**

> unzip file from Android `assets` folder to target path

***Note: Android only.***

`assetPath` is the relative path to the file inside the pre-bundled assets folder, e.g. `folder/myFile.zip`. ***Do not pass an absolute directory.***

```js
const assetPath = './myFile.zip'
const targetPath = DocumentDirectoryPath

unzipAssets(assetPath, targetPath)
.then((path) => {
  console.log(`unzip completed at ${path}`)
})
.catch((error) => {
  console.error(error)
})
```

**`subscribe(callback: ({ progress: number, filePath: string }) => void): EmitterSubscription`**

> Subscribe to the progress callbacks. Useful for displaying a progress bar on your UI during the process.

Your callback will be passed an object with the following fields:

- `progress` (number)  a value from 0 to 1 representing the progress of the unzip method. 1 is completed.
- `filePath` (string)  the zip file path of zipped or unzipped file.


***Note: Remember to check the filename while processing progress, to be sure that the unzipped or zipped file is the right one, because the event is global.***

***Note: Remember to unsubscribe! Run .remove() on the object returned by this method.***

```js
componentDidMount() {
  this.zipProgress = subscribe(({ progress, filePath }) => {
    // the filePath is always empty on iOS for zipping.
    console.log(`progress: ${progress}\nprocessed at: ${filePath}`)
  })
}

componentWillUnmount() {
  // Important: Unsubscribe from the progress events
  this.zipProgress.remove()
}
```

## Related Projects

- [ZipArchive](https://github.com/ZipArchive/ZipArchive) (iOS native library)
- [zip4j](https://github.com/srikanth-lingala/zip4j) (Android native library)

## Credits

Forked from [react-native-zip-archive](https://github.com/mockingbot/react-native-zip-archive) by [MockingBot](https://github.com/mockingbot).

## License

MIT
