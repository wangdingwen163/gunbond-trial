# GUNBOND 试玩版 v0.2gh8 · 2026-11-25

> 这一版只修 11-25 真机日志暴露的三个问题，没有新增内容、没有调数值手感（除了把「0 料能开正常枪」堵上——这条本身会明显改变枯竭难度）。

## 一、材料终于真的等于子弹（严重修复）

**现象**：20 分钟真机，`1770` 发里**弱射 0 发**；枯竭窗口里料一直是 0，却打了 20 发、杀了 11 只。

**根因**（`WeaponRuntime.Update`）：

```csharp
// 旧：先判冷却，不判料
if (wantShot && time >= FireInterval(...))  Shoot();
else if (wantShot && starving && ...)       ShootWeak();   // 永远进不来
```

- `hold = wantFire && !starving`，而 `wantShot = hold || buffered` ⇒ 料不够时 `wantShot` 恒 false
- 主分支不判料，`SpendAmmo` 只是把料夹到 0 ⇒ **0 料照样打 100% 伤害**

**修复**：
- 新增 `canAfford = 料 >= AmmoCost(单发耗)`，付不起只给弱射
- `wantShot = wantFire || buffered`（看「想不想开火」而不是「付不付得起」）
- HUD 的「弱射中」改读 `WeaponRuntime.weakNow`

**无头验收**（`GB_H_GATE=1`，料恒 0 + 强制按住开火）：

```
t=6.0s  总射=7   弱射=7
t=8.0s  总射=13  弱射=13
t=10.0s 总射=19  弱射=19
t=13.0s 判定=OK（0 料只出弱射）  mat=0
```

修复前同一探针：`总射=1 弱射=0`，之后 8 秒一发都没有。

## 二、枯竭窗口不再被「选枪 / 休整」冻结吃掉

旧版只认 `PauseMenu.isOpen` 才停表；选枪面板走 `PauseHub + timeScale=0`，`Time.unscaledTime` 照跑。

真机证据：窗口剩 21s，玩家开了一下选枪面板，2.6 秒后被判「弹药耗尽」。

现在：`bool frozen = PauseMenu.isOpen || Time.timeScale <= 0.0001f;`，任何冻结都补时。

## 三、总击杀 / 总局数不再记两遍

- 击杀：`GameManager.NotifyKill()` 每杀 +1，`MetaProgress.OnRunEnd/OnRunAbandon` 又 `totalKills += kills`
- 局数：`GameManager.SetState` 进 Playing 时 +1，结算再 +1（每次启动多算 1 局）

真机证据：3 局实杀 `248+28+257 = 533` → 存档 `+1066`；局数 `640 → 644`（3 局记 4 局）。

现在只保留逐只击杀累加与 `MetaProgress` 的局数口径。

## 四、验收汇总

| 项 | 结果 |
|---|---|
| 编译 | `error CS = 0`，无新增 warning |
| 构建 | `Succeeded` / 错误 0 / 警告 8 / `99,075,439 B` / 167 文件 |
| DLL | `568,832 B` / `2026-11-25 19:14:19`，内含 `0.2gh8` |
| 冷启动冒烟 | 59 行 / Exception 0 / Error 0 / Warning 0 |
| 门控探针 | 0 料 20 发全为弱射，料不为负 |

## 已知问题

1. 守卫长波 32 秒硬限时，守卫长没死就超时扣 30 材料（设计如此）。
2. 仅中文，双语排后续版本（`L.cs` + 870 条表已落地，接线未做）。
3. 材料经济仍偏松：通关峰值 450+ 料，枯竭很少自然发生（M15 议案）。
4. 本版把「0 料能开正常枪」堵上后，**枯竭期会明显变难**，需要重新体感确认。