# GUNBOND 试玩版 v0.2gh10 · 2026-11-25

> 只修一条：榴弹系的弱射伤害算错了，导致「站住打 5 只」这条出路对 GL 系完全不存在。

## 根因：`damage = 0` 的枪，弱射只有 1 点

武器资产里 GL / GL+* 全部是 `damage: 0`，伤害写在 `explosionDamage`（10~16）：

```
GL        damage=0  explosionDamage=10
GL+BLAST  damage=0  explosionDamage=16
GL+FROST  damage=0  explosionDamage=10
```

旧公式：

```csharp
int dmg = Mathf.Max(1, Mathf.RoundToInt(def.damage * weakMul));  // 0 * 0.3 = 0 → 1
```

**弱射每发只有 1 点**。10 血小怪要 10 发；GL 间隔 1.0s × 1.6 = 1.6s/发 ⇒ 16 秒杀一只，5 只 80 秒 ≫ 25 秒窗口。

真机证据（11-25 第 7 局）：枯竭 4 次，其中 2 次判负，那两局 **0 杀 / 1 杀**，弱射只打出 2~5 发。

## 修复

```csharp
float baseDmg = d.damage > 0f ? d.damage : d.explosionDamage;   // 榴弹系回落到爆炸段
float mul = weakDamageMul * (famine ? 3f : 1f);                 // 枯竭 ×3 = 45%
return Mathf.Max(1, Mathf.FloorToInt(baseDmg * mul + 0.5f));    // 半数进位
```

用「半数进位」而不是 `Mathf.RoundToInt`：后者是银行家舍入，`10 × 0.45 = 4.5` 会被舍成 **4**（实测表里就是这样）。

## 验收表（`GB_H_GATE=3`，敌人 10 血）

| 枪 | damage | explosion | 弱射(平时) | 弱射(枯竭) | 每杀 |
|---|---|---|---|---|---|
| AR | 5 | 0 | 1 | 2 | 5 发（0.32s/发 = 1.6s） |
| SR | 14 | 0 | 2 | 6 | 2 发（1.84s/发 = 3.7s） |
| GL | 0 | 10 | 2 | **5** | 2 发（1.6s/发 = 3.2s） |
| GL+BLAST | 0 | 16 | 2 | **7** | 2 发（3.2s） |
| GL+FROST | 0 | 10 | 2 | **5** | 2 发（3.2s） |

最慢的 GL 杀 5 只约 16s < 25s 窗口。

## 构建

| 项 | 结果 |
|---|---|
| 编译 | `error CS = 0` |
| 构建 | `Succeeded` / 错误 0 / 警告 7 |
| DLL | `568,832 B`，内含 `0.2gh10`、无 `0.2gh9` |
| 冒烟 | 59 行 / Exception 0 / Error 0 / Warning 0 |
| zip | `40,971,175 B` / MD5 `4C6E14EFC3C6D02C493272E537F8AEFB` |

## 仍在观察

1. 高耗枪（GL 一发 0.8 料）枯竭触发较勤（一局 4 次），若体感过密再调入口。
2. 守卫长 32s 硬限时、仅中文、材料经济偏松（M15）。