---
title: 改装教程：世界生成
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-25 19:15:23
password:
summary:
tags: [tModLoader, terraria, 翻译,c#]
categories:
---
[World Generation](https://github.com/tModLoader/tModLoader/wiki/World-Generation)

# 什么是世界生成
世界生成是以编程方式从世界中放置和移除 Tile 的行为。世界生成在两个地方完成，在世界创建期间和在游戏中。本指南的大部分内容将集中在世界创建期间的世界生成，但也会详细介绍游戏中的注意事项。

世界生成是一个相当复杂的主题，需要对许多主题有很好的理解才能有效地工作。建议在直接跳入代码之前先熟悉以下部分。此外，强烈建议使用具有编辑和持续支持的 IDE，例如 Visual Studio。

# 术语
## Pass, Step, and Task
生成世界时，游戏会按顺序运行每个通道。在本指南中，术语 step 将指代构成 pass 的各个代码部分。 World Generation 由 Passes 组成，Passes 由 Steps 组成。例如，构成生物群系的通道可能由 2 个步骤组成，第一步是挖洞，第二步是放置树木。术语 Task 可能等同于 Pass，但我们将在本指南中避免使用该术语，因为 Task 具有其他含义，我们稍后将在本指南中使用它。

# 必备知识
## Tile 坐标
在 tile 坐标系中世界的左上角是 0,0 ，右下角位于 Main.maxTilesX，Main.maxTilesY。这些坐标直接映射到 Main.tile[,]。有关详细信息，请参阅[坐标](https://github.com/tModLoader/tModLoader/wiki/Coordinates)。按照惯例，我们在代码中使用 x 和 y 或 i 和 j 来表示图块坐标。我们需要多对变量，因为很多时候我们都在使用从其他坐标派生的坐标。

## Main.tile[,]
Main.tile[,] 是一个包含世界上所有 tile 的二维数组。您可以通过编写 Tile tile = Main.tile[x, y]; 在 worldgen 期间直接访问特定 x 和 y 坐标处的 Tile 对象。如果您在游戏中检查 Tile，则必须使用 Tile tile = Framing.GetTileSafely(x, y);因为 Tile 对象可能为空。因为 Tile 对象可能为空。请注意，负数或世界范围之外的坐标会导致错误。为避免这种情况，请使用 [WorldGen.InWorld](https://github.com/tModLoader/tModLoader/wiki/World-Generation#terrariaworldgen-public-static-bool-inworldint-x-int-y-int-fluff--0) 方法。

## Tile 类
Tile 类包含它所代表的 tile 处的所有数据。最重要的字段是 type 和 wall，它们表示该位置存在的 TileType 和 WallType。有关 Tile 类的各种字段和方法的更多详细信息，请参阅 [Tile 类文档](https://github.com/tModLoader/tModLoader/wiki/Tile-Class-Documentation)。

## Framed vs FrameImportant Tiles
重要的是要记住泰拉瑞亚中的 Tile 有两种基本类型。有普通的地形图块，如泥土、矿石和石头，也有不是地形图块的图块，如树木、铁砧、绘画等。地形图块被称为“框架”图块，因为游戏会根据附近的图块调整它们的外观。例如，将宝石火花块放置在现有的宝石火花块 Tile 旁边会改变原始宝石火花块的外观，使它们看起来像一个单一的矿石矿床。
![](https://camo.githubusercontent.com/9f24fe23607c0e0dd10c0d3dec66ed2a88e849a83fa5223e590596a6e83c48d5/68747470733a2f2f692e696d6775722e636f6d2f7052365a4355582e706e67)
其他 Tile，称为 FrameImportant 或 multitiles，具有不变的定义外观。要知道的重要一点是，用其他 Framed tile 替换 Framed tile 很容易，只需将 Tile.type 设置为新的 Tile 类型。尝试手动放置 FrameImportant Tile 或替换它们要困难得多。

## Framing
Framing 是游戏调整 Tile 的 Tile.frameX 和 Tile.frameY 值以调整其外观以适应其上下文的过程。在世界生成代码中，您无需担心 Framing，因为游戏在加载世界时会自动 Framing 所有 Tile。如果您在游戏中更改 Tile，您需要告诉游戏框出附近的 Tile。 [ExampleSolution.cs](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/Projectiles/ExampleSolution.cs#L68) 显示了需要 tile framing 和 syncing 的情况。这是一个无框 Tile的例子。在这个例子中，所有 gemspark 块的 tile.frameX 和 tile.frameY 值都是 0。如果你在游戏中看到过这样的图块，那么你的代码就有问题。
![](https://camo.githubusercontent.com/1673ade895034e9349bd161e1fef2d00325226518eaecc9fea3da9640c50c4bf/68747470733a2f2f692e696d6775722e636f6d2f61703077444b482e706e67)
这是 tile framing 后的结果。
![](https://camo.githubusercontent.com/0e837a88fe31b825bf6c1b39252d06da0f513872196b8b48427efaec45cd595b/68747470733a2f2f692e696d6775722e636f6d2f4e3570526b51692e706e67)
在这里，我们可以看到 tile 如何使用 frameX 和 frameY 来确定要绘制的 spritesheet 的部分。例如，用红色勾勒的 tile 的 frameX 为 36，frameY 或 0，这意味着从像素坐标 36, 0 开始的精灵的 16x16 部分是为此 tile 绘制的。
![](https://camo.githubusercontent.com/e261e12eb879230973625747c1098c6b62bc1764154174be9c97a2cdb5d48f96/68747470733a2f2f692e696d6775722e636f6d2f4b396b525958492e706e67)

# 调试世界生成
测试世界生成代码可能非常耗时。首先你必须进行代码更改，构建并重新加载 mod，等待世界完成生成，然后探索新生成的世界以查看最终结果。可以简化此过程，以提高编写和测试世界生成代码的效率。

为了提高生产力，最好有一个可以快速完成代码测试的设置。本指南中显示的短视频和图片都是通过允许在游戏中编辑源代码、在游戏中手动触发世界生成步骤以及撤消世界更改的设置完成的。此设置不是必需的，但可以使编写和测试世界生成代码所需的猜测更少，效率更高。

在盲目测试完整的通过之前测试通过的各个步骤是一个好主意。从小处着手，慢慢扩大测试范围。例如，要测试添加矿石的过程，首先使用以下过程测试单个 WorldGen.TileRunner 执行以获取正确的参数。一旦开始工作，独立测试完成的通过，以验证矿石的频率是否正确。最后，如果需要，在真实世界的生成场景中测试 pass 以确保它完全工作。

## 设置
- 1. 下载并启用以下模组：HEROs Mod 和 Modders Toolkit
- 2. 将以下代码添加到您的项目中，确保修复命名空间。熟悉此过程后，您可以稍后替换 WorldGen.TileRunner 方法：

```csharp
using Terraria;
using Terraria.ModLoader;
using Microsoft.Xna.Framework.Input;
using Terraria.ID;
using Microsoft.Xna.Framework;
using Terraria.World.Generation;
using Terraria.GameContent.Generation;

namespace WorldGenTutorial
{
	class WorldGenTutorialWorld : ModWorld
	{
		public static bool JustPressed(Keys key) {
			return Main.keyState.IsKeyDown(key) && !Main.oldKeyState.IsKeyDown(key);
		}

		public override void PostUpdate() {
			if (JustPressed(Keys.D1))
				TestMethod((int)Main.MouseWorld.X / 16, (int)Main.MouseWorld.Y / 16);
		}

		private void TestMethod(int x, int y) {
			Dust.QuickBox(new Vector2(x, y) * 16, new Vector2(x + 1, y + 1) * 16, 2, Color.YellowGreen, null);

			// Code to test placed here:
			WorldGen.TileRunner(x - 1, y, WorldGen.genRand.Next(3, 8), WorldGen.genRand.Next(2, 8), TileID.CobaltBrick);
		}
	}
}
```

- 3. 确保 tModLoader 已关闭，然后开始调试您的 mod。在 mod 构建后，tModLoader 将启动。当 tModLoader 启动时，打开一个你不关心的世界。将 Visual Studio 放在屏幕的一侧，将 tModLoader 置于窗口模式的另一侧：
![](https://camo.githubusercontent.com/1939ad854c7ae33a5b5b644bcea82275763e3773a328289e516f3fbd4ec9d2d0/68747470733a2f2f692e696d6775722e636f6d2f4b7a62356f6b6b2e706e67)
- 4. 在 HEROsMod 中，单击按钮禁用敌人生成，将 Light Hack 设置为 100%，打开上帝模式，然后显示地图。这些设置将让您专注。
![](https://camo.githubusercontent.com/108c2a895260cb81634a2141271062eeb7a10f57e714511a603a346e847035b7/68747470733a2f2f692e696d6775722e636f6d2f4a576c693637562e706e67)
- 5. 启用 HEROsMod 后，右键单击全屏地图将传送玩家。传送到您最终希望放置世界生成代码的区域的典型区域。
- 6. 如果您正在测试的代码非常具有破坏性，请使用 Modders Toolkit 的 Miscellaneous Tool 菜单中的 Take World Snapshot 按钮来保留世界的副本。 （按钮位于屏幕右下方）
![](https://camo.githubusercontent.com/ead3beeda13bd2f95979edcd9bd775b0b18feea02499d0b3cc02c2762574a262/68747470733a2f2f692e696d6775722e636f6d2f6e31793955396e2e706e67)
- 7. 在您希望试验的代码行上设置断点。单击要试验的行的“gutter”设置断点。：
![](https://camo.githubusercontent.com/2ed0ca39743034e8c995e9607cba096b5a31a0c82631ad65925aac0a69de1a5d/68747470733a2f2f692e696d6775722e636f6d2f485363664a67732e706e67)
- 8. 重复以下操作
  - 将鼠标悬停在您希望尝试测试代码的 tile 上，然后按键盘上的 1 键。
  - Visual Studio 将立即获得焦点，因为它已到达我们设置的断点。这将由 gutter 中的黄色箭头指示：
![](https://camo.githubusercontent.com/f7989b1a22d6d33c7cd0229d932f497221b5994f70ce8c8c2bc98c11c69d8958/68747470733a2f2f692e696d6775722e636f6d2f417777756d494b2e706e67)
  - 现在您可以编辑代码。如果这是您第一次，只需按 F5 继续。否则，在黄色箭头处或下方更改变量和其他逻辑。完成更改后，按 F5 继续。
  - 回到 tModLoader，您应该会短暂地看到一个方形的灰尘，指示您运行代码的坐标。您还将看到代码的效果。
  - 如果世界生成代码具有破坏性，请按“恢复世界快照”按钮。更改应还原。
  - 如果您的代码效果是您想要的，那么恭喜您，您现在对要在世界生成过程中使用的代码有了一个很好的了解。您可以复制代码并适当地使用它。否则，请重复这些步骤，直到您发现符合您要求的参数和值。

## 通过实验学习
许多可用于世界生成的方法根本没有记录。我们可以使用上面的设置来发现 WorldGen.DigTunnel。

首先，让我们将 WorldGen.TileRunner 换成 WorldGen.DigTunnel。接下来，让我们看一下参数名称并猜测一些合适的值。对于 X 和 Y，我们可以猜测它可能是在询问一些 Tile 坐标。 xDir 和 yDir 可能会影响方向，我们可以将它们保留为 0。 Steps 和 Size 可能需要一个非零数字，让它们从 1 开始并从那里开始。
![](https://camo.githubusercontent.com/79e2f929847fa910094b3917a3b705669fd544d6fd73e1a755eee04389479d59/68747470733a2f2f692e696d6775722e636f6d2f79324733326e642e706e67)

现在我们有了一些代码，我们将执行上述步骤并测试我们的代码。每次看到结果，我们可以再次下断点后编辑代码，看看我们修改的效果，从而了解参数的含义。一旦我们知道了含义，我们就有了在实际世界生成步骤中使用 WorldGen.DigTunnel 方法所需的知识。
使用 WorldGen.digTunnel(x, y, 0, 0, 1, 1, false);，我们得到一个小洞：
![](https://camo.githubusercontent.com/107986cc5eaf6ae29ed64e2aa402795b5ef1a9b29c7a8352a6ef341a4257e00c/68747470733a2f2f692e696d6775722e636f6d2f7a3742536743322e706e67)

让我们用 Size 做实验，这里是 WorldGen.digTunnel(x, y, 0, 0, 1, 10, false); 的结果，我们可以看到 Size 似乎影响了一个半径：
![](https://camo.githubusercontent.com/f75ebf677c85d2d825a97861170799b625355915e116a7008c9979fc0df0a030/68747470733a2f2f692e696d6775722e636f6d2f336179516d776d2e706e67)

让我们用 Steps 来做实验，这里是 WorldGen.digTunnel(x, y, 0, 0, 10, 1, false); 的结果。不清楚这个参数有什么影响，我们可能需要结合其他参数来测试它：
![](https://camo.githubusercontent.com/a1d45308cc754839792eb46938d2422152359c9d68ca14288981f903d3c99528/68747470733a2f2f692e696d6775722e636f6d2f6574744a4e666a2e706e67)

让我们用 Steps 和 xDir 和 yDir 来做实验。通过阅读 WorldGen.DigTunnel 的 vanilla 代码用法，我们可以看到 xDir 和 yDir 通常是介于 -1 和 1 之间的数字，所以让我们试试 WorldGen.digTunnel(x, y, 1, 1, 10, 1, false);。现在我们可以看到 xDir 和 yDir 似乎影响了 Tile 被挖出的方向，而 Steps 似乎表明了这个挖掘过程应该迭代多少次。
![](https://camo.githubusercontent.com/da723570f1fa31626cd1cab0ef9704ce43caf8d318bb1e2d10c23c2a3605b002/68747470733a2f2f692e696d6775722e636f6d2f4a744a37306a462e706e67)

借助我们从实验中获得的参数知识，让我们尝试制作一个向下移动的长孔。我的猜测是高 yDir、高步数和中等大小将满足我们的需求。让我们试试 WorldGen.digTunnel(x, y, 0, 1, 30, 3, false);：
![](https://camo.githubusercontent.com/a2a21b360d0adf54987254e754aaa21aed6811283632fa1b03713aba334dfaf9/68747470733a2f2f692e696d6775722e636f6d2f674763546b63342e706e67)

看起来挺好的。我希望这个实验已经展示了如何在游戏中实时测试代码位可以帮助破译方法和参数的含义。如果您有信心，也可以阅读源代码。

## 高级代码设置
如果您想真正快速地迭代测试参数，您可以...

# 测试完全通过
如果您对 pass 中的各个步骤感到满意，则需要通过生成一个新世界并查看结果是否令人满意来测试完整的 pass。在这个阶段，你应该调整一些东西来控制你的世界生成结构生成的数量。使用 WorldGen Previewer mod 将有助于全面了解您的生物群落和结构在世界上的流行程度。请注意，玩家将使用其他模组，所以尽量不要用你的结构覆盖世界。感觉像是一个真正的发现的稀有世界生成功能对玩家来说是令人兴奋的。

# 代码设置
现在您已经了解了先决条件并进行了高效的设置，现在是学习世界生成代码的基本布局的时候了。所有代码都放在扩展 ModWorld 的类中。这个例子将涵盖产生矿石，一些简单但通常需要的东西。请继续阅读并阅读评论。
```csharp
// 1. 你需要各种 using 语句。如果缺少这些，Visual Studio 会建议这些，但为了方便起见，它们在此处列出。
using System.Collections.Generic;
using Terraria;
using Terraria.GameContent.Generation;
using Terraria.ModLoader;
using Terraria.World.Generation;

// 2. 我们的世界生成代码必须从扩展 ModWorld 的类开始
public class WorldGenTutorialWorld : ModWorld
{
	// 3. 我们使用 ModifyWorldGenTasks 方法告诉游戏我们的世界生成代码应该运行的顺序
	public override void ModifyWorldGenTasks(List<GenPass> tasks, ref float totalWeight) {
    // 4. 我们使用 FindIndex 来定位名为“Shinies”的香草世界生成任务的索引。这确保我们的代码在正确的步骤运行。
		int ShiniesIndex = tasks.FindIndex(genpass => genpass.Name.Equals("Shinies"));
		if (ShiniesIndex != -1) {
			// 5. 我们通过传入一个名称和将执行我们的世界生成代码的方法来注册我们的世界生成通行证。
			tasks.Insert(ShiniesIndex + 1, new PassLegacy("World Gen Tutorial Ores", WorldGenTutorialOres));
		}
	}

	// 6. 这是实际的世界生成代码。
	private void WorldGenTutorialOres(GenerationProgress progress) {
		// 7. 设置进度消息总是一个好主意。这是用户在世界生成期间看到的消息，可用于识别无限循环。    
		progress.Message = "World Gen Tutorial Ores";

		// 8. 这里我们使用一个for循环来多次运行循环内的代码。 此 for 循环可扩展到 Main.maxTilesX、Main.maxTilesY 和 2E-05 的乘积。 2E-05 是科学计数法，等于 0.00002。 有时，在处理大量零时，科学记数法更容易阅读。
		// 9. 在一个小世界里，这个数学结果是 4200 * 1200 * 0.00002，大约是 100。这意味着我们将在 for 循环中运行代码 100 次。 这是猩红矿或魔矿生成的数量。 由于我们按世界大小的两个维度进行缩放，因此生成的数量将自动调整为不同的世界大小，以实现一致的矿石分布。
		for (int k = 0; k < (int)((Main.maxTilesX * Main.maxTilesY) * 6E-05); k++) {
			// 10. 我们随机选择一个 x 和 y 坐标。 x 坐标是从最左边到最右边的坐标中选择的。 然而，y 坐标是从 WorldGen.worldSurfaceLow 和地图底部之间选择的。 我们可以使用这种技术来确定我们的矿石应该生成的深度。
			int x = WorldGen.genRand.Next(0, Main.maxTilesX);
			int y = WorldGen.genRand.Next((int)WorldGen.worldSurfaceLow, Main.maxTilesY);

			// 11. 最后，我们做实际的世界生成代码。 在此示例中，我们使用 WorldGen.TileRunner 方法。 此方法生成我们提供给该方法的 Tile 类型的斑点。 TileRunner 的行为在下面的有用方法部分中有详细说明。
			WorldGen.TileRunner(x, y, WorldGen.genRand.Next(3, 6), WorldGen.genRand.Next(2, 6), TileID.CobaltBrick);
		}
	}
}
```
如您所见，ModifyWorldGenTasks 用于注册您的每个世界生成通行证。每个通道都有一个相应的方法，可以对世界进行一些编辑。我们将我们的通行证插入到原版世界生成通行证顺序中，以确保我们的代码在适当的时候执行。

## 添加额外的世界生成代码
要添加更多世界生成代码，首先确定您是否希望向现有通行证添加一个步骤，或者您是否希望创建一个新通行证。如果 pass 与现有 pass 没有有意义的连接，或者现有 mod 或 vanilla pass 的顺序要求新代码存在于其他地方，则创建新 pass 很有用。

例如，如果我们希望在世界中生成宝箱，则不能在生成矿石的同一通道中执行此操作，因为矿石在生成重要的 tile 之前生成。矿石生成会腐蚀过早放置的箱子。如果你正在制作一个生物群系，代码挖洞和代码放置地形可以在同一个通道中共存。

如果我们想生成额外的矿石，我们可以简单地在上面的 WorldGenTutorialOres 示例中添加另一个 for 循环，并调整数字以适应新矿石。这将被称为在传递中添加一个步骤。

如果我们想放置箱子，我们会在 ModifyWorldGenTasks 中添加类似于以下的代码：
```csharp
int BuriedChestsIndex = tasks.FindIndex(genpass => genpass.Name.Equals("Buried Chests"));
if (BuriedChestsIndex != -1) {
	tasks.Insert(BuriedChestsIndex + 1, new PassLegacy("World Gen Tutorial Chests", WorldGenTutorialChests));
}
```
然后也添加 WorldGenTutorialChests 方法。确保不要弄乱 c# 语法：
```csharp
private void WorldGenTutorialChests(GenerationProgress progress) {
    // Chest placement code here
}
```

## 调试注意事项
如果您使用上述调试设置，我们需要进一步分解我们的代码以方便测试。在这里，我们可以看到可以从 PostUpdate 中的热键代码和名为 WorldGenTutorialOres 的世界生成步骤中调用 PlaceOresAtLocation，从而允许对代码进行独立测试。
```csharp
public override void PostUpdate() {
    if (JustPressed(Microsoft.Xna.Framework.Input.Keys.D1))
        PlaceOresAtLocation((int)Main.MouseWorld.X / 16, (int)Main.MouseWorld.Y / 16);
}

private void WorldGenTutorialOres(GenerationProgress progress) {
    progress.Message = "World Gen Tutorial Ores";

    for (int k = 0; k < (int)((Main.maxTilesX * Main.maxTilesY) * 6E-05); k++) {
        int x = WorldGen.genRand.Next(0, Main.maxTilesX);
        int y = WorldGen.genRand.Next((int)WorldGen.worldSurfaceLow, Main.maxTilesY);

        PlaceOresAtLocation(x, y);
    }
}

// PlaceOresAtLocation 在我们的调试热键代码和 WorldGenTutorialOres 方法之间共享。这使我们可以在游戏中快速测试这部分代码，但也可以在世界生成步骤中使用代码。
private void PlaceOresAtLocation(int x, int y) {
    WorldGen.TileRunner(x, y, WorldGen.genRand.Next(3, 6), WorldGen.genRand.Next(2, 6), TileID.CobaltBrick);
}
```

# 确定合适的索引
咨询[Vanilla World Generation Passes](https://github.com/tModLoader/tModLoader/wiki/Vanilla-World-Generation-Steps)，找到一个合适的地方插入您的世界世代通行证。在类似的世界生成通过后立即执行类似的代码通常是一个很好的规则。一些早期的通行证不考虑多块，所以避免过早放置箱子或其他多块。相同的概念适用于各种地形塑造方法，因为太晚使用这些方法可能会损坏已经放置的多块，导致它们破裂或看起来不完整。
注意：不要将 FindIndex 调用分组到 task.Insert 代码之上。如果这样做，则索引可能是错误的。下面是一个潜在问题的示例，该问题可能是由于在错误的步骤中运行代码而导致的。在这里，我们看到 TileRunner 代码已经损坏了多个 Tile ，例如门、箱子和其他装饰 Tile：
![](https://camo.githubusercontent.com/69171d0a21ff7ceda8b13fd5157090b1690abae5cc92c64b8571981acc012215/68747470733a2f2f692e696d6775722e636f6d2f54424a677361672e706e67)

## 香草世界生成时间线
本节列出了在世界生成期间发生的各种重要事件，这些事件将帮助您确定适合一般世界生成通行证的索引：
- 地狱
- 生成点：分配了 Main.spawnTileX 和 Main.spawnTileY
- TODO - 寻找重要的通行证：大型地形编辑的最后机会，如何避免损坏箱子等。

# 确定起始位置
大多数世界生成步骤随机选择一个坐标开始。我们可以通过调整我们对这个初始坐标的选择来调整我们的世界生成代码的分布。

## Random
我们使用 WorldGen.genRand.Next 方法来选择一个随机数。对所有随机决策使用 WorldGen.genRand 很重要，因为它有助于世界种子功能。

## Width
TODO：地图中心、重生点周围的安全区、海洋位置

## Depth
从下到下，以下是 worldgen 期间可用的深度：0、Worldgen.worldSurfaceLow、Worldgen.worldSurfaceHigh、Worldgen.rockLayerLow、Worldgen.rockLayerHigh、Main.maxTilesY。通过调整提供给 WorldGen.genRand.Next 方法的最小值和最大值，我们可以告诉游戏我们希望我们的矿石生成的深度范围。以下是游戏中的铜矿生成代码，它使用 3 个具有不同参数和循环乘数的独立 for 循环来使矿床越深越频繁：
```csharp
for (int i = 0; i < (int)((double)(Main.maxTilesX * Main.maxTilesY) * 6E-05); i++) {
	TileRunner(WorldGen.genRand.Next(0, Main.maxTilesX), WorldGen.genRand.Next((int)WorldGen.worldSurfaceLow, (int)WorldGen.worldSurfaceHigh), WorldGen.genRand.Next(3, 6), WorldGen.genRand.Next(2, 6), copper);
}

for (int i = 0; i < (int)((double)(Main.maxTilesX * Main.maxTilesY) * 8E-05); i++) {
	TileRunner(WorldGen.genRand.Next(0, Main.maxTilesX), WorldGen.genRand.Next((int)WorldGen.worldSurfaceHigh, (int)WorldGen.rockLayerHigh), WorldGen.genRand.Next(3, 7), WorldGen.genRand.Next(3, 7), copper);
}

for (int i = 0; i < (int)((double)(Main.maxTilesX * Main.maxTilesY) * 0.0002); i++) {
	TileRunner(WorldGen.genRand.Next(0, Main.maxTilesX), WorldGen.genRand.Next((int)WorldGen.rockLayerLow, Main.maxTilesY), WorldGen.genRand.Next(4, 9), WorldGen.genRand.Next(4, 8), copper);
}
```
请注意，WorldGen.rockLayerLow 和 Main.maxTilesY 之间的距离远大于其他 2 个范围，因此矿石的分布并不像 * 0.0002 所暗示的那样密集。

另请注意，并非所有这些值都保留在游戏中。例如，Worldgen.worldSurfaceLow 和 Worldgen.worldSurfaceHigh 被遗忘，只剩下 Main.worldSurface。 Main.worldSurface 等于 Worldgen.worldSurfaceHigh + 25.0。确保如果您在执行游戏世界生成代码时引用了实际加载的变量，您可以检查 Terraria.IO.WorldFile.LoadHeader 进行仔细检查。

地狱位于地图底部的 200 个图块上。 Main.maxTilesX - 200 及以下将产生冥界坐标。

## 生物群落
我们可以在随机坐标处检查现有的 Tile 以确定所选位置的生物群落。例如，如果我们只想在雪附近放置矿石，我们可以检查雪砖：
```csharp
Tile tile = Main.tile[x, y];
if (tile.active() && tile.type == TileID.SnowBlock) {
    // TileRunner code here
}
```
在检查这样的条件时，重要的是要考虑是否希望循环计数器在失败时增加或保持不变。可能所有选择的随机坐标都可能不包含雪，并且世界不会受到您的代码的影响。另一方面，如果你重复你的世界生成代码直到找到雪的特定次数，一个有少量雪的世界可能会不成比例地受到你的世界生成代码的影响。在设计代码时要注意这种可能性。

### Spawn
Main.spawnTileX 和 Main.spawnTileY 指示默认生成位置。通常 Main.spawnTileX 位于 Main.maxTilesX / 2 的 5 格内，但模组可以改变这一点。生成位置在“生成点”通道中分配。

### 地牢
Main.dungeonX 和 Main.dungeonY 指向地牢入口处的 Tile。 TODO：dungeonSide 说明，关于何时设置 dungeonXY 的信息
![](https://camo.githubusercontent.com/f0c610c5fc98d7737c745538c4e84a8674c10d8cde3ab0ce2e20e30ed647db72/68747470733a2f2f692e696d6775722e636f6d2f425078613641302e706e67)

### 寺庙
神殿的位置不存储在世界文件中，但是如果您在所有图块中搜索 TileID.LihzahrdAltar 或上锁的门，您可能会找到它，但不能保证。
TODO：On.makeTemple 示例

### 金字塔
金字塔坐标也不会被记住。

TODO：使用反射来检索通过闭包示例捕获的局部变量的 FieldInfo。检索 PyrX 和 PyrY。 （仔细检查编辑封闭变量是否可以修改原始捕获的变量值）

## 查找表面位置
要找到表面坐标，首先选择一个随机的 X 坐标，然后从世界顶部开始检查所有 Tile，直到找到第一个实心 Tile。这是一个例子：
```csharp
int x = WorldGen.genRand.Next(0, Main.maxTilesX);
bool foundSurface = false;
int y = 1;
while (y < Main.worldSurface) {
	if (WorldGen.SolidTile(x, y)) {
		foundSurface = true;
		break;
	}
	y++;
}
```
在这个例子中，我们检查 Main.worldSurface 以确保我们不会走得太深。例如，这是为了确保您不会尝试在深坑中间生成地表生物群系。

# 常见模式
## 尝试直到成功
为许多世界生成操作寻找合适的位置可能很困难。例如，放置一个箱子需要 2 个并排的实心 Tile，上面有 2x2 的空间，没有任何 Tile。编写一个算法来搜索具有这种情况的位置可能很困难并且容易出错。虽然有时搜索特定上下文很有用，但以更懒惰的方式生成世界代码是非常常见的。这种更懒惰的方式是尝试在随机坐标上做某事，直到获得所需的成功次数。例如，如果您希望每个世界生成 4 个特殊箱子，您可能会尝试将箱子随机放置在所需区域，直到 PlaceChest 报告成功 4 次。执行此方法时，存在搜索区域不包含任何满足您条件的位置的可能性，因此限制尝试很有用。如果您不限制尝试，您的代码可能会陷入无限循环。这个尝试限制应该足够大，以至于它不会过早失败，但又足够小，以至于世界生成不会暂停太久，导致用户假设代码陷入了无限循环。

例如，让我们尝试在世界上放置 10 个箱子：
```csharp
for (int i = 0; i < 10; i++) {
	bool success = false;
	int attempts = 0;
	while (!success) {
		attempts++;
		if (attempts > 1000) {
			break;
		}
		x = WorldGen.genRand.Next(0, Main.maxTilesX);
		y = WorldGen.genRand.Next(0, Main.maxTilesY);
		int chest = WorldGen.PlaceChest(x, y);
		success = chest != -1;
	}
	if(success)
		Main.NewText($"Placed chest at {x}, {y} after {attempts} attempts.");
	else
		Main.NewText($"Failed to place chest after {attempts} attempts.");
}
```
在这个例子中，我们尝试放置 10 个箱子，每个箱子尝试 1000 次。这是输出：
![](https://camo.githubusercontent.com/547118fe6889148b78bb8db51ad3cb8bb8449df3003e0646091aedd723c1eb47/68747470733a2f2f692e696d6775722e636f6d2f616162766374692e706e67)

在这里您可以看到，使用随机坐标，您通常可以使用 WorldGen.PlaceChest 在我们允许的 1000 次尝试中放置一个箱子。将 1000 提升到 10000 不是问题，但几乎可以保证 10 个箱子，而不是我们在这里看到的 9 个成功。计算机非常快，数以万计的放置尝试并不是什么大问题。最重要的是让你的循环在多次尝试后失败，你不希望你的世界生成代码陷入无限循环。

## 影响所有 Tile
有时你想对所有的 Tile 做一些事情。例如，将所有铁矿石图块更改为 MyCoolOre 图块。您可以这样做，但请注意，像这样应用一揽子更改可能会与其他 mod 的期望相冲突。此外，最好在 PostWorldGen 或后期通行证中执行此操作，以允许查找这些图块的其他代码首先完成其工作。为此，我们使用双 for 循环：
```csharp
for (int i = 0; i < Main.maxTilesX; i++) {
	for (int j = 0; j < Main.maxTilesY; j++) {
		Tile tile = Main.tile[i, j];
		if (tile.type == TileID.Iron)
			tile.type = (ushort)ModContent.TileType<MyCoolOre>();
	}
}
```

## 放置 Tile 实体
TODO：必须手动放置，因为它们没有以正常方式放置

## 将物品放入箱子
使用 PlaceChest 将箱子添加到世界后，您可以通过访问该位置的 Chest 对象的项目数组来添加项目。如果使用 AddBuriedChest，则不会将 Chest 或 Chest 索引返回给调用者，因此您无法在不搜索 Chest 的情况下修改内容。

### 将物品放入新箱子
以下示例显示了添加项目的许多方法。要记住的重要一点是正确跟踪您正在编辑的 Item 插槽的当前索引。此示例在末尾添加所有项目以简化此操作。
```csharp
// 放置箱子 Tile，使用Style 10，即冰冷的箱子 style
int chestIndex = WorldGen.PlaceChest(x, y, style: 10);
// 如果箱子成功放置...
if(chestIndex != -1) {
	Chest chest = Main.chest[chestIndex];
	// itemsToAdd 将保存我们要添加到箱子的每个项目的类型和堆栈数据
	var itemsToAdd = new List<(int type, int stack)>();

	// 这是一个使用 WeightedRandom 为不同项目随机选择不同权重的示例。
	int specialItem = new Terraria.Utilities.WeightedRandom<int>(
		Tuple.Create((int)ItemID.Acorn, 1.0),
		Tuple.Create((int)ItemID.Meowmere, 0.1),
		Tuple.Create(ModContent.ItemType<MyItem>(), 1.0),
		Tuple.Create((int)ItemID.None, 7.0) // 不选择权重为 7 的项目。
	);
	if(specialItem != ItemID.None) {
		itemsToAdd.Add((specialItem, 1));
	}
	// 使用 switch 语句和随机选择来添加项目集。
	switch (Main.rand.Next(4)) {
		case 0: 
			itemsToAdd.Add((ItemID.CobaltOre, Main.rand.Next(9, 15)));
			break;
		case 1:
			itemsToAdd.Add((ItemID.Duck, 1));
			break;
		case 2:
			itemsToAdd.Add((ItemID.FireblossomSeeds, Main.rand.Next(2, 5)));
			break;
		case 3:
			itemsToAdd.Add((ItemID.Glowstick, Main.rand.Next(9, 15)));
			itemsToAdd.Add((ItemID.Dynamite, Main.rand.Next(1, 3)));
			itemsToAdd.Add((ItemID.Bomb, Main.rand.Next(3, 7)));
			break;
	}

	// 最后，遍历 itemsToAdd 并实际创建 Item 实例并添加到 chest.item 数组
	int chestItemIndex = 0;
	foreach (var itemToAdd in itemsToAdd) {
		Item item = new Item();
		item.SetDefaults(itemToAdd.type);
		item.stack = itemToAdd.stack;
		chest.item[chestItemIndex] = item;
		chestItemIndex++;
		if (chestItemIndex >= 40)
			break; // 确保不要超过箱子的容量
	}
}
```
### 将物品放置在其他现有的箱子中
ExampleWorld.cs 显示了一个将单个项目放置在由其他代码放置的箱子中的示例。[在冰柜中放置一些物品](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/ExampleWorld.cs#L380)。如果你想放置多个物品，你也可以使用上面显示的技术，你只需要确保按照逻辑在箱子中找到一个空的物品槽，这样你就不会覆盖现有的物品。

## 液体
液体存储在与实际 tile 共存的 Tile 对象中（如果存在）。

许多大规模地形方法都有参数，可以选择在生成的地形中放置水。例如，Worlgen.digTunnel 有一个湿参数，它会在挖完洞后用一些水填充洞。在其他方法中寻找类似的参数。要手动放置单块水，您可以通过以下方式设置该块的液体类型和液体量：
```csharp
Main.tile[i,j].liquid = 255;
Main.tile[i,j].liquidType(Tile.Liquid_Water);
```

# 游戏内/多人游戏注意事项
TODO:
  - 许多方法并非设计用于多人游戏。
  - 有关如何同步 Tile 更改的示例。不发送要避免的图块更改的方法示例。
  - 过早离开时节省腐败。 （tModLoader 需要 WorldGen.IsGeneratingHardMode 等效钩子）
  - 由于同步代码导致服务器崩溃。 （需要异步代码示例，线程静态警告）
  - 始终在游戏中使用 Framing.GetTileSafely
  - 确保代码在服务器上运行

# Stamp Tiles
有时，模组希望将精心设计的建筑物或其他设计特征放置到世界中。编写代码以手动单独放置每个图块非常麻烦。有一种方法可以将选择的 Tile “标记”到世界中。它的工作原理是您首先在游戏中设计结构，然后使用 TODOMETHODNAME 方法导出表示该 Tile 选择的二进制文件. 您可以将该文件添加到您的 mod 并在 worldgen 通行证中引用它。您可以使用 TODOMETHODNAME 方法将该文件转换为二维 Tile 数组。获得 Tile 后，您可以找到合适的位置并将 Tile 复制到该位置，就像您在该位置上盖印 Tile 一样。使用这种方法时，您可能需要花费一些精力来确保所选位置与您放置的 Tile 很好地融合在一起。

# 程序语法
在原版代码中最近添加的许多世界生成代码中，可以看到一种更强大的典型世界生成代码方法。这种方法使泰拉瑞亚更令人印象深刻的世界生成功能成为可能。活红木树和附魔剑坛的有机流动就是这种方法的力量的很好例子。这种程序方法提供了一种以更简洁和不易出错的方式将条件和动作链接在一起的方法。如果您不熟悉高级 C# 语法模式，语法会令人困惑，但一旦掌握了它，这种方法就会非常强大和高效。

## 快速示例
作为这种方法的快速入门，这里有一个简单的例子：
```csharp
Point point = new Point(x, y);
WorldUtils.Gen(point, new Shapes.Circle(8, 8), new Actions.SetTile(TileID.RubyGemspark));
```
这段代码令人生畏，但如果你学会阅读它，它确实还不错。 WorldUtils.Gen 方法基本上采用 Point、GenShape 和 GenAction。从 Point 表示的坐标开始，GenShape 的代码在每个坐标上运行 GenAction 代码的同时追踪所需的形状。此代码在圆内的每个坐标上运行 SetTile 方法，创建一个半径为 8 且填充有 Gemspark Tile的圆。
![](https://camo.githubusercontent.com/4bf7dedc965e54e1dcf0f2a0f1bdbd7e5f87066fb9be13c71de1b71fee67e0b8/68747470733a2f2f692e696d6775722e636f6d2f4a56726d7473322e706e67)

```csharp
Point point = new Point(x, y);
WorldUtils.Gen(point, new Shapes.Circle(8, 4), Actions.Chain(new GenAction[]
{
	new Actions.SetTile(TileID.AmberGemspark),
	new Actions.PlaceWall(WallID.BlueDynasty),
	new Actions.Custom((i, j, args) => {Dust.QuickDust(new Point(i, j), Color.Purple); return true; }),
}));
```
此示例显示使用 Actions.Chain 将多个 GenAction 链接在一起。我们放置 AmberGemspark，放置蓝色王朝墙，并生成紫色尘埃。灰尘只是用来帮助可视化所有可能受所提供形状影响的图块。
![](https://camo.githubusercontent.com/963ab9fe263ada4846d654f6129563d79eac905ade9762ff740bb26838d11652/68747470733a2f2f692e696d6775722e636f6d2f427259346733312e706e67)

## GenShape
GenShapes 用于指定动作发生的位置。圆形和矩形等香草形状是不言自明的，您可能需要尝试其他形状。使用 GenShape 的一个很好的例子是 EnchantedSwordBiome 类。这个类负责世界生成代码的一般形状。

### GenModShape
从 GenModShape 继承的类使用输入的 ShapeData 点来驱动它们的坐标。例如，ModShapes.InnerOutline 可用于影响由 ShapeData 提供的点集的内部轮廓。

### 自定义 GenShape
从 GenShape 继承允许使用自定义形状。
```csharp
// World Gen Code
Point point = new Point(x, y);
WorldUtils.Gen(point, new AngularSpiral(8), new Actions.SetTile(TileID.RubyGemspark));
WorldUtils.Gen(point, new AngularSpiral(8), new Actions.SetFrames());

// Custom GenShape class
public class AngularSpiral : GenShape
{
	private int radius;

	public AngularSpiral(int radius) {
		this.radius = radius;
	}

	public override bool Perform(Point origin, GenAction action) {
		int i = 0;
		int j = 0;
		int dx = 0;
		int dy = -1;
		while(i <= radius && j <= radius) { 
			if(-origin.X/2< i && i <= origin.X / 2 && -origin.Y / 2 < j && j <= origin.Y / 2)
				if (!UnitApply(action, origin, origin.X + i, origin.Y + j) && _quitOnFail)
					return false;
			if(i == j || (i<0 && i == -j) || (i>0 && i == 2 - j))
				(dx, dy) = (-dy, dx);
			(i, j) = (i + dx, j + dy);
		}
		return true;
	}
}
```
![](https://camo.githubusercontent.com/986f88023e641437617fc3cf88a89164ae529d745692f8e97016a2f318d4362b/68747470733a2f2f692e696d6775722e636f6d2f363238564a55592e706e67)

## GenAction
GenActions 指示影响由 GenShape 提供的坐标的代码。一些常见的操作包括 SetTile，用于设置图块类型，以及 Scanner，用于计算 GenShape 的迭代次数。

### Scanner
Scanner 可用于计算当前有多少块满足 Actions.Chain 的条件。这对于查找主要是某种情况或其他情况的斑点很有用。例如，如果您想查找 90% 实心 Tile 的位置，您可以将扫描仪的结果与检查的 Tile 总数进行比较。这个例子展示了如何通过 `Ref<int>` 使用 Scanner。此示例还显示了 Actions.ContinueWrapper，它允许将条件分成子链，当它们失败时不会停止其他链。 （通常，当 Action 返回 false 时，链将终止。）
```csharp
Ref<int> anyCount = new Ref<int>(0);
Ref<int> solidCount = new Ref<int>(0);
Ref<int> notsolidCount = new Ref<int>(0);
WorldUtils.Gen(point, new Shapes.Rectangle(10, 6), Actions.Chain(new GenAction[]
{
	new Actions.ContinueWrapper(Actions.Chain(new GenAction[]
	{
		new Modifiers.IsNotSolid(),
		new Actions.Custom((i, j, args) => {Dust.QuickDust(new Point(i, j), Color.Purple); return true; }),
		new Actions.Scanner(notsolidCount)
	})),
	new Actions.ContinueWrapper(Actions.Chain(new GenAction[]
	{
		new Modifiers.IsSolid(),
		new Actions.Custom((i, j, args) => {Dust.QuickDust(new Point(i, j), Color.YellowGreen); return true; }),
		new Actions.Scanner(solidCount)
	})),
	new Actions.Scanner(anyCount),
}));
Main.NewText($"Any {anyCount.Value}, Solid {solidCount.Value}, NotSolid {notsolidCount.Value}");
```
![](https://camo.githubusercontent.com/ab262f62ebab28210ce7b728d252fc95fbf20ca0b7ac966e04bb0d8cff48eaba/68747470733a2f2f692e696d6775722e636f6d2f5741624a4f686e2e706e67)

### TileScanner
TileScanner 按给定形状中的类型计算图块。 TileScanner 通过检查附近的 Tile 来帮助计算位置是否合适。它有助于避免与其他世界生成元素重叠，并有助于将世界生成要素放置在与所需位置完全匹配的位置。以下示例使用 TileScanner 检查测试区域中 50% 的 Tile 是石头还是泥土。通过调整我们的标准，我们可以保证我们的世界生成元素的放置令人愉悦。
```csharp
Point point = new Point(x, y);
Dictionary<ushort, int> dictionary = new Dictionary<ushort, int>();
WorldUtils.Gen(point, new Shapes.Rectangle(20, 10), new Actions.TileScanner(TileID.Dirt, TileID.Stone).Output(dictionary));
int stoneAndDirtCount = dictionary[TileID.Dirt] + dictionary[TileID.Stone];
// 20 * 10 == 200. This is checking that at least 75% of the area is Stone or Dirt
if (stoneAndDirtCount < 150)
	Main.NewText($"Not a suitable location: {stoneAndDirtCount}/200");
else
	Main.NewText($"A Suitable location: {stoneAndDirtCount}/200");
Dust.QuickBox(new Vector2(x, y) * 16, new Vector2(x + 20, y + 10) * 16, 20, Color.Orange, null);
```
![](https://camo.githubusercontent.com/9d0cd9b7dd7d1f95198ad49fba25b0d98226e0dc3a8ac4cccbfb12e2826275d5/68747470733a2f2f692e696d6775722e636f6d2f714233634b57762e706e67)

### 自定义
Actions.Custom GenAction 允许执行任意代码。您想用 GenActions 做的大多数典型事情已经被现有的类所涵盖，但是使用它的一个例子是产生灰尘：
```csharp
new Actions.Custom((i, j, args) => { Dust.QuickDust(new Point(i, j), Color.Red); return true; }),
```

### 自定义 GenAction 
从 GenAction 继承可用于在每个坐标上运行自定义代码。这是一个以 ActionVines 为模型的名为 ActionRope 的示例。自定义 GenAction 类可以帮助组织代码的可重用部分。
```csharp
public class ActionRope : GenAction
{
	private int _minLength;
	private int _maxLength;
	private int _vineId;

	public ActionRope(int minLength = 6, int maxLength = 10, int vineId = TileID.Rope) {
		_minLength = minLength;
		_maxLength = maxLength;
		_vineId = vineId;
	}

	public override bool Apply(Point origin, int x, int y, params object[] args) {
		int num = GenBase._random.Next(_minLength, _maxLength + 1);
		int i;
		for (i = 0; i < num && !GenBase._tiles[x, y + i].active(); i++) {
			GenBase._tiles[x, y + i].type = (ushort)_vineId;
			GenBase._tiles[x, y + i].active(active: true);
		}

		if (i > 0)
			return UnitApply(origin, x, y, args);

		return false;
	}
}

// 使用代码。此代码调用木 Tile 下方的 ActionRope 1 块。 NotTouching 和 Dither 使放置更加随机
WorldUtils.Gen(point, new ModShapes.All(shapeData), Actions.Chain(
	new Modifiers.OnlyTiles(TileID.WoodBlock), 
	new Modifiers.Offset(0, 1), 
	new Modifiers.NotTouching(true, TileID.Rope),
	new Modifiers.Dither(0.5f),
	new ActionRope(5, 9)
));
```
![](https://camo.githubusercontent.com/48fa5d4f867986a6885e3448f1d42ae07580dc20003aa24789fae97e9e09c89c/68747470733a2f2f692e696d6775722e636f6d2f684b695364797a2e706e67)

## Modifier
修饰符是特殊的 GenAction，它限制后续链接的 GenAction 的执行。一个简单的例子是抖动修改器。抖动随机终止动作链。在下面的示例中，圆圈内的所有 Tile 都会产生黄尘，但抖动修改器会在 20% 的时间提前终止链，从而导致如下所示的破烂放置。
```csharp
WorldUtils.Gen(point, new Shapes.Circle(8, 4), Actions.Chain(new GenAction[]
{
	new Actions.Custom((i, j, args) => {Dust.QuickDust(new Point(i, j), Color.Yellow); return true; }),
	new Modifiers.Dither(.2),
	new Actions.SetTile(TileID.AmberGemspark),
}));
```
![](https://camo.githubusercontent.com/cb69a160c0a3fbf9768740962983965e9e65906c1faa74ee5d23ee3f224fac64/68747470733a2f2f692e696d6775722e636f6d2f566146535666362e706e67)

## Output
输出可用于记住特定 GenAction 处的坐标集。在此示例中，我们将 2 个单独的 Circles 的结果输出到共享的 ShapeData。此 ShapeData 被传递给 InnerOutline，后者计算来自该数据的哪些图块形成内部轮廓。通过这种方式，我们基本上合并了两个 GenShapes 的结果，并使用这些结果来制作 Lava Moss Tile 的独特形状。
```csharp
ShapeData shapeData = new ShapeData();
WorldUtils.Gen(point, new Shapes.Circle(5, 5), new Actions.Blank().Output(shapeData));
WorldUtils.Gen(point, new Shapes.Circle(3, 3), Actions.Chain(new GenAction[]
{
	new Modifiers.Offset(9, 0),
	new Actions.Blank().Output(shapeData)
}));
WorldUtils.Gen(point, new ModShapes.InnerOutline(shapeData, true), new Actions.SetTile(TileID.LavaMoss, true));
```
![](https://camo.githubusercontent.com/024293b4c8a695dce61c443484d635fc0668da204dfd0cdd4813b2fbab251d20/68747470733a2f2f692e696d6775722e636f6d2f4d594752596d742e706e67)

## GenCondition
GenConditions 是确定区域或坐标是否满足条件的类。默认情况下，它们只检查提供的坐标，但可用于查找满足条件的区域。面积测量为矩形，提供的坐标为左上角。 GenConditions 通常与 WorldUtils.Find 结合使用，以找到适合步骤的位置。

### AreaAnd
通过使用 AreaAnd 修改 GenCondition，区域内的所有坐标都必须满足条件才能被视为成功。例如，new Conditions.IsSolid().AreaAnd(6, 2) 检查 6 tile 宽 x 2 tile 高区域中的所有 Tile 是否都是实心的。

### AreaOr
AreaOr 检查区域中是否有任何 Tile 满足条件。例如，new Conditions.IsSolid().AreaOr(3, 1) 尝试确定 3x1 区域中的任何 Tile 是否是实心的。

### Not
Not 可以应用于 AreaAnd、AreaOr 或没有区域的 GenCondition。没有区域不应用将反转条件。例如，新的Conditions.IsSolid().Not() 只有在 Tile 不是实心的时候才会成功。不应用于 AreaOr 充当 NOR 操作，因为该区域中的任何 Tile 都不满足条件。new Conditions.IsSolid().Not().AreaOr(3, 5) 将尝试找到一个 3x5 区域，其中没有任何实心 Tile 。不适用于 AreaAnd 充当 NAND 操作，仅当并非所有 Tile 都满足条件时才返回 true，或者换句话说，至少有 1 个 Tile 不满足条件。

### Offset
尚不支持抵消 GenConditions。这意味着单个 Find 中的所有 GenCondition 将共享左上角。

## Find
WorldUtils.Find 可用于搜索满足特定条件的位置。通过使用搜索和许多 GenCondition，该方法尝试找到满足所有条件的坐标。Searches.Down 指示 Find 从输入点开始并向下移动最多 20 个 Tile 以寻找合适的位置。如果搜索成功，该方法返回 true。此示例中的条件尝试查找全部为实心和沙色的 5x5 正方形Tile。如果找到了，黑曜石就会放在中间。黄色的尘埃显示了发现的符合条件的区域。光标显示搜索从地面开始并向下搜索，直到找到最终结果。
```csharp
Point resultPoint;
bool searchSuccessful = WorldUtils.Find(point, Searches.Chain(new Searches.Down(20), new GenCondition[]
{
	new Conditions.IsSolid().AreaAnd(5, 5),
	new Conditions.IsTile(TileID.Sand).AreaAnd(5, 5),
}), out resultPoint);
if (searchSuccessful) {
	Main.tile[resultPoint.X + 2, resultPoint.Y + 2].type = TileID.Obsidian;
}
```
![](https://camo.githubusercontent.com/25a1d3f7570de194e271eb73f9d5d466f66dea427633d460d70b89fb30137e72/68747470733a2f2f692e696d6775722e636f6d2f50583444396f422e706e67)

## 实例探究
以下是一些复杂的示例，可以展示这种方法生成世界代码的全部潜力。

### 附魔剑神殿
Enchanted Sword Shrine 的代码可在 Terraria.GameContent.Biomes.EnchantedSwordBiome 类中找到。本节将研究 EnchantedSwordBiome 如何使用各种技术在合适的位置干净地生成神殿而不会出现问题。跟随下面的评论和视频。
```csharp
public override bool Place(Point origin, StructureMap structures) {
// 通过使用 TileScanner，检查以原点为中心的 50x50 区域主要是 Dirt 或 Stone
Dictionary<ushort, int> tileDictionary = new Dictionary<ushort, int>();
WorldUtils.Gen(new Point(origin.X - 25, origin.Y - 25), new Shapes.Rectangle(50, 50), new Actions.TileScanner(TileID.Dirt, TileID.Stone).Output(tileDictionary));
if (tileDictionary[TileID.Dirt] + tileDictionary[TileID.Stone] < 1250)
	return false;  // 如果不是，则返回 false，这将导致调用方法尝试不同的来源

Point surfacePoint;
// 在上面最多搜索 1000 个图块，以查找 50 个图块高、1 个图块宽且没有单个实心图块的区域。基本上找到表面。
bool flag = WorldUtils.Find(origin, Searches.Chain(new Searches.Up(1000), new Conditions.IsSolid().AreaOr(1, 50).Not()), out surfacePoint);
// 从原点到地表搜索，确保原点和地表之间没有沙子
if (WorldUtils.Find(origin, Searches.Chain(new Searches.Up(origin.Y - surfacePoint.Y), new Conditions.IsTile(TileID.Sand)), out Point _))
	return false;

if (!flag)
	return false;

surfacePoint.Y += 50; // 调整结果指向表面，而不是上面 50 个图块
ShapeData slimeShapeData = new ShapeData();
ShapeData moundShapeData = new ShapeData();
Point point = new Point(origin.X, origin.Y + 20);
Point point2 = new Point(origin.X, origin.Y + 30);
float xScale = 0.8f + GenBase._random.NextFloat() * 0.5f; /// 随机化神社区域的宽度
// 检查 StructureMap 对于我们希望放置神殿的预期区域是否存在任何冲突。
if (!structures.CanPlace(new Rectangle(point.X - (int)(20f * xScale), point.Y - 20, (int)(40f * xScale), 40)))
	return false;
// 检查 StructureMap 对于通向表面的轴是否存在任何冲突
if (!structures.CanPlace(new Rectangle(origin.X, surfacePoint.Y + 10, 1, origin.Y - surfacePoint.Y - 9), 2))
	return false;
// 使用粘土形状，清除 Tile。斑点使边缘看起来更自然。 https://i.imgur.com/WtZaBbn.png
WorldUtils.Gen(point, new Shapes.Slime(20, xScale, 1f), Actions.Chain(new Modifiers.Blotches(2, 0.4), new Actions.ClearTile(frameNeighbors: true).Output(slimeShapeData)));
// 在切出的粘土形状内放置一个土堆
WorldUtils.Gen(point2, new Shapes.Mound(14, 14), Actions.Chain(new Modifiers.Blotches(2, 1, 0.8), new Actions.SetTile(TileID.Dirt), new Actions.SetFrames(frameNeighbors: true).Output(moundShapeData)));
// 从粘土坐标数据中删除土墩坐标
slimeShapeData.Subtract(moundShapeData, point, point2);
// 沿着粘土坐标数据的内部轮廓放置草
WorldUtils.Gen(point, new ModShapes.InnerOutline(slimeShapeData), Actions.Chain(new Actions.SetTile(TileID.Grass), new Actions.SetFrames(frameNeighbors: true)));
// 将水放在粘土形状下半部分的空坐标中
WorldUtils.Gen(point, new ModShapes.All(slimeShapeData), Actions.Chain(new Modifiers.RectangleMask(-40, 40, 0, 40), new Modifiers.IsEmpty(), new Actions.SetLiquid()));
// 在所有粘土形状坐标上放置花墙。将藤蔓放置在粘土形状的所有草方块下方 1 格。
WorldUtils.Gen(point, new ModShapes.All(slimeShapeData), Actions.Chain(new Actions.PlaceWall(WallID.Flower), new Modifiers.OnlyTiles(TileID.Grass), new Modifiers.Offset(0, 1), new ActionVines(3, 5)));
// 移除图块以创建轴到表面。将沿轴的图块转换为硬化的图块。
ShapeData shaftShapeData = new ShapeData();
WorldUtils.Gen(new Point(origin.X, surfacePoint.Y + 10), new Shapes.Rectangle(1, origin.Y - surfacePoint.Y - 9), Actions.Chain(new Modifiers.Blotches(2, 0.2), new Actions.ClearTile().Output(shaftShapeData), new Modifiers.Expand(1), new Modifiers.OnlyTiles(TileID.Sand), new Actions.SetTile(TileID.HardenedSand).Output(shaftShapeData)));
WorldUtils.Gen(new Point(origin.X, surfacePoint.Y + 10), new ModShapes.All(shaftShapeData), new Actions.SetFrames(frameNeighbors: true));
// 33% 的几率放置一个附魔剑神殿Tile
if (GenBase._random.Next(3) == 0)
	WorldGen.PlaceTile(point2.X, point2.Y - 15, TileID.LargePiles2, mute: true, forced: false, -1, 17);
else
	WorldGen.PlaceTile(point2.X, point2.Y - 15, TileID.LargePiles, mute: true, forced: false, -1, 15);
// 将植物放在土堆形状的草砖上方。
WorldUtils.Gen(point2, new ModShapes.All(moundShapeData), Actions.Chain(new Modifiers.Offset(0, -1), new Modifiers.OnlyTiles(TileID.Grass), new Modifiers.Offset(0, -1), new ActionGrass()));
// 添加到 StructureMap 以防止其他 worldgen 与该区域相交。
structures.AddStructure(new Rectangle(point.X - (int)(20f * xScale), point.Y - 20, (int)(40f * xScale), 40), 4);
return true;
}
```
点击查看[附魔剑神社](https://gfycat.com/ForcefulImmediateAsiaticlesserfreshwaterclam)高清版本

# 有用的方法

## [Terraria.WorldGen] public static void TileRunner(int i, int j, double strength, int steps, int type, bool addTile = false, float speedX = 0f, float speedY = 0f, bool noYChange = false, bool overRide = true)
此方法从坐标（ Tile 坐标中的 x 和 y）开始放置指定 Tile （类型）的小斑点。这种方法通常用于放置矿石或其他非框架重要的 Tile ，如沙子、泥土或石头。类型的特殊值具有特殊效果。 -1 将删除 Tile 而不是放置 Tile ，-2 将执行相同的操作，但如果坐标低于熔岩线，则添加熔岩。形状和大小由强度和步长参数控制。强度指导 Tile 的斑点有多大，步骤指示该过程将重复多少次。例如，小力度小步长会产生小斑点，力度大小步长会产生大斑点，力度小大步长会导致 Tile 的路径又长又细。 speedX 和 speedY 驱动各个步骤的路径将采用的初始方向，但该方法也会随机调整方向。 noYChange 为 true 时似乎将土墙放置在地表水平的 Tile 后面，并且对垂直运动的变化也有一些影响。 addTile 当为 true 时，会在世界上放置额外的 Tile 。当 true 将现有 Tile 更改为指定 Tile 时，overRide。当世界上有多个瓦片且 overRide 参数为 true 时，使用 TileRunner 是不安全的，因为它会破坏它们。在多人游戏中使用也是不安全的，因为它不会“框定” Tile ，也不会同步 Tile 更改。使用此方法的最新 vanilla 步骤是“Gems”步骤，因此使用此方法且 overRide 参数为 true 的放置步骤的最新位置将紧接在“Gems”步骤之后。如果您有不应被生成的矿石和宝石覆盖的 Tile ，请将该 ModTile 的 TileID.Sets.CanBeClearedDuringGeneration[Type] 设置为 false。由于在调用 TileRunner 时世界上不应存在多块，因此您只需为不希望矿石渗入的地形块设置此选项。
![](https://camo.githubusercontent.com/ede3d5f04262bc1d3f26ec0354c7b70e05b5f968c7441730fd471e99a1210b77/68747470733a2f2f7468756d62732e6766796361742e636f6d2f576967676c794a61756e74794879646174696474617065776f726d2d736d616c6c2e676966)
[高清版本](https://camo.githubusercontent.com/ede3d5f04262bc1d3f26ec0354c7b70e05b5f968c7441730fd471e99a1210b77/68747470733a2f2f7468756d62732e6766796361742e636f6d2f576967676c794a61756e74794879646174696474617065776f726d2d736d616c6c2e676966)

## [Terraria.WorldGen] public static void OreRunner(int i, int j, double strength, int steps, ushort type)
类似于 TileRunner，但没有很多选项。 OreRunner 从坐标（Tile坐标中的 x 和 y）开始放置指定Tile（类型）的小斑点。 OreRunner 仅替换 TileID.Sets.CanBeClearedDuringOreRunner 或 Main.tileMoss 的活动 Tile ，使其适合在世界上存在帧重要 Tile 之后使用。如果您有一个在世界中产生额外矿石时应该容易被替换的 Tile ，请将该 ModTile 的 TileID.Sets.CanBeClearedDuringOreRunner 设置为 true。原版代码仅在生成困难模式矿石时使用此方法。此方法适合在游戏中和多人游戏中使用，因为它同时帧和同步 Tile 更改。

## [Terraria.WorldGen] public static int PlaceChest(int x, int y, ushort type = 21, bool notNearOtherChests = false, int style = 0)
该方法尝试在给定坐标处放置一个箱子。如果方法成功，提供的坐标将是生成的箱子的左下角。 type 是要放置的 Tile 类型，style 是要放置的样式类型。对于香草箱子，您可以在提取香草纹理后从 Tiles_21.png 图像的左侧从零开始计数，以找到您想要放置的样式。 notNearOtherChests 可以设置为 true 以防止在左侧或右侧 25 格和上下 8 格内存在另一个箱子时放置该箱子。此方法返回成功放置的箱子的箱子索引，如果放置失败则返回 -1。宝箱放置失败的原因有很多，例如如果现有的 Tile 挡住了空间，或者在预期位置正下方没有 2 个合适的实心 Tile 。有关使用此方法的方法，请参阅尝试直到成功。有关将物品放入箱子的信息，请参阅将物品放入箱子中。
![](https://camo.githubusercontent.com/fdc4965e337bedda9231e9287a5aa6950b60215e873bae1a00518b5a72e97278/68747470733a2f2f692e696d6775722e636f6d2f43794167564e702e706e67)

## [Terraria.WorldGen] public static bool AddBuriedChest(int i, int j, int contain = 0, bool notNearOtherChests = false, int Style = -1)
该方法尝试放置一个箱子并根据样式和深度填充典型的战利品。如果没有任何参数，将根据深度创建常规、金色或锁定的阴影箱。您可以为包含传递一个项目类型，箱子中的第一个项目将是该项目。与 PlaceChest 不同，生成的箱子将放置在给定坐标的右下角。此外，如果给定的 j 坐标不合适，AddBuriedChest 将从给定坐标向下搜索以找到它遇到的第一个实心 Tile 并尝试放置在那里。如果成功放置了箱子，则此方法返回 true，但请注意，箱子可能不完全位于您提供的坐标处。这是使用默认参数 WorldGen.AddBuriedChest(x, y); 运行该方法的示例。请注意箱子样式如何根据深度变化以及箱子如何放置在提供的坐标正下方的地板上（如果可能）：
![](https://camo.githubusercontent.com/4edc4bcb926282075242e5ba21e97e6bfe2dd120bfb635506e352ab086f41b8d/68747470733a2f2f7468756d62732e6766796361742e636f6d2f556e636f6d666f727461626c654c6967687468656172746564456173747275737369616e636f757273696e67686f756e64732d736d616c6c2e676966)
[高清版本](https://gfycat.com/wigglyjauntyhydatidtapeworm)
有关将物品放入箱子的信息，请参阅[将物品放入箱子]（将物品放入箱子）。

## [Terraria.WorldGen] public static bool InWorld(int x, int y, int fluff = 0)
在处理与加法或减法相结合的随机坐标时，您可能会在世界范围之外构建坐标。这会导致世界生成崩溃，因此在尝试在这些坐标处执行操作之前检查坐标是否合适非常重要。使用此方法检查给定坐标是否在世界范围内。绒毛参数进一步检查坐标距离边缘至少有多少 Tile ，这对于可能影响大片 Tile 的世界生成动作很有用。

## [Terraria.WorldGen] public static Point RandomWorldPoint(int top = 0, int right = 0, int bottom = 0, int left = 0)
一种在世界中寻找随机 Tile 坐标的更简化的方法。 Point point = WorldGen.RandomWorldPoint((int)Main.worldSurface, 50, 500, 50) 等价于
```csharp
int x = WorldGen.genRand.Next(50, Main.maxTilesX - 50);
int y = WorldGen.genRand.Next((int)Main.worldSurface, Main.maxTilesY - 500);
```

## [Terraria.WorldGen] public static void KillTile(int i, int j, bool fail = false, bool effectOnly = false, bool noItem = false)
KillTile 可用于破坏指定 i 和 j 坐标处的瓦片或多瓦片。 fail 可防止 Tile 被破坏，但仍会播放撞击声音。 effectOnly 防止 Tile 被破坏，但仍会产生击中灰尘并防止击中声音。 noItem 防止物品掉落。

## [Terraria.WorldGen] public static bool PlaceTile(int i, int j, int type, bool mute = false, bool forced = false, int plr = -1, int style = 0)
PlaceTile 是在遵守锚点注意事项的同时放置单个图块的主要方式。 i 和 j 是坐标。这些坐标与图块的原点有关，不一定是图块的左上角。阅读 Basic Tile 以熟悉 Anchors 和 Origins 的概念。静音指示是否应该发出声音，这仅适用于游戏内使用，因为声音在世界生成期间全部静音。即使其他图块已经在坐标处，也强制尝试放置图块，但这是不可靠的。 plr 除了影响浴缸之外什么都不做。 style 是指提供的 tile 类型的样式。样式在 Basic Tile 指南中进行了解释。

PlaceTile 返回一个表示放置成功的布尔值。不幸的是，它不起作用，不要使用它。调用 PlaceTile 后检查坐标是检查放置是否成功的好方法： if(Main.tile[x, y].type == TileID.Campfire)

PlaceTile 不会公开所有内容。例如，尝试放置具有特定样式的图块将被许多底层方法忽略。另一个问题是不可能放置左右放置方向面向右侧的图块。在这些情况下，您可能需要手动将每个图块放置在多图块中，或者改用 WorldGen.PlaceObject。 WorldGen.PlaceObject 需要更多输入。例如，使用 PlaceObject 放置 Coral 意味着您必须手动指定样式，因为随机样式选择是 PlaceTile 的一个功能。

TODO：解释如何在代码中放置 TileEntity，因为 PlaceTile 不会自动完成。

## StructureMap
在世界生成期间，游戏使用通过 Worldgen.structures 访问的 StructureMap 来跟踪重要的世界生成功能以防止重叠。 StructureMap 基本上是一个 Rectangles 集合，指示世界中被不应被干扰的世界生成特征所占据的区域。如果您正在生成一些重要的东西，您可能希望通过 Worldgen.structures.AddProtectedStructure 方法添加到 StructureMap 以告诉其他世界生成通道避开该区域。 StructureMap 是合作的，如果您要放置结构，最好在将结构放置在坐标处之前检查 Worldgen.structures.CanPlace 结构。 StructureMap 中的一些结构原版位置包括 Hives、Enchanted Sword Altars 和 Cabins。 StructureMap 不必用于所有结构，因为生物群系之间的交互很有趣。将 StructureMap 用于不应交互的结构，并在后期执行破坏性操作之前检查 StructureMap。
此图像显示了 StructureMap 中以绿色突出显示的条目。
![](https://camo.githubusercontent.com/9efe2d3dc7769c4c64ab9b20f0457e0f59248b2f6d57492842399f19d8efb5de/68747470733a2f2f692e696d6775722e636f6d2f31765a476b63732e706e67)

## TileID.Sets.GeneralPlacementTiles
当代码检查 Worldgen.structures.CanPlace 时，CanPlace 将另外在该区域中搜索 GeneralPlacementTiles 中为 false 的图块。在 GeneralPlacementTiles 中将 TileID 设置为 false 将阻止遵循 StructureMap 的结构尝试放置在其上。

## TileID.Sets.CanBeClearedDuringGeneration
此代码会影响 Cavinator 和 TileRunner 等地形方法。此数组中标记为 false 的图块将在这些操作中继续存在。

## IL Editing
原版世界生成通行证都是匿名方法，不幸的是，这意味着 IL 编辑要困难得多。我们必须手动使用 HookEndpointManager，更复杂的是方法名称是自动生成的。 TODO：他们经常改变吗？如何以编程方式找到它们，例如。
