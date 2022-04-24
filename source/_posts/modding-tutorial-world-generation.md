---
title: 改装教程：世界生成
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-24 19:15:23
password:
summary:
tags: [tModLoader, terraria, 翻译]
categories:
---
[Modding Tutorial: World Generation](https://forums.terraria.org/index.php?threads/modding-tutorial-world-generation.47601/)

# 前言
世界生成涉及很多细节。即使在你做最基本的结构时，它们也会不断地笼罩着你，并且对于理解你的代码为什么工作或不工作至关重要。

这些概念中最基本的是泰拉瑞亚使用的坐标系，其中位置 (0, 0) 是世界的最左上角。然后，您可以使用公共变量 Main.maxTilesX 和 Main.maxTilesY 来分别获取世界的宽度和高度。

但是，在执行此操作时，请注意可玩世界在玩家实际到达任一轴上的坐标 0 之前停止 - 我从未测试过，但世界在真正停止之前停止滚动可能 40 或 50 个 tile。当您想向世界的边缘添加东西（例如海洋或太空内容）时，请记住这一点。

参考这个以获得更好的世界高度的视觉表示：
![](https://forums.terraria.org/index.php?attachments/1612298483774-png.306777/)

另一个需要注意的非常重要的事情是生成顺序 - 在泰拉瑞亚中，游戏使用了一个名为 GenPasses 的任务列表，告诉世界如何按顺序生成。记住这一点非常重要 - 例如，如果您过早生成矿石，它们很容易被紧随其后的新一代取代。比如我前段时间做了一个生物群系，经过一番测试，发现地牢或者丛林神殿可以覆盖它，使其基本不存在。因此，我不得不将其在 gen 列表中向前移动，以免我的生物群落消失。如果您了解您的世代在列表中的位置，您还会发现更容易调试。
有关 vanilla 生成步骤顺序的进一步参考，请在此处遵循此[列表](https://github.com/tModLoader/tModLoader/wiki/Vanilla-World-Generation-Steps)。

最后，您必须始终牢记的是，tile 存储在 2D 数组中。其后果如下：
- 1.如果超出数组的边界（即尝试访问(-1, 0)处的tile），则会崩溃或无法生成；
- 2.如果您要使用例如玩家位置，您需要将玩家位置除以 16 才能访问同一位置的tile - 例如，如果我想获得玩家正上方的 tile，我将使用以下代码：
```c#
Framing.GetTileSafely((int)(player.position.X / 16f), (int)(player.position.Y / 16f) - 1)
```
对弹丸、NPC、灰尘和血块进行相同的除法操作。
- 3. 这些 tile位置也始终是整数，而不是任何类型的小数。如果您尝试访问 1.2f, 2 处的 tile，您将收到错误消息。
- 4. 最后，每个 tile 总是加载到世界中，但只有当 tile 实际放置在世界中时才会激活 - 因此，如果您访问空气 tile，您可能会得到非空气类型（即沙子），但是它将是空的。

# 基本
因此，除了所有这些，我们可以从最简单的方法和工具开始，您可以使用它们来创建或检查或做任何事情：
```c#
WorldGen.PlaceTile(int i, int j, int type, bool mute = false, bool forced = false, int plr = -1, int style = 0)
```
正如您可能猜到的那样，这会在 i、j 处放置一个类型为 type 的 tile，
如果 mute 为假，则播放声音。
至于forced - 我想它会强制放置 tile，但我不经常使用它。
至于plr - 我不知道这是什么。完全没有。应该不重要吧？
最后，style 很难解释——它用于显示单个 tile 的替代版本。我将使用 PlaceObject 对此进行更多解释。

```c#
WorldGen.KillTile(int i, int j, bool fail= false, bool effectOnly = false, bool noItem = false)
```
也很简单 - 破坏 i, j 处的 tile。如果fail为true，则不会删除该 tile 。例如，这用于当您挖掘镐但需要多次击打时。
如果 effectOnly 为true，则tile只会产生灰尘，但不会破裂。通常与失败同时出现。例如，挖掘一个镐力太低的tile。
noItem 很简单，当 tile 被破坏时不会掉落物品。

```c#
Framing.GetTileSafely(int i, int j)
```
为了完全透明，我只知道这是抓取任何给定 tile 的更好方法。如果你试图通过 Main.tile[x, y] 抓取一个 tile ，我建议你改用这个。
这对于检查给定 tile 的类型、它是否处于活动状态或它的某些特性非常有用。

```c#
WorldGen.TileRunner(int i, int j, int strength, int steps, int type, bool addTile = false, float speedX = 0, float speedY = 0, bool noYChange = false, bool overRide = true)
```
好吧，这要复杂得多。 TileRunner 是一种方法，我可以解释的唯一方法是，如果选择的话，它会创建一个带有嘈杂边缘的“钻石”。
i 和 j 是 TileRunner 的位置。同样，这是在平铺位置。因此，如果您想使用玩家或 npc 位置来生成某些东西，则必须除以 16。
strength 是产生的块有多大——在某种程度上是“半径”。
我认为更好的解释方法是视觉参考：
## Spoiler: Strength of 2
<div style="display:flex;flex-direction:row;flex-wrap:wrap;">
<img src="https://forums.terraria.org/index.php?attachments/1611985758319-png.306278/"/>
<img src="https://forums.terraria.org/index.php?attachments/1611985817752-png.306279/"/>
<img src="https://forums.terraria.org/index.php?attachments/1611985961872-png.306280/"/>
<img src="https://forums.terraria.org/index.php?attachments/1611986005783-png.306281/"/>
<img src="https://forums.terraria.org/index.php?attachments/1611986233247-png.306282/">
</div>

作为参考，每个 TileRunner 都有以下一行：
```c#
WorldGen.TileRunner((int)(Main.MouseWorld.X / 16f), (int)(Main.MouseWorld.Y / 16f), STRENGTH, 5, TileID.Dirt, true, 0, 0, false, true);
```
其中 STRENGTH = 展示的强度值。
step 对我来说有点混乱 - 据我所知，它是重复的数量，循环的数量，你在这个 TileRunner 调用上做的。如果需要使用 speedX 和 speedY，您可以将东西排成一行，在 addTile 之后解释。
addTile 告诉 TileRunner 是否要添加 tile 。如果将此设置为 true，它将放置新的瓷砖。如果不是，它只会替换旧 tile ，只要将 overRide 设置为 true。
speedX 和 speedY 指的是循环后新块的位移。例如，如果我有一个任意大小的 TileRunner，在任意位置，有 3 个步骤，speedX 为 5，speedY 为 0，它将放置 3 个块，每 5 个块相隔，从原始位置开始（i，j ) 并向右走。或者，如果我将 speedX 设置为负数，它将从 (i, j) 开始并向左移动。同样的原则也适用于 speedY；只是负速度 Y 上升，而正速度 Y 下降，分别对应于 (i, j)。这可能是一个有点冗长的解释，但如果你稍微弄乱它应该是有道理的。
noYChange 是……奇怪。我从来没有测试过它，但我猜它只会让 TileRunner 忽略 speedY。我不知道。我猜只是保持这个错误。
最后，overRide。如果可能，这将使用您的类型的 tile 替换现有 tile 。例如，如果我要放置我的矿石 BananaOre，我希望将 overRide 设置为 true，这样它就会在石头、泥土和泥土中生成。当您只想添加 tile 时设置为 false。

TileRunner 对于较小规模的 tile 非常有用，例如矿床（矿石是 TileRunner 最常见的用途之一）、沙子、泥土和石块以及类似的东西。但是，我建议不要将其用于大型生物群落和结构，因为当您想要完全填充一个区域而又不会低效时，它真的很难使用。同样，也很难准确地控制你想要的方式。
```c#
WorldGen.digTunnel(int i, int j, float xDir, float yDir, int Steps, int Size, bool Wet = false)
```
现在，想象一下 TileRunner，但它会删除tile。就是这样。它的工作原理相同，只是杀死tile。
唯一的区别是 Wet 的作用。这会将水放入它挖掘的隧道中。很简单。

# 定位
考虑到所有这些方法，我看到做生成的人最大的问题是 定位。我应该把我的结构放在哪里才能让它在 <x> 生物群系中？天空在哪里？表面在哪里？这些都是有效的问题，值得庆幸的是，它们很容易解决——尤其是当你像我一样做 gen 的时候。

你有五个主要的值——
- 1. 0，世界的顶部
- 2. Main.worldSurface，世界的表面和下面的坐标被认为是“地下”；
- 3. Main.rockLayer，世界上更深的洞穴和下面的坐标被认为是“洞穴”；
- 4. Main.maxTilesY - 200，或Underworld层，此时往下意味着你​​在Underworld；
- 5. 最后是 Main.maxTilesY，世界的底部。

使用这些的任意组合可以确保您的给定结构或生物群系或任何可能的东西可以仅在特定区域内生成，例如仅在黑社会中或仅在地表下。明智地使用这些。

至于 X 坐标，它有点模棱两可，只有四个主要值（我用过）——
- 1. 0，世界的最左边
- 2. Main.maxTilesX / 2，世界中心；
- 3. Main.maxTilesX，世界的最右边；
- 4. 最后是 Main.dungeonX。该值可以位于世界中心的左侧或右侧，这样您就可以知道雪地和丛林生物群系的位置。如果 Main.dungeonX 小于 Main.maxTilesX / 2，则雪地生物群系和地牢在 spawn 的左侧。否则，它们在生成的右侧。请注意，如果在地牢生成步骤之前使用地牢X，则可能不会设置。

这也使您通常可以猜测地点和生物群落的位置。
值得注意的是变量 WorldGen.UndergroundDesertLocation，一个 Rectangle。这通过 WorldGen.UndergroundDesertLocation.Location 存储地下沙漠的左上角，并通过 WorldGen.UndergroundDesertLocation.Size() 存储大小。这在灾难的沉没之海或星光河的玻璃沙漠生物群系等情况下很有用。

# 生成阶段续
现在通过自己。
GenPass 再次成为泰拉瑞亚处理世界生成的方式。它按顺序运行GenPass by GenPass，逐步构建世界。这些通行证与整个地牢一样大，或与早期世界中添加的沙子一样小。重要的是您要如何对其进行分区。

老实说，GenPass 本身最终成为最简单易用的东西之一。
在 ModifyWorldGenTasks 方法下，如图所示，
```c#
public override void ModifyWorldGenTasks(List<GenPass> tasks, ref float totalWeight)
```
您只需将您的方法甚至代码直接添加到新任务中，如下所示，
```c#
tasks.Add(new PassLegacy("My Custom Generation", MyGenCode));
```
您需要做的就是创建一个名为 MyGenCode 的方法，并带有 GenerationProgress 参数，例如，
```c#
public void MyGenCode(GenerationProgress p)
```
但是，在大多数情况下，您不希望将您的 gen 传递给任务列表。
通常，您希望将您的通行证插入某个特定位置，如前言的“生成顺序”小节中所述。例如，
```c#
            int shiniesIndex = tasks.FindIndex(genpass => genpass.Name.Equals("Shinies"));
            if (shiniesIndex != -1)
                tasks.Insert(shiniesIndex + 1, new PassLegacy("MyGenPass", MyGenPass));
```
这将在 Shinies 步骤之后插入您的一代，这是最常用于放置矿石的步骤。
同样，您可以在[此处](https://github.com/tModLoader/tModLoader/wiki/Vanilla-World-Generation-Steps)找到所有香草通行证的名称。

您可以根据需要将您的 worldgen 组织成尽可能多（或尽可能少）的 genpass，但是，我建议您尝试将它们保持在您认为合适的不同步骤中。特别是对于可能放置在不同位置且重叠不同的不同生物群系，有时您希望将一个生物群系或结构放置在与您可能添加的另一个生物群系相比不同的位置。只要确保您始终了解您的生物群落的位置和时间，您就应该做得很好。

# 箱子
箱子自然是设计生物群落或结构并填充内容的非常重要的部分。

在 worldgen 期间（甚至在游戏期间）填充箱子非常简单：
```c#
WorldGen.PlaceChest(x, y, type, notNearOtherChests, style)
```
PlaceChest 是 worldgen 期间使用的一种方法，它可以放置一个箱子。如果箱子放置成功，它还会返回 Main.chest 中与 tile 关联的 Chest 对象的索引。如果没有成功放置，它只返回-1。
所以，我们可以这样做：
```c#
int ChestIndex = WorldGen.PlaceChest(x, y, (ushort)type, false, style);
if (ChestIndex != -1)
```
检查，当我们放下一个箱子时，它是否成功。
然后，在 if 语句中，我们可以这样做：
```c#
Main.chest[ChestIndex].item[0].SetDefaults(ItemID.Bananarang);
```
这会将您放置的箱子中的第一个物品设置为一个香蕉郎。 如果要增加数量，只需执行以下操作：
```c#
Main.chest[ChestIndex].item[0].stack = 20;
```
boom，你有20 个香蕉郎。
对箱子及其库存的进一步操作只需要一些逻辑和思考，因此大约对本节进行了四舍五入。