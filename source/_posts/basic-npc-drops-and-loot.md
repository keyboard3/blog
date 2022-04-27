---
title: NPC 掉落和战利品基础
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-27 10:33:23
password:
summary:
tags: [tModLoader, terraria, 翻译,c#]
categories:
---
[Basic NPC Drops and Loot](https://github.com/tModLoader/tModLoader/wiki/Basic-NPC-Drops-and-Loot)

# NPC 掉落和战利品基础
本指南将教授在杀死敌人时丢弃物品的基础知识。请注意，此页面仅适用于 1.3 tModLoader。 1.4 tModLoader 中的 NPC Loot 完全不同，请参阅 ExampleMod 中的 ModifyNPCLoot 用法示例和基本 NPC Drops and Loot 1.4。

## 基础
我们在 ModNPC 类或 GlobalNPC 类中使用 NPCLoot 方法来指定从敌人身上掉落的物品。

### ModNPC.NPCLoot() vs GlobalNPC.NPCLoot()
我们可以在 2 个地方放置 NPC 战利品代码。如果我们的模组添加了一个 NPC 并且我们想要该 NPC 的特定掉落，请将相关代码放入该 ModNPC 类中。如果我们想为原版 NPC 添加掉落物，请将代码放入 GlobalNPC 类中。如果我们想为所有NPC添加掉落物，例如地牢宝箱钥匙或灵魂如何掉落，请将代码放在GlobalNPC中。请记住，组织是模组可维护性的关键。

### Item.NewItem
在本指南中，您将看到 Item.NewItem 方法被调用。请参阅[有用的香草方法](https://github.com/tModLoader/tModLoader/wiki/Useful-Vanilla-Methods#public-static-int-newitemint-x-int-y-int-width-int-height-int-type-int-stack--1-bool-nobroadcast--false-int-pfix--0-bool-nograbdelay--false-bool-reverselookup--false)以查看参数。此方法将一个项目生成到游戏世界中。该项目以参数指定的区域为中心生成。

### 如何指定我的项目？
在下面的示例中，我们删除了一个普通项目：ItemID.BeeGun。这可以通过用 ModContent.ItemType<ItemName>() 替换你的 mod 中的一个项目

### 一直掉落 1 件物品
下面显示了最基本的示例。每次杀死此 ModNPC 时，此代码将掉落 1 个蜜蜂枪。
```csharp
public override void NPCLoot()
{
	Item.NewItem(npc.getRect(), ItemID.BeeGun);
}
```

### 额外的掉落
我们可以通过添加其他代码行来添加额外的drop。
```csharp
Item.NewItem(npc.getRect(), ItemID.Beenade);
Item.NewItem(npc.getRect(), ItemID.HiveWand);
```

### Stack Size
如果我们希望在 Stack 中掉落多个项目，请指定要掉落的数量。
```csharp
Item.NewItem(npc.getRect(), ItemID.Beenade, 20);
```

### 每个玩家或实例掉落
//每个玩家的 TODO

#### Instanced
如果你想让一个物品像 boss 包一样掉落（每个玩家一个，客户端（其他玩家不会看到属于其他玩家的掉落物）），请使用 npc.DropItemInstanced 方法：
```csharp
npc.DropItemInstanced(npc.position, npc.Size, ItemID.Picksaw, 1, true);
```
最后两个参数是 stack 大小，以及 NPC 和玩家之间是否需要交互才能使其掉落。

## 随机性
大多数时候，我们不希望物品一直掉落，而是希望有很小的机会。我们可以使用随机数生成器让我们的物品有机会掉落。我们将使用 Main.rand.[METHODNAME] 来执行此操作，通常是 Main.rand.Next。

### 随机几率
如果我们想要 1 in X 的机会，推荐使用以下代码。请注意，Next(int max) 方法返回一个从 0 到 max - 1 的数字。在以下示例中，返回值的可能性为：0、1、2、3、4、5 和 6。（绝不是 7！）另外，不要更改 0。
```csharp
if (Main.rand.Next(7) == 0)
	Item.NewItem(npc.getRect(), ItemID.Beenade, 20);
```
### 随机数量
请记住， Main.rand.Next(int) 可以返回 0 并且不返回最大值，因此请使用以下之一。
```csharp
Item.NewItem(npc.getRect(), ItemID.Beenade, 5 + Main.rand.Next(3)); // 5, 6, or 7
Item.NewItem(npc.getRect(), ItemID.Beenade, Main.rand.Next(5, 8)); // 5, 6, or 7
// 错误的！不要使用，有几率掉落0：Item.NewItem(npc.getRect(), ItemID.Beenade, Main.rand.Next(5))；
```

### 指定几率
有时，X 比率中的 1 可能不是我们想要的。
```csharp
if(Main.rand.Next(7) < 2) // a 2 in 7 chance
```
我们甚至可以做具体的百分比。
```csharp
if (Main.rand.NextFloat() < .1323f) // 13.23% chance
```

## 其他条件
有时我们想要检查其他条件，例如专家模式或当前生物群系。

### 使用条件
我们可以使用 AND (&&) 等逻辑运算符向 NPCLoot 代码添加条件
```csharp
if (Main.rand.Next(7) == 0 && Main.expertMode)
	// 专家只放在这里。七分之一的机会进入专家世界。
```
### 双专家模式掉落
以下是加倍专家 mod 掉落的示例。你可以随心所欲地做，只要确保逻辑是合理的。
```csharp
if(Main.rand.NextBool(Main.expertMode ? 2 : 1, 5))
```
### 不同的专家模式掉落
如果您想更具体地了解您的专家模式条件或任何其他条件，请使用 if-else 语句。
```csharp
// 专家模式下降 10-20，普通模式下降 20-30：
if(Main.expertMode)
	Item.NewItem(npc.getRect(), ItemID.Beenade, Main.rand.Next(20, 31));
else
	Item.NewItem(npc.getRect(), ItemID.Beenade, Main.rand.Next(10, 21));
```
### 生物群落或位置
下面显示了香草如何掉落光之魂的代码。请注意，此示例更适合 GlobalNPC 类而不是 NPCLoot 类，因为它会为所有在生物群落/区域中死亡的 NPC 添加掉落物。
```csharp
// 我们检查了一些过滤掉 Boss 和小动物的东西，以及 npc 死亡的深度。
if (Main.hardMode && !npc.boss && npc.lifeMax > 1 && npc.damage > 0 && !npc.friendly && npc.position.Y > Main.rockLayer * 16.0 && npc.value > 0f && Main.rand.NextBool(Main.expertMode ? 2 : 1, 5))
{
	if (Main.player[Player.FindClosest(npc.position, npc.width, npc.height)].ZoneHoly)
	{
		Item.NewItem(npc.getRect(), ItemID.SoulofLight);
	}
}
```

### 自定义生物群落
将上述示例中的 .ZoneHoly 替换为 .GetModPlayer<ExamplePlayer>().ZoneExample。

### 杀死NPC的玩家
有时我们想为最后攻击 NPC 的玩家做点什么。 NPC 有一个 lastInteraction 字段，默认为 255，这意味着没有玩家损坏过 NPC。如果 townNPC 或陷阱对 NPC 造成所有伤害，则 NPC 有可能在 lastInteraction 仍为 255 时死亡。因此，通常用于奖励或影响杀死 NPC 的玩家的代码可能看起来像这样，如果需要，可以使用 FindClosestPlayer：
```csharp
int playerIndex = npc.lastInteraction;
if (!Main.player[playerIndex].active || Main.player[playerIndex].dead)
{
	playerIndex = npc.FindClosestPlayer(); // 因为 lastInteraction 可能是无效的玩家，所以回退到最近的玩家
}
Player player = Main.player[playerIndex];
// 其他影响玩家的代码。如果相关，可能需要 ModPackets。
```

## 随机选择
很多时候，我们想从一组选择中掉落一个随机项目。有幼稚的方法和更好的方法。
```csharp
int choice = Main.rand.Next(2);
if (choice == 0)
{
	Item.NewItem((int)npc.position.X, (int)npc.position.Y, npc.width, npc.height, ModContent.ItemType<PuritySpiritMask>());
}
else if (choice == 1)
{
	Item.NewItem((int)npc.position.X, (int)npc.position.Y, npc.width, npc.height, ModContent.ItemType<BunnyMask>());
}
```
上面的代码也可以用 switch 语句代替。
更好的方法：
```csharp
using Terraria.Utilities;
// --------
var dropChooser = new WeightedRandom<int>();
dropChooser.Add(mod.ItemType<Items.Armor.PuritySpiritMask>());
dropChooser.Add(mod.ItemType<Items.Armor.BunnyMask>());
int choice = dropChooser;
Item.NewItem(npc.getRect(), choice);
```
第二种方法更容易维护。您还可以为不同的选择分配不同的权重。这种用法可能更高级或专家级别，但我想我会提到它。
另一种方法：
```csharp
int[] choices = new int[] { ModContent.ItemType<CarKey>(), ModContent.ItemType<ExampleLightPet>(), ItemID.PinkJellyfishJar };
int choice = Main.rand.Next(choices);
Item.NewItem(npc.getRect(), choice);
```

## What about Vanilla NPC?
如果您想从原版 NPC 中获得物品掉落，所有相同的想法都适用，只是我们将代码放入 GlobalNPC 类中，并使用 if 语句过滤掉我们没有的 npc 掉落，而不是将我们的代码放在 ModNPC 类中不想影响
```csharp
class MyGlobalNPC : GlobalNPC
{
	public override void NPCLoot(NPC npc)
	{
		if(npc.type == NPCID.Frankenstein)
		{
      //在这里添加指定的 Frankenstein NPC 调用物品
		}
		// 如果您想向其他原版 npc 添加掉落物，请在此处添加 if 语句。
	}
}
```
### 特别案例
一些原版 Boss 需要特殊条件才能检测到它们何时被杀死并准备好掉落战利品。
世界吞噬者：
```csharp
if (npc.boss && System.Array.IndexOf(new int[] { NPCID.EaterofWorldsBody, NPCID.EaterofWorldsHead, NPCID.EaterofWorldsTail }, npc.type) > -1)
```
双胞胎：
```csharp
if (npc.type == NPCID.Retinazer && !NPC.AnyNPCs(NPCID.Spazmatism) || npc.type == NPCID.Spazmatism && !NPC.AnyNPCs(NPCID.Retinazer))
```

## 其他方法
### NextBool
如果您想使用它而不是将随机数与 0 进行比较，可以使用 NextBool 方法
```csharp
if (Main.rand.NextBool(7))
	Item.NewItem(npc.getRect(), ItemID.Beenade, 20); 
```