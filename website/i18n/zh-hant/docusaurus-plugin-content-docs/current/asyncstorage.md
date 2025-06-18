---
id: asyncstorage
title: '🚧 AsyncStorage'
---

> **Removed.** Use one of the [community packages](https://reactnative.directory/?search=storage) instead.

`AsyncStorage` is an unencrypted, asynchronous, persistent, key-value storage system that is global to the app. It should be used instead of LocalStorage.

It is recommended that you use an abstraction on top of `AsyncStorage` instead of `AsyncStorage` directly for anything more than light usage since it operates globally.

On iOS, `AsyncStorage` is backed by native code that stores small values in a serialized dictionary and larger values in separate files. On Android, `AsyncStorage` will use either [RocksDB](https://rocksdb.org/) or SQLite based on what is available.

The `AsyncStorage` JavaScript code is a facade that provides a clear JavaScript API, real `Error` objects, and non-multi functions. Each method in the API returns a `Promise` object.

Importing the `AsyncStorage` library:

```jsx
import {AsyncStorage} from 'react-native';
```

Persisting data:

```jsx
_storeData = async () => {
  try {
    await AsyncStorage.setItem(
      '@MySuperStore:key',
      'I like to save it.',
    );
  } catch (error) {
    // Error saving data
  }
};
```

Fetching data:

```jsx
_retrieveData = async () => {
  try {
    const value = await AsyncStorage.getItem('TASKS');
    if (value !== null) {
      // We have data!!
      console.log(value);
    }
  } catch (error) {
    // Error retrieving data
  }
};
```

---

# Reference

## Methods

### `getItem()`

```jsx
static getItem(key: string, [callback]: ?(error: ?Error, result: ?string) => void)
```

Fetches an item for a `key` and invokes a callback upon completion. Returns a `Promise` object.

**Parameters:**

| Name     | Type                                        | Required | Description                                                       |
| -------- | ------------------------------------------- | -------- | ----------------------------------------------------------------- |
| key      | string                                      | Yes      | Key of the item to fetch.                                         |
| callback | `?(error: ?Error, result: ?string) => void` | No       | Function that will be called with a result if found or any error. |

---

### `setItem()`

```jsx
static setItem(key: string, value: string, [callback]: ?(error: ?Error) => void)
```

Sets the value for a `key` and invokes a callback upon completion. Returns a `Promise` object.

**Parameters:**

| Name     | Type                       | Required | Description                                  |
| -------- | -------------------------- | -------- | -------------------------------------------- |
| key      | string                     | Yes      | Key of the item to set.                      |
| value    | string                     | Yes      | Value to set for the `key`.                  |
| callback | `?(error: ?Error) => void` | No       | Function that will be called with any error. |

---

### `removeItem()`

```jsx
static removeItem(key: string, [callback]: ?(error: ?Error) => void)
```

Removes an item for a `key` and invokes a callback upon completion. Returns a `Promise` object.

**Parameters:**

| Name     | Type                       | Required | Description                                  |
| -------- | -------------------------- | -------- | -------------------------------------------- |
| key      | string                     | Yes      | Key of the item to remove.                   |
| callback | `?(error: ?Error) => void` | No       | Function that will be called with any error. |

---

### `mergeItem()`

```jsx
static mergeItem(key: string, value: string, [callback]: ?(error: ?Error) => void)
```

Merges an existing `key` value with an input value, assuming both values are stringified JSON. Returns a `Promise` object.

**NOTE:** This is not supported by all native implementations.

**Parameters:**

| Name     | Type                       | Required | Description                                  |
| -------- | -------------------------- | -------- | -------------------------------------------- |
| key      | string                     | Yes      | Key of the item to modify.                   |
| value    | string                     | Yes      | New value to merge for the `key`.            |
| callback | `?(error: ?Error) => void` | No       | Function that will be called with any error. |

Example:

```jsx
let UID123_object = {
  name: 'Chris',
  age: 30,
  traits: {hair: 'brown', eyes: 'brown'},
};
// You only need to define what will be added or updated
let UID123_delta = {
  age: 31,
  traits: {eyes: 'blue', shoe_size: 10},
};

AsyncStorage.setItem(
  'UID123',
  JSON.stringify(UID123_object),
  () => {
    AsyncStorage.mergeItem(
      'UID123',
      JSON.stringify(UID123_delta),
      () => {
        AsyncStorage.getItem('UID123', (err, result) => {
          console.log(result);
        });
      },
    );
  },
);

// Console log result:
// => {'name':'Chris','age':31,'traits':
//    {'shoe_size':10,'hair':'brown','eyes':'blue'}}
```

---

### `clear()`

```jsx
static clear([callback]: ?(error: ?Error) => void)
```

Erases _all_ `AsyncStorage` for all clients, libraries, etc. You probably don't want to call this; use `removeItem` or `multiRemove` to clear only your app's keys. Returns a `Promise` object.

**Parameters:**

| Name     | Type                       | Required | Description                                  |
| -------- | -------------------------- | -------- | -------------------------------------------- |
| callback | `?(error: ?Error) => void` | No       | Function that will be called with any error. |

---

### `getAllKeys()`

```jsx
static getAllKeys([callback]: ?(error: ?Error, keys: ?Array<string>) => void)
```

Gets _all_ keys known to your app; for all callers, libraries, etc. Returns a `Promise` object.

**Parameters:**

| Name     | Type                                             | Required | Description                                                     |
| -------- | ------------------------------------------------ | -------- | --------------------------------------------------------------- |
| callback | `?(error: ?Error, keys: ?Array<string>) => void` | No       | Function that will be called with all keys found and any error. |

---

### `flushGetRequests()`

```jsx
static flushGetRequests(): [object Object]
```

透過單一批次呼叫刷新所有待處理的請求以獲取資料。

---

### `multiGet()`

```jsx
static multiGet(keys: Array<string>, [callback]: ?(errors: ?Array<Error>, result: ?Array<Array<string>>) => void)
```

此方法允許您批次獲取給定鍵值陣列對應的項目。回調函數將被調用並傳入一組對應的鍵值對：

```
multiGet(['k1', 'k2'], cb) -> cb([['k1', 'val1'], ['k2', 'val2']])
```

該方法返回一個 `Promise` 物件。

**參數：**

| Name     | Type                                                              | Required | Description                                                                                                         |
| -------- | ----------------------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------- |
| keys     | `Array<string>`                                                   | Yes      | Array of key for the items to get.                                                                                  |
| callback | `?(errors: ?Array<Error>, result: ?Array<Array<string>>) => void` | No       | Function that will be called with a key-value array of the results, plus an array of any key-specific errors found. |

範例：

```jsx
AsyncStorage.getAllKeys((err, keys) => {
  AsyncStorage.multiGet(keys, (err, stores) => {
    stores.map((result, i, store) => {
      // get at each store's key/value so you can work with it
      let key = store[i][0];
      let value = store[i][1];
    });
  });
});
```

---

### `multiSet()`

```jsx
static multiSet(keyValuePairs: Array<Array<string>>, [callback]: ?(errors: ?Array<Error>) => void)
```

使用此方法批次儲存多組鍵值對。操作完成時將透過單一回調返回所有錯誤：

```
multiSet([['k1', 'val1'], ['k2', 'val2']], cb);
```

該方法返回一個 `Promise` 物件。

**參數：**

| Name          | Type                               | Required | Description                                                                  |
| ------------- | ---------------------------------- | -------- | ---------------------------------------------------------------------------- |
| keyValuePairs | `Array<Array<string>>`             | Yes      | Array of key-value array for the items to set.                               |
| callback      | `?(errors: ?Array<Error>) => void` | No       | Function that will be called with an array of any key-specific errors found. |

---

### `multiRemove()`

```jsx
static multiRemove(keys: Array<string>, [callback]: ?(errors: ?Array<Error>) => void)
```

呼叫此方法可批次刪除 `keys` 陣列中的所有鍵值。返回一個 `Promise` 物件。

**參數：**

| Name     | Type                               | Required | Description                                                             |
| -------- | ---------------------------------- | -------- | ----------------------------------------------------------------------- |
| keys     | `Array<string>`                    | Yes      | Array of key for the items to delete.                                   |
| callback | `?(errors: ?Array<Error>) => void` | No       | Function that will be called an array of any key-specific errors found. |

範例：

```jsx
let keys = ['k1', 'k2'];
AsyncStorage.multiRemove(keys, err => {
  // keys k1 & k2 removed, if they existed
  // do most stuff after removal (if you want)
});
```

---

### `multiMerge()`

```jsx
static multiMerge(keyValuePairs: Array<Array<string>>, [callback]: ?(errors: ?Array<Error>) => void)
```

批次合併指定鍵集合的現有值與新值。此操作假設值為字串化的 JSON。返回一個 `Promise` 物件。

**注意**：並非所有原生實作都支援此功能。

**參數：**

| Name          | Type                               | Required | Description                                                                  |
| ------------- | ---------------------------------- | -------- | ---------------------------------------------------------------------------- |
| keyValuePairs | `Array<Array<string>>`             | Yes      | Array of key-value array for the items to merge.                             |
| callback      | `?(errors: ?Array<Error>) => void` | No       | Function that will be called with an array of any key-specific errors found. |

範例：

```jsx
// first user, initial values
let UID234_object = {
  name: 'Chris',
  age: 30,
  traits: {hair: 'brown', eyes: 'brown'},
};

// first user, delta values
let UID234_delta = {
  age: 31,
  traits: {eyes: 'blue', shoe_size: 10},
};

// second user, initial values
let UID345_object = {
  name: 'Marge',
  age: 25,
  traits: {hair: 'blonde', eyes: 'blue'},
};

// second user, delta values
let UID345_delta = {
  age: 26,
  traits: {eyes: 'green', shoe_size: 6},
};

let multi_set_pairs = [
  ['UID234', JSON.stringify(UID234_object)],
  ['UID345', JSON.stringify(UID345_object)],
];
let multi_merge_pairs = [
  ['UID234', JSON.stringify(UID234_delta)],
  ['UID345', JSON.stringify(UID345_delta)],
];

AsyncStorage.multiSet(multi_set_pairs, err => {
  AsyncStorage.multiMerge(multi_merge_pairs, err => {
    AsyncStorage.multiGet(['UID234', 'UID345'], (err, stores) => {
      stores.map((result, i, store) => {
        let key = store[i][0];
        let val = store[i][1];
        console.log(key, val);
      });
    });
  });
});

// Console log results:
// => UID234 {"name":"Chris","age":31,"traits":{"shoe_size":10,"hair":"brown","eyes":"blue"}}
// => UID345 {"name":"Marge","age":26,"traits":{"shoe_size":6,"hair":"blonde","eyes":"green"}}
```