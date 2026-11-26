# GUNBOND 试玩包 v0.2gh18

> 2026-11-27 · Windows 64 位 · 免安装

## 这一版是「定稿前最后一道闸」

我加了一个**运行时 i18n 扫描**（`GB_I18N=1`）：英文模式下遍历七个面板的整棵 UI 树，
把任何**还含中文的 `Text`** 逐条打出来。

为什么不能用现有的 `[L] 缺英文` 计数：那个只统计「走 `L.T()` 的查询」，
而今晚两次漏翻（巨像长提示条、骨甲）都是**代码直接拼字符串**，它根本看不见。

## 首轮扫描抓出 8 处，已全修

| 面板 | 原文 | 现英文 |
|---|---|---|
| 封面 | 军械库（标题） | Armory |
| 选枪卡 | 每秒伤害（单体） | DPS · single target |
| 选枪卡 | 每秒伤害（全中） | DPS · all pellets |
| 关间 | 第 N 层 · 完成 | Level N · Cleared |
| 关间 | 强化 · 双选 / 补给 · 补到本层 / 修补 · +25 血 | Upgrade · pick 2 / Supply · refill / Repair · +25 HP |
| 关间 | 两条选项说明 + 次强化/料上限/血·赠强化 | 见译表 |
| 军械库 | 解锁（按钮 ×7） | Unlock |
| 结算 | 胜   利 | Victory |

顺手把语言芯片「中文」加了 Exclude —— 那两个字本来就该是中文，不该被扫描当成漏译。

## 复扫结果

```
[I18N] Cover       残留中文 0 条   [FITAUDIT] Cover       问题 0 条
[I18N] GunSelect   残留中文 0 条   [FITAUDIT] GunSelect   问题 0 条
[I18N] Intermission 残留中文 0 条  [FITAUDIT] Intermission 问题 0 条
[I18N] Fusion      残留中文 0 条   [FITAUDIT] Fusion      问题 0 条
[I18N] BuffChoice  残留中文 0 条   [FITAUDIT] BuffChoice  问题 0 条
[I18N] Armory      残留中文 0 条   [FITAUDIT] Armory      问题 0 条
[I18N] Result      残留中文 0 条   [FITAUDIT] Result      问题 0 条
```

七处 **残留中文 0 / 定宽溢出 0**。

## 怎么用（以后每次发版前 30 秒）

```
GB_UISHOT=1 GB_LANG=en GB_FITAUDIT=1 GB_I18N=1 GUNBOND.exe
```

跑完看日志里 `[I18N] ... 残留中文 N 条`，全是 0 才算过闸。**玩家侧零开销**（不带环境变量就不跑）。

## 校验

- `compile error CS = 0`
- 文件：`GUNBOND_Trial_v0.2gh18_20261127.zip`
- 大小：40,985,423 B（39.09 MB）· 171 条目
- MD5：`7217F5D786C5A714A86307D9B0FA5E6F`
- SHA256：`A852179F2F7D1CFD9E11357890317415120DBF894FDD526723AD7DDF4F6E70A6`
- 自证：`[RUN] start … ver=0.2gh18`
