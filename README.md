# react-devtools-extension-chinese
个人自用汉化版 react 开发者工具扩展，顺便修复了一些小BUG

## 注意事项
兼容性： 只测试了 Chrome 平台，Edge 未测试  
扩展版本: `只能说尽可能保持和官网一样的版本更新速度`

## 安装方法
### Chrome
把 `.crx` 拖入 Chrome 扩展管理里，接着会提示：  
`[该扩展程序未列在 Chrome 应用商店中，并可能是在您不知情的情况下添加的]`，
解决方法详见[这篇教程](https://cloud.tencent.com/developer/article/1506583) 
### Electron
> [!NOTE]
> 前两步是针对以前的electron，最新版直接跳到第三步就行了
1. 程序运行之后，会在`C:\Users\Administrator\AppData\Roaming\你的项目名称\`这里生成扩展文件夹
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/18f0a4c7-6c83-49a0-9a16-9ba7d87cf970" />

2. 然后把Chrome文件夹 `C:\Users\Administrator\AppData\Local\Google\Chrome\User Data\Default\Extensions` 里的**对应扩展文件夹**拷贝到自己项目的扩展文件夹里
<img width="1447" height="600" alt="image" src="https://github.com/user-attachments/assets/ebb0c890-f359-491c-8c5b-15cbdb164be0" />


3. **最后`electron`程序里直接引用该扩展ID**，具体可参阅[这个模板](https://github.com/NiButCrazy/Vite-Electron-React-Template)
```typescript
/**
 * 加载谷歌扩展
 */
export function load_extensions(mainWindow: BrowserWindow) {
  // 扩展必须是绝对路径
  const reactDevtools = path.join(__dirname, '../../static/react-devtools')
  const ses = mainWindow.webContents.session
  ses.extensions.loadExtension(reactDevtools)
  // ! 点睛之笔!!!
  //  React 开发者工具的内容脚本尝试与后台服务工作者通信，而后者在启动时（首次安装后）并未运行,所以手动运行
  ses.extensions.on('extension-ready', (_, extension) => {
    const manifest = extension.manifest
    if (manifest.manifest_version === 3 && manifest?.background?.service_worker) {
      ses.serviceWorkers.startWorkerForScope(extension.url)
    }
  })
}


```
