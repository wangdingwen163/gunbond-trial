# GUNBOND 试玩版 v0.2gh12

> 2026-11-26 · Windows 64 位 · 免安装 · 解压即玩

## 这一版：英文界面的最后几处中文清掉了

上一版（gh11）切到 English 后，画面上还剩这几处中文 —— 全部修掉：

| 位置 | 旧 | 新 |
|---|---|---|
| 顶部关卡行 | `第 1 关 · 波 1（第 1/5 关 · 波 1/3）　剩余敌人 15` | `Level 1 · Wave 1/3  ·  15 enemies left` |
| 右下敌情提示 | `突击兵：成群的快怪…` | `Raider: fast and comes in packs — clear the edges first` |
| 结算副标题 | `全部波次清除` / `倒在首领战` / `倒在第 N 关` | `All waves cleared` / `Fell in the boss fight` / `Fell on level N` |
| 超血 | `+10 超血`、`超血：8 秒内不补血会开始衰减…` | `+10 overheal`、`Overheal: starts decaying after 8s without healing` |
| 枯竭横幅 | `弹药枯竭` / `按 Q 换弹 +15料/−20血` | `OUT OF MATERIAL` / `Press Q to reload +15 mat / −20 HP` |
| 其他 | 守卫长预警、融合教学提示、选枪「未解锁路线」 | 全部英文 |

根因有两层，都在这版解决：
1. **译文表里有 639 行「只有 key、没有译文」的空占位** —— 代码写了 `L.T("全部波次清除")`，表里查得到 key 但值是空的，于是回落中文，而「缺译=0」的验收却看不出问题。现在**删掉 628 行空占位**，表内 457 条全部有译文（体积 35 KB → 23 KB）。
2. **HUD / 提示条 / 飘字走的是运行时写 text 的路径**，根本不过建面板时的自动翻译扫描；而且顶部那行是**按中文语序拼片段**（`"剩余敌人 " + N`），英文语序不同，只能整句重写。

## 还修了一处版式

图鉴材料页：选中材料后底部是 3 行详情，第三行 `5 routes · found 5/5` 会和右下 `Click empty space / Esc to close` 撞在一起（中文句子短所以从没暴露）。
详情框 58→84 高并上移、关闭提示下移 → 中英各留 14px 间距，已截图复核。

## 顺手加固

- `L.T1`：已经翻过的串不再二次查表，避免「拿英文查中文表」刷出假缺译日志把真问题洗掉。
- **F11 截图改位置**：从 `<游戏目录>/shots/` 改到存档目录 `%USERPROFILE%\AppData\LocalLow\王鼎文\GUNBOND\shots\` —— 换包/清理再也不会弄丢玩家截图。

## 已知问题

1. 守卫长波 32 秒硬限时
2. 材料经济偏松，但高耗枪（GL 一发 0.8 料）会让枯竭来得比较勤
3. 英文个别长句为保持单行会缩字号，字号略小于中文

## 校验

- 文件：`GUNBOND_Trial_v0.2gh12_20261126.zip`
- 大小：40,983,395 B（39.08 MB）· 171 条目 · 根目录 `GUNBOND_Trial_v0.2gh12`
- MD5：`1CE59161C8A263458BDAD3397551778F`
- SHA256：`9C5E05E3C9472C5C67AE06F4EA7EA3B083DC49EE176358B66D41B9AA450F56DE`
- 自证：日志 `[RUN] start tier=0 waves=15 levels=5 ver=0.2gh12`
- 验收：EN / ZH 双跑 `缺译=0`、`FITAUDIT` 全 0、`EXC=0`
