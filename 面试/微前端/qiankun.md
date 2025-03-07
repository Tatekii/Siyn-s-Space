# 沙箱

## JS沙箱
### **快照沙箱**（Snapshot Sandbox）
**原理**：
- 在子应用挂载（mount）前，**保存** window 全局对象的状态（快照）。
- 在子应用卸载（unmount）后，**恢复** window 到之前的状态。


**示例**：
```javascript
let windowSnapshot = {}; 

// 进入子应用前，保存 window 快照
function recordWindowSnapshot() {
    windowSnapshot = { ...window };
}

// 退出子应用后，恢复 window 快照
function restoreWindowSnapshot() {
    Object.keys(window).forEach(key => {
        if (!(key in windowSnapshot)) {
            delete window[key];  // 删除子应用新增的全局变量
        } else {
            window[key] = windowSnapshot[key];  // 还原 window
        }
    });
}
```

**优缺点**：
✅ **优点**：简单易实现，适用于子应用不会修改大量 window 属性的情况。
❌ **缺点**：无法隔离异步代码（如 setTimeout、Promise），可能会导致状态泄漏。


### Proxy沙箱（Proxy Sandbox）
**原理**：
- 使用 **JavaScript Proxy** 拦截 window 读写操作。
- **为每个子应用创建一个独立的 window 代理对象**，保证子应用修改的 window 变量不会影响其他应用。

**示例**：
```javascript
class ProxySandbox {
    constructor() {
        this.sandbox = {}; // 子应用自己的全局对象
        this.proxy = new Proxy(window, {
            get: (target, key) => {
                return this.sandbox.hasOwnProperty(key) ? this.sandbox[key] : target[key];
            },
            set: (target, key, value) => {
                this.sandbox[key] = value; // 存入当前子应用的沙箱中
                return true;
            }
        });
    }
}

// 创建一个沙箱实例
const sandbox = new ProxySandbox();
sandbox.proxy.test = "hello"; // 只影响当前子应用
console.log(window.test); // undefined
```

**优缺点**：
✅ **优点**：
- **完全隔离子应用**，不会污染全局 window。
- **支持异步代码**，不会造成状态泄漏。
❌ **缺点**：
- **部分全局属性（如 window.location）无法代理**。
- **Proxy 在 IE 低版本浏览器不支持**。
## CSS沙箱
