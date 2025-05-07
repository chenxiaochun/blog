## vscode 使用技巧

### 退出 vscode 时，弹出确认框。以避免误操作

```json
{
  "window.confirmBeforeClose": "always"
}
```

<img src="https://img11.360buyimg.com/imagetools/jfs/t1/246803/23/25050/30160/673ee145F6d2e6560/9ce454d59247daa2.png" width="200" />

### 快捷键

`F12`，可以自动跳转到函数/类型定义的位置

### 自定义 window.title

```json
{
  "window.title": "${activeRepositoryBranchName}/${folderName}"
}
```
