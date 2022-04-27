---
title: NPC 生成基础
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-27 11:10:23
password:
summary:
tags: [tModLoader, terraria, 翻译,c#]
categories:
---
[Basic NPC Spawning](https://github.com/tModLoader/tModLoader/wiki/Basic-NPC-Spawning)

# NPC 生成基础
本指南将教授生成敌人的基础知识。

# 基本思想
您需要首先了解一些想法：

## 平衡
很难猜测 ModNPC.SpawnChance 的返回值是否合适。与普通 NPC 相比，我们不希望我们的 NPC 频繁生成。通常 0.1f 或更小的值是好的，但你应该使用 [Modders Toolkit](https://forums.terraria.org/index.php?threads/modders-toolkit-a-mod-for-modders-doing-modding.55738/) 的 NPC Spawn Tool 来比较你的 NPC 与原版和其他 Mod 的 NPC 的生成率。

## Terraria 生成
泰拉瑞亚通过首先决定生成 NPC 的位置，然后询问每个 NPC 是否愿意在该位置生成来生成 NPC。每次泰拉瑞亚决定生成一个 NPC 时，它将与一个玩家对象 (NPCSpawnInfo.player) 结合使用。在多人游戏中，服务器处理所有生成决策。如果你在你的模组中创建了一个自定义生物群系并注意到生成在多人游戏中无法正常工作，你的 ModPlayer.SendCustomBiomes 和相关钩子需要正确实现，以便服务器知道自定义生物群系布尔值的正确值，以便它可以做出正确的决定。

## 返回值
ModNPC.SpawnChance 钩子返回一个浮点数。不明白的谷歌一下。 ModNPC.CanTownNPCSpawn 钩子返回一个布尔值。

## ModNPC.SpawnChance
这是本指南的主要重点。所有自然生成的非boss、非城镇NPC ModNPC 类都应该覆盖这个钩子：
```csharp
public override float SpawnChance(NPCSpawnInfo spawnInfo)
{
	// Code goes here
}
```

## ModNPC.CanTownNPCSpawn
仅适用于 townNPC ModNPC。请注意，此类返回布尔值而不是浮点数。在满足某些条件（例如击败boss）后，使用它让您的城镇 NPC 生成。
```csharp
public override bool CanTownNPCSpawn(int numTownNPCs, int money)
{
	// Code goes here
}
```

## 条件句（if-else）
生成 NPC 归结为决定是否生成我们的 ModNPC。在[此处](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/if-else)阅读 if-else。

### Not Operator (!)
在[这里](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/logical-negation-operator)阅读。
```csharp
if(Main.dayTime) // if day time

if(!Main.dayTime) // if night time
```

### And and OR (&& and ||)
阅读 [&&](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-and-operator) 和 [||](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-or-operator)。请注意 && 的优先级高于 ||。我们将使用它来组合条件。

### = vs ==

不要把这两个混为一谈。 = 为变量赋值， == 比较两个值。确保永远不要做 if(Main.hardMode = true) 之类的事情，否则你会困惑为什么你的世界突然进入困难模式。

### 三元
if-else 条件的更紧凑版本是三元。在[这里](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-operator)阅读它。基本上它会改变：
```csharp
if (condition)
{
	return .1f;
}
else
{
	return 0f;
}
```
对此：
```csharp
return condition ? .1f : 0f;
```

# NPCSpawnInfo
NPCSpawnInfo 是一个结构，其中包含与泰拉瑞亚希望生成 NPC 的生成位置有关的所有信息。请参阅有关字段的[文档](http://tmodloader.github.io/tModLoader/html/struct_terraria_1_1_mod_loader_1_1_n_p_c_spawn_info.html)。我们将使用该结构中的值来指导我们的逻辑并做出最终决定。

## 玩家生物群系
使用在 NPCSpawnInfo 中传入的玩家对象而不是 Main.LocalPlayer 在生成逻辑中使用玩家生物群系。[List of Zone Booleans](http://tmodloader.github.io/tModLoader/html/struct_terraria_1_1_mod_loader_1_1_n_p_c_spawn_info.html#a894868167c60f17bea09fba0aea811a8)
```csharp
if(spawnInfo.player.ZoneJungle) // Vanilla Biome aka Zone

if(spawnInfo.player.GetModPlayer<ExamplePlayer>().ZoneExample) // Mod Biome
```

## Heights
生成世界时，会与世界一起保存一些值以指定各种高度。 Main.worldSurface 在 spawn 下方几格，而 Main.rockLayer 在岩石变得比泥土更普遍的地方。 Main.maxTilesY 是世界上 Y 的最大值，最低点。我们可以使用这些值和数学来驱动我们的生成条件。我们还可以使用如下图所示的预定义区域，而不是搞乱数学。
![](https://camo.githubusercontent.com/38c32bf6a91f4f83f30a027f7f0bfc507f1e531af29eff461c140542d12202d3/68747470733a2f2f692e696d6775722e636f6d2f39724967534d742e706e67)
在图像的右侧，我们看到为我们预定义的区域，在左侧，我们看到驱动这些区域的数学。例如，以下是等价的：
```csharp
if(spawnInfo.player.ZoneRockLayerHeight)
```
```csharp
if(spawnInfo.spawnTileY <= Main.maxTilesY - 200 && spawnInfo.spawnTileY > Main.rockLayer)
```
我们可以使用简单的数学来制作更精确的基于高度的生成条件。例如 if(spawnInfo.spawnTileY <= Main.maxTilesY - 200 && spawnInfo.spawnTileY > (Main.rockLayer + Main.maxTilesY - 200) / 2) 可用于指定 ZoneRockLayerHeight 下半部分的生成条件，如图所示多于。预定义的基于高度的区域易于使用，但请记住，您可以将数学用于更具体的行为。还请记住，Y 坐标在天空中从 0 开始，并且随着您在世界中下降，值会增加。不要被 WorldGen.lavaLine、WorldGen.waterLine、WorldGen.worldSurfaceHigh 以及这里没有提到的其他值所迷惑，它们不会保存在世界文件中，并且不适用于 NPC 生成。

# Other Values
除了 NPCSpawnInfo，我们还可以在 SpawnChance 逻辑中使用其他字段：

- `Main.dayTime` - 白天为true，晚上为false
- `NPC.downedGolemBoss` 和 [other](https://github.com/tModLoader/tModLoader/wiki/NPC-Class-Documentation#downedboss1) - 如果指定的 Boss 在这个世界上被击败，则为 true
- `Main.hardMode` - 如果处于困难模式，则为true
- `Main.expertMode` - 如果处于专家模式，则为 true
- `Main.time` - 白天介于 0 (4:30 AM) 和 54000 (7:30 PM) 之间以及夜间介于 0 (7:30 PM) 和 32400 (4:30 AM) 之间的值。与 Main.dayTime 一起使用。 Main.time 通常每个刻度增加 1。每个游戏小时是 3600 滴答声。
  - 示例：Main.dayTime && Main.time < 18000.0 - 早上 4:30 到 9:30 之间（因为 18000/3600 == 5）
- `Main.raining` - 如果当前正在下雨则为 true
- `NPC.AnyNPCs(NPCID.IceGolem)` - 如果世界上有任何冰傀儡，则为 true。与 !以防止重复生成迷你 Boss。
  - NPC.AnyNPCs(mod.NPCType<PartyZombie>()) 或 NPC.AnyNPCs(mod.NPCType("PartyZombie")) - 相同，但适用于修改后的 NPC
- `NPC.CountNPCS(NPCID.AngryNimbus) < 2` - 如果世界上存在的 NPC 少于 2 个，则为真
- `TileID.Sets.Conversion.Sand[spawnInfo.spawnTileType]` - 如果生成图块是任何类型的沙子图块，则为 true。 TileID.Sets.Conversion 中还有其他可能有用的集合
- `Math.Abs(spawnInfo.spawnTileX - Main.spawnTileX) > Main.maxTilesX / 3` - 如果生成图块位于地图的外三分之一处，则为 true
- `NPC.waveNumber` - 事件期间的波数
- 需要更多？在 Discord 上向我们寻求帮助，我们可以在此列表中添加更多内容。

# SpawnCondition
SpawnCondition 是一个包含一组即用型字段的类，这些字段模拟各种 Vanilla NPC 生成条件的逻辑。有关可用的 SpawnConditions，请参阅[文档](http://tmodloader.github.io/tModLoader/html/class_terraria_1_1_mod_loader_1_1_spawn_condition.html)。使用 SpawnCondition 字段可以简化 SpawnChance 逻辑。例如，可以像这样轻松实现白天的粘土
```csharp
return SpawnCondition.OverworldDaySlime.Chance * 0.1f;
```
与
```csharp
return Main.dayTime && info.spawnTileY <= Main.worldSurface ? 0.1f : 0f;
```

# 示例
下面的每个示例都好像它们在 ModNPC.SpawnChance 方法中一样：
```csharp
public override float SpawnChance(NPCSpawnInfo spawnInfo)
{
	// Example Code Here
}
```

### 在我的 Mod 块上生成
```csharp
return spawnInfo.spawnTileType == mod.TileType<Tiles.CrystalBlock>() ? .1f : 0f;
```

### 如果玩家在自定义生物群系/区域中生成
```csharp
return spawnInfo.player.GetModPlayer<CrystalPlayer>().ZoneCrystal ? .1f : 0f;
```

### 生成于丛林神殿
```csharp
return spawnInfo.spawnTileType == TileID.LihzahrdBrick && spawnInfo.lihzahrd ? .1f : 0f;
// or
return SpawnCondition.JungleTemple.Chance * 0.1f;
```

### 在日食期间生成
```csharp
return spawnInfo.spawnTileY <= Main.worldSurface && Main.dayTime && Main.eclipse;
// or
return SpawnCondition.SolarEclipse.Chance * 0.05f; // 记住要测试这个值是否平衡
```

### 玩家站在太阳板块上
```csharp
// 这里我展示了两种将 boolean 转换为 int 的方法。 （false为 0，true 为 1）
return (Main.tile[spawnInfo.playerFloorX, spawnInfo.playerFloorY].type == TileID.Sunplate).ToInt() * 0.2f;
// or
return Convert.ToInt32(Main.tile[spawnInfo.playerFloorX, spawnInfo.playerFloorY].type == TileID.Sunplate) * 0.2f; // using System;
```

# 组合片段
就像上面的例子一样，我们结合逻辑片段来构建我们的最终决定。参见 !、&& 和 ||多于。

## 结合 bool 和 float
SpawnCondition 字段返回代表机会的浮点值，而许多其他条件只是布尔值。这可能会导致一些棘手的代码。让我们尝试为应该在蜘蛛洞穴中但仅在晚上生成的 NPC 组合 SpawnChance 代码。我们可以为此使用 SpawnCondition.SpiderCave 和 Main.dayTime。

### 简单的语法
如果你不是很了解 c#，就将 bool 和 float 分开。使用 !、&& 和 || 在 if 语句中使用布尔值如果需要，然后如果我们通过这些条件，请在返回中使用 SpawnCondition。如果条件失败，代码将返回 0，表示 NPC 不会生成：
```csharp
if(!Main.dayTime)
    return SpawnCondition.SpiderCave.Chance * 0.1f;
return 0;
```

### 中等语法
使用我们上面学到的三元组，我们可以使我们的生成条件逻辑更加紧凑。
```csharp
return !Main.dayTime ? SpawnCondition.SpiderCave.Chance * 0.1f : 0;
```

### 复杂的语法
如果您发现将布尔值分别解释为 false 和 true 时的 0 和 1 时您的逻辑更有意义，那么您可以这样做。这种方法并不是很常见，但可能很有用：
```csharp
return (!Main.dayTime).ToInt() * SpawnCondition.SpiderCave.Chance * 0.1f;
```

# 常见错误
### 为什么我的世界突然进入困难模式？
许多新程序员混淆了 = 和 ==。 = 为变量赋值， == 比较值。

如果你这样做 if(Main.hardMode = true) 你将 true 分配给 hardMode，本质上是让世界直接进入 hardmode，而不是你想要的。确保执行 if(Main.hardMode == true) 或更好的 if(Main.hardMode)。

### CS0161 '[ClassName].SpawnChance(NPCSpawnInfo)'：并非所有代码路径都返回值
这意味着您的代码有机会不返回值。例如。
```csharp
if (spawnInfo.granite)
    return 0.2f;
```
需要像这样修复：
```csharp
if (spawnInfo.granite)
    return 0.2f;
return 0f;
```

### CS0029 无法将类型“bool”隐式转换为“float”
这意味着您可能忘记使用您的逻辑来决定要返回的值。往上看。

# 相关参考资料
- [ModNPC.SpawnChance Documentation](http://tmodloader.github.io/tModLoader/html/class_terraria_1_1_mod_loader_1_1_mod_n_p_c.html#ae7713bbbd313012944b958e8eafc35e0)
- [NPCSpawnInfo Documentation](http://tmodloader.github.io/tModLoader/html/struct_terraria_1_1_mod_loader_1_1_n_p_c_spawn_info.html)
- [SpawnCondition Documentation](http://tmodloader.github.io/tModLoader/html/class_terraria_1_1_mod_loader_1_1_spawn_condition.html)

# 基础级别未涵盖
- ModNPC.CheckConditions - 自定义 TownNPC 房屋条件（如 Truffle）
- Mod Boss Booleans - 请参阅 ExampleMod 以了解正确的同步和使用
- 使用 ModNPC.SpawnNPC - 在生成时操纵 NPC
- GlobalNPC.EditSpawnRate - 操纵最大生成和生成率（水蜡烛）
- GlobalNPC.EditSpawnRange
- GlobalNPC.EditSpawnPool - 填充选项后操作生成池
- GlobalNPC.SpawnNPC
