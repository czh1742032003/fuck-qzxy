# 趣智校园免广告

针对「趣智校园」App 的广告域名拦截规则，提供 **Shadowrocket 模块** 和 **Clash Verge Rev 覆写（Merge）** 两种形式。

---

## 目录结构

```
.
├── README.md
├── Shadowrocket/
│   └── 趣智校园免广告.sgmodule      # 给 Shadowrocket（macOS / iOS）
└── Clash/
    └── 趣智校园免广告.merge.yaml    # 给 Clash Verge Rev（Mihomo 内核）
```

---

## 使用前须知

- 这是**域名级拦截**，不是去广告插件：能挡住已知广告 SDK 的域名，但 App 自身服务器下发的广告、或换用新 SDK 后的广告可能仍然出现，需要在规则里补充新域名。
- 规则里有一条 `DOMAIN-KEYWORD,mobads` 以及若干通用广告域名，理论上也会拦截其他 App 的同类广告；如果其他 App 出现功能异常，可临时停用本规则或删掉对应行。
- 规则来自实际抓包与测试，App 更新后域名可能变化；不保证长期有效。

---

## Shadowrocket（macOS / iOS）

### 导入

**macOS**

1. 右键 `Shadowrocket/趣智校园免广告.sgmodule` →「打开方式」→ Shadowrocket；
2. 或在终端执行：

   ```bash
   open -a Shadowrocket "Shadowrocket/趣智校园免广告.sgmodule"
   ```

导入后文件会进入 Shadowrocket 的模块目录。

**iOS**

1. 用 AirDrop / 文件 App 把 `.sgmodule` 传到 iPhone；
2. 点开文件 →「用 Shadowrocket 打开」；或长按 → 共享 → Shadowrocket。

**从 URL 导入（可选，便于以后更新）**

把 `.sgmodule` 放到任意 HTTPS 地址后：

- Shadowrocket →「配置」→「模块」→ 右上角「+」→「Download from URL…」；或
- 在 Safari 打开 `shadowrocket://install?module=<文件URL>`。

之后可在模块页用「更新模块」拉取更新。

**手动新建（没有文件时）**

「配置」→「模块」→「新建模块」，粘贴 `.sgmodule` 的全部内容（含 `#!name` / `#!desc` / `[Rule]`），保存。

> 注意：不要用「配置 → 导入」——那是导入 `.conf` 主配置的入口，不是模块入口。

### 启用与停用

- 在「配置 → 模块」中找到「趣智校园免广告」，确认已启用；
- 停用：关闭该模块的开关，或直接删除；

---

## Clash Verge Rev（Mihomo 内核）

Clash Verge Rev 用 **Merge（增强配置）** 实现同样的效果：新建一张 Merge 配置，启用时叠加到当前主配置，停用即恢复，不需要改主配置或订阅。

注：本教程以 Clash Verge Rev 进行演示。本教程的配置文件适配所有 Mihomo 内核的软件，如 Clash、Flclash、Sparkle 等等

### 导入

1. 打开 Clash Verge Rev →「配置」页 → 新建 → **Merge**（部分版本显示为「覆写」）；
2. 把 `Clash/趣智校园免广告.merge.yaml` 的内容粘贴进去，或导入该文件；
3. 右键这张配置 →「启用」。

文件内容是一段 `prepend-rules`：

```yaml
prepend-rules:
  - DOMAIN-SUFFIX,xiaozhuanbang.cn,REJECT
  ...
  - DOMAIN-SUFFIX,china-qzxy.cn,DIRECT
```

- 修改文件后，右键「禁用」再「启用」或点刷新，才会重新生效。
- **顺序**：如果你同时启用了其他用 `rules:` 整体接管规则的 Merge，请让本配置在它之后启用；若规则没生效，把本配置禁用再启用一次。
- **停用/卸载**：右键停止启用或删除这张 Merge 配置即可。

### 其他 Mihomo 客户端

若有 OpenClash、裸 Mihomo 等如果不支持 Verge 的 Merge 机制，可以：

- 把 45 条规则手动插到 `rules:` 的最前面；或
- 用 `rule-providers`（`type: inline` 或 `file`，`behavior: classical`）加载本规则，并在规则里加一条 `RULE-SET` 引用。

这两种方式需要改配置文件，不能像 Merge/模块那样独立启停。

---

## 验证是否生效

1. 重启趣智校园（清掉已缓存的广告）；
2. 打开 App，观察开屏/首页是否还有「摇一摇」跳转或横幅广告；

---

## 维护

- 两份规则文件内容保持一致：Shadowrocket 用 `DOMAIN-SUFFIX,域名,REJECT` 形式，Clash 在同一行前加 `  - ` 放进 `prepend-rules`；
- 新增规则时按类别放到对应分组，保持可读；
