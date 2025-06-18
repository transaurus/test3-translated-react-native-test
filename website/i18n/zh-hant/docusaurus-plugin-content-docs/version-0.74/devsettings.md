---
id: devsettings
title: DevSettings
---

`DevSettings` 模組提供開發者在開發階段自訂設定的方法。

---

# 參考文獻

## 方法

### `addMenuItem()`

```tsx
static addMenuItem(title: string, handler: () => any);
```

在開發者選單中新增自訂選項。

**參數：**

| Name                                                         | Type     |
| ------------------------------------------------------------ | -------- |
| title <div className="label basic required">Required</div>   | string   |
| handler <div className="label basic required">Required</div> | function |

**範例：**

```tsx
DevSettings.addMenuItem('Show Secret Dev Screen', () => {
  Alert.alert('Showing secret dev screen!');
});
```

---

### `reload()`

```tsx
static reload(reason?: string): void;
```

重新載入應用程式。可直接呼叫或透過使用者互動觸發。

**範例：**

```tsx
<Button title="Reload" onPress={() => DevSettings.reload()} />
```