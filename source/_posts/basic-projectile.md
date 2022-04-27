---
title: 弹丸基础
top: false
cover: false
toc: true
mathjax: true
date: 2022-04-27 9:29:23
password:
summary:
tags: [tModLoader, terraria, 翻译,c#]
categories:
---
[Basic Projectile](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile)

# 什么是弹丸
在开始修改射弹之前，您应该了解物品和射弹之间的区别。物品是可以存储在您的库存中的对象，而射弹是例如从武器或敌人射出的对象。

# 怎么使用弹丸
泰拉瑞亚中的许多物品由于射弹而具有功能，包括枪和弓（分别是子弹和箭）、激光、炸弹和其他投掷物品，以及大多数魔法武器。您可能认为不是射弹的其他一些物品包括：抓钩、连枷、长矛、宠物、召唤物、钻头和悠悠球。许多敌人也会产生射弹。

# 制作弹丸
要在泰拉瑞亚中创建弹丸，您必须首先创建一个“继承”自 ModProjectile 的类。为此，请在您的 mod 源目录 (My Games\Terraria\ModLoader\Mod Sources\MyModName) 中创建一个 .cs 文件，然后在文本编辑器中打开该文件。将以下内容粘贴到该文件中，将 NameHere 替换为您的项目的内部名称，并将 ModNamespaceHere 替换为您的 mod 的文件夹名称/命名空间。 （一个常见的错误是在内部名称中使用撇号或空格，不要这样做，计算机不会理解。）
```csharp
using Terraria;
using Terraria.ID;
using Terraria.ModLoader;

namespace ModNamespaceHere
{
	public class NameHere : ModProjectile
	{
		public override void SetStaticDefaults()
		{
			DisplayName.SetDefault("English Display Name Here");
		}

		public override void SetDefaults()
		{
			projectile.arrow = true;
			projectile.width = 10;
			projectile.height = 10;
			projectile.aiStyle = 1;
			projectile.friendly = true;
			projectile.ranged = true;
			aiType = ProjectileID.WoodenArrowFriendly;
		}

		// Additional hooks/methods here.
	}
}
```
现在您有了一个 .cs 文件，将您的纹理文件（您制作的一个 .png 图像文件）放入该 .cs 文件所在的文件夹中。确保阅读自动加载，这样您就知道如何满足计算机对其文件名和文件夹结构的期望。

# 我找不到我的投射物
请记住，物品和射弹是不同的。一个常见的错误是模组制作者会制造弹丸，但不明白他们需要使用该弹丸制造一些东西。例如，对于投掷刀武器，您需要同时制作物品和射弹。弹药物品也需要一个与之相关的独特射弹。您并不总是需要和项目和射弹，例如如果射弹是由 npc 生成的。测试弹丸的最简单方法是制作一个物品并将 item.shoot 设置为弹丸。例如，item.shoot = ModContent.ProjectileType<MyProjectile>()。参见 ExampleMod 以获取由 Items 生成的 Projectiles 的许多示例，它们位于不同的文件夹中，但很容易找到。

# SetDefaults
弹丸最重要的部分是 SetDefaults。 SetDefaults 是您为射弹设置值的地方，例如命中框的宽度和高度，射弹是友好还是敌对，以及射弹将使用哪个 AI。请参阅 [Projectile 类文档](https://github.com/tModLoader/tModLoader/wiki/Projectile-Class-Documentation)以查看 SetDefaults 中通常设置的值的含义。您还可以通过访问[Vanilla弹丸字段值](https://github.com/tModLoader/tModLoader/wiki/Vanilla-Projectile-Field-Values)来查看Vanilla弹丸值。可以在 [ExampleMod.Projectiles](https://github.com/tModLoader/tModLoader/tree/master/ExampleMod/Projectiles) 中找到许多不同射弹的示例

## projectile.damage
一个常见的错误是在 SetDefaults 中设置 projectile.damage，这不起作用，因为在生成射弹时，射弹的伤害值总是被传递给 Projectile.NewProjectile 的值覆盖。通常物品或生成该物品的NPC会影响伤害。

## drawOffsetX, drawOriginOffsetY, drawOffsetX
这些是 ModProjectile 字段，与正确将命中框居中到精灵相关。阅读[绘图和碰撞](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#drawing-and-collision)了解更多信息。

# 其他 Hooks/Methods
[ModProjectile 文档](http://tmodloader.github.io/tModLoader/html/class_terraria_1_1_mod_loader_1_1_mod_projectile.html)列出了您将要使用的许多其他 Hooks/Methods 来使您的射弹独一无二。例如，如果您想在弹丸击中时应用减益效果，您可以使用 OnHitNPC。要在弹丸击中 Tile 时执行某些操作，请使用 OnTileCollide。请参阅 ExampleMod 中的文档和用法以了解如何正确使用它们。

# 什么是 AI
射弹的 AI 是射弹最重要的方面，它控制射弹在生成后如何移动和动作。新模组最容易通过分配 projectile.aiStyle = #; 来首先依赖其他原版射弹中已经使用过的 AI 代码。和 aiType = ProjectileID.NameHere;。这被称为模仿Vanilla弹丸。当你渴望更高级的运动时，你会意识到模仿Vanilla射弹 AI 是非常有限的。我们将在下面讨论模仿和自定义 AI。

# 使用 Vanilla AI
我们可以使用Vanilla AI 来制作我们的射弹原型。让我们做一个回旋镖。使用与像回旋镖一样移动的Vanilla弹丸相同的aiStyle，我们可以制作回旋镖。您可以在 Vanilla Projectile Field Values 中查找回旋镖弹丸，您会发现回旋镖都使用 3 的 aiStyle：
![](https://camo.githubusercontent.com/507c1c4b98cf977713066cbb25ed56bc96e857941f2615698fdb05e83132503c/68747470733a2f2f692e696d6775722e636f6d2f525361785636542e706e67)

我们现在可以使用 projectile.aiStyle = 3;在我们的代码中。为了使这个回旋镖更容易，我们可以使用 projectile.CloneDefaults(ProjectileID.EnchantedBoomerang)，它也将复制所有其他默认值。这样做，你会得到一个几乎和原版射弹一样的射弹：
![](https://camo.githubusercontent.com/5f1a9878e94ecadf7955bdfdb4538b982150b3c5ba4ca828d811dc32ac297d4f/68747470733a2f2f692e696d6775722e636f6d2f434c324d7761462e706e67)

这是生成的代码。
```csharp
public override void SetDefaults()
{
	projectile.CloneDefaults(ProjectileID.EnchantedBoomerang);
	// projectile.aiStyle = 3; This line is not needed since CloneDefaults sets it.
	aiType = ProjectileID.EnchantedBoomerang;
}
```
那个灰尘很酷，但是如果你想改变那个灰尘或其他任何小东西的颜色，你不能依赖 aiStyle 和 aiType。要进行更改，您需要查阅 [Vanilla Code Adaption 指南](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption)以调整现有代码或继续阅读以了解如何从头开始编写 AI 代码。请记住，使用 projectile.aiStyle 和 aiType 是一种原型设计工具，任何在 mod 中远程有趣的东西都可能需要编写自己的 AI 代码或改编 vanilla 代码。

# 自定义 AI
本节将讨论可以合并到 AI 中的元素。如果您使用 projectile.CloneDefaults 复制其他射弹默认值，请记住将 projectile.aiStyle 设置回 0。自定义 AI 的所有代码都进入 ModProjectile.AI 方法。

## Timers
许多射弹使用计时器来延迟动作。通常我们使用 projectile.ai[0] 或 projectile.ai[1] 因为这些值会自动同步，但我们也可以使用类字段。在这里，我们数到 30，或者换句话说，半秒。
```csharp
projectile.ai[0] += 1f;
if (projectile.ai[0] >= 30f)
{
	// 半秒过去了。重置定时器
	projectile.ai[0] = 0f;
	projectile.netUpdate = true;
	// 在这里做点什么，也许换一个新的状态。
}
```

## 重力
抛射物实际上并不存在重力，每个随着重力移动的抛射物实际上只是在其 AI 中具有代码。要实现重力，只需向 projectile.velocity.Y 添加一个小值：
```csharp
projectile.velocity.Y = projectile.velocity.Y + 0.1f; // 箭重力0.1f，刀重力0.4f
if (projectile.velocity.Y > 16f) //这个检查实现了“终端速度”。我们不希望弹丸越来越快。超过 16f 这个射弹会穿过方块，所以这个检查很有用。
{
	projectile.velocity.Y = 16f;
}
```

### 延迟重力
箭和飞刀射弹在受到重力影响之前都会等待几帧：
```csharp
projectile.ai[0] += 1f; // 在应用重力之前使用计时器等待 15 个滴答声。
if (projectile.ai[0] >= 15f)
{
	projectile.ai[0] = 15f;
	projectile.velocity.Y = projectile.velocity.Y + 0.1f;
}
if (projectile.velocity.Y > 16f)
{
	projectile.velocity.Y = 16f;
}
```

## 抗风能力
通过减少 projectile.velocity.X 的多重性，我们可以轻松实现风阻。结合计时器有条件地产生这种效果。
```csharp
projectile.velocity.X = projectile.velocity.X * 0.97f; // 0.99f 用于滚动手榴弹减速。尝试 0.9f 和 0.99f 之间的值
```

## Rotation
### 恒定旋转
我们可以增加 AI 中的 projectile.rotation 使其像回旋镖一样旋转。
```csharp
projectile.rotation += 0.4f * (float)projectile.direction;
```

### 面向前方
沿行进方向旋转通常用于箭头等射弹。如果您的弹丸朝向正确，则无需添加 MathHelper.PiOver2（可在 Microsoft.Xna.Framework 中找到）。如果您的弹丸指向上方，则需要这样做。
```csharp
projectile.rotation = projectile.velocity.ToRotation() + MathHelper.PiOver2; // projectile sprite faces up
// or
projectile.rotation = projectile.velocity.ToRotation(); // projectile faces sprite right
```

### spriteDirection
如果你的精灵在向左射击时是倒置的，你需要设置这个：projectile.spriteDirection = projectile.direction;有关说明和示例，请参阅[绘图和碰撞](https://github.com/tModLoader/tModLoader/wiki/Basic-Projectile#drawing-and-collision)。

## Dust
在 AI 中生成灰尘以获得视觉效果。随机放置、灰尘和频率在视觉上令人愉悦。这是 Enchanted Boomerang 尘土生成（aiStyle 3，aiType ProjectileID.EnchantedBoomerang）：
```csharp
if (Main.rand.Next(5) == 0) // only spawn 20% of the time
{
	int choice = Main.rand.Next(3); // choose a random number: 0, 1, or 2
	if (choice == 0) // use that number to select dustID: 15, 57, or 58
	{
		choice = 15;
	}
	else if (choice == 1)
	{
		choice = 57;
	}
	else
	{
		choice = 58;
	}
	// Spawn the dust
	Dust.NewDust(projectile.position, projectile.width, projectile.height, choice, projectile.velocity.X * 0.25f, projectile.velocity.Y * 0.25f, 150, default(Color), 0.7f);
}
```

### Dust Trail
每次 AI 更新都会产生 1 个灰尘来完成一条灰尘轨迹。

## Lighting
模组制作者对照明有许多不同的定义。如果要添加粒子，请参阅“灰尘”部分。如果您希望射弹纹理不受光照影响，请参阅 ModProjectile.GetAlpha。如果你想让弹丸发出白光，你可以设置 projectile.light = 1f; （或 0 到 1 之间的任何数字）在 SetDefaults 中。最后，如果你想发出彩色光而不是产生的灰尘，照亮附近 Tile 的光，在你的 AI 方法中使用 Lighting.AddLight：
```csharp
Lighting.AddLight(projectile.Center, 0.9f, 0.1f, 0.3f); // R G B 值从 0 到 1f。这是猩红之心宠物的红色
```

## Sound
### 重复声音
soundDelay 字段将自动减少每一帧。检查它是否为 0，然后将其设置为一个值并播放声音将导致重复声音。此示例来自回旋镖 aiStyle (3)。
```csharp
if (projectile.soundDelay == 0)
{
	projectile.soundDelay = 8;
	Main.PlaySound(SoundID.Item7, projectile.position);
}
```

## Splitting/Spawning Projectiles
Crystal Bullet 和腐化者天灾 (EatersBite) 在死亡时都会生成新的投射物。我们通常会在 Kill 或 OnTileCollide 中看到生成的射弹，但我们也可以在 AI 中做到这一点。生成射弹时，我们需要注意多人游戏兼容性，并确保仅在 Main.myPlayer == projectile.owner 为 true 时生成射弹以防止出现问题。缩小射弹伤害是典型的。请参阅 Projectile.NewProjectile 以了解多人游戏的参数和用法。
```csharp
// 此代码生成 3 个与射弹相反方向的射弹，速度随机变化。
if (OptionallySomeCondition && projectile.owner == Main.myPlayer)
{
	for (int i = 0; i < 3; i++)
	{
		// 计算其他射弹的新速度。
		// 以 40% 到 70% 的速度反弹，加上 -8 到 8 之间的随机值
		float speedX = -projectile.velocity.X * Main.rand.NextFloat(.4f, .7f) + Main.rand.NextFloat(-8f, 8f);
		float speedY = -projectile.velocity.Y * Main.rand.Next(40, 70) * 0.01f + Main.rand.Next(-20, 21) * 0.4f; // This is Vanilla code, a little more obscure.
		// 生成射弹。
		Projectile.NewProjectile(projectile.position.X + speedX, projectile.position.Y + speedY, speedX, speedY, 90, (int)(projectile.damage * 0.5), 0f, projectile.owner, 0f, 0f);
	}
}
```

## Homing
// TODO 简而言之：您可以循环 Main.npc，并选择一个有效的目标。然后，对于您的射弹所具有的目标，您调整射弹的速度，使其向目标移动。

## Follow Mouse
// TODO 如果你想让弹丸正好在光标上，只需在 AI 中将 projectile.position 设置为 Main.MouseWorld： projectile.position = Main.MouseWorld

## Held Projectile
// TODO

## Fade In/Out
许多子弹会逐渐消失，因此当它们产生时它们不会与它们出现的枪口重叠。您可以使用 projectile.alpha = 255 将弹丸设置为透明生成；在 SetDefaults 中。
```csharp
if (projectile.alpha > 0)
{
	projectile.alpha -= 15; // Decrease alpha, increasing visibility.
}
```
ExampleAnimatedPierce 显示同时使用淡入和淡出。

## Animation/Multiple Frames
弹丸动画，切换要绘制精灵的哪一帧，发生在 AI 中。确保设置 Main.projFrames[projectile.type] = #;首先在 SetStaticDefaults 中。您可以将 projectile.frame 设置为您想要绘制的任何帧。

### Looping/Cycling
您可以使用 projectile.frameCounter 和 Main.projFrames[projectile.type] 来实现循环动画。示例：ExampleAnimatedPierce
```csharp
// 循环遍历 4 个动画帧，每个帧花费 5 个刻度。
if (++projectile.frameCounter >= 5)
{
	projectile.frameCounter = 0;
	if (++projectile.frame >= Main.projFrames[projectile.type])
	{
		projectile.frame = 0;
	}
}
// Or, more compactly:
if (++projectile.frameCounter >= 5)
{
	projectile.frameCounter = 0;
	projectile.frame = ++projectile.frame % Main.projFrames[projectile.type];
}
```

## 例子
### AiStyle 1
弹丸AiStyle 1，用于游戏中的许多简单弹丸，长度超过3000行。如果您尝试使用 [Advanced Vanilla Code Adaption](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption) 指南调整此 AI，您可能会感到沮丧。这是没有所有 ProjectileID 特定代码的 AiStyle 的简要概述：
```csharp
// 可选：如果弹丸应该淡入，淡入：
	if (projectile.alpha > 0)
		projectile.alpha -= 15;
	if (projectile.alpha < 0)
		projectile.alpha = 0;
// 设置旋转面向当前轨迹：
projectile.rotation = (float)Math.Atan2((double)projectile.velocity.Y, (double)projectile.velocity.X) + 1.57f;
// 或者，这个版本更容易阅读：
projectile.rotation = projectile.velocity.ToRotation() + MathHelper.PiOver2;
// 限制向下的速度，以防你向这个射弹添加重力
if (projectile.velocity.Y > 16f)
	projectile.velocity.Y = 16f;
```
如您所见，没有所有 ProjectileID 特定代码的 1 的 Projectile AiStyle 只有几行代码，并且与上面的淡入和旋转示例相匹配。

# Bounce and OnTileCollide
许多射弹在与实心 Tile 碰撞时会反弹。这种行为在技术上不是 AI 的一部分，因为它发生在称为 OnTileCollide 的方法中。默认情况下，当弹丸与 Tile 碰撞时，速度会迅速降低，这样弹丸就会停下来，弹丸就会被杀死。通过覆盖 ModProjectile.OnTileCollide 并返回 false，我们可以避免该逻辑并实现我们自己的逻辑。如果我们返回 true，我们可以在保留原版逻辑的同时添加额外的逻辑。最常见的用途是让你的弹丸反弹。一些弹丸通过失去一些速度而真实地弹跳，而另一些弹丸则不切实际地弹跳并在新的方向上保持其原始速度。一些射弹的反弹有限，这通常是通过利用 projectile.penetrate 来完成的。当覆盖 ModProjectile.OnTileCollide 时，杀死射弹、产生 Tile 碰撞灰尘和播放碰撞声音都是可能需要实现的东西。

## OnTileCollide 示例
[ExampleBullet.cs](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/Projectiles/ExampleBullet.cs#L34) 展示了有限的反弹、Tile 碰撞灰尘、Tile 碰撞声音和弹跳，同时完全保持速度。

[ExampleCloneProjectile.cs](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/Projectiles/ExampleCloneProjectile.cs#L24) 展示了返回 true 以保留原始碰撞逻辑，同时还产生少量次要射弹。

[SparklingBall.cs](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/Projectiles/SparklingBall.cs#L27) 与 [ExampleBullet.cs](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/Projectiles/ExampleBullet.cs#L34) 类似，不同之处在于速度按 0.75f 缩放，从而在每次反弹时减慢弹丸的速度。

[ExampleFlailProjectile.cs](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/Projectiles/ExampleFlailProjectile.cs#L134) 也类似，只是速度降低到原来速度的五分之一，让武器感觉很重。此代码还显示了使用弹丸的速度来影响声音和灰尘的产生。这允许连枷仅在速度足够快的情况下才会发出碰撞声音，而如果连枷只是缓慢滚动则静音。 ExampleFlailProjectile 也跳过使用 projectile.projectile 操作，因为它不会像 SparklingBall 和 ExampleBullet 那样在弹跳一定次数后杀死自己。

通过上述示例，您可以制作所需的 Tile 碰撞行为。如果您尝试克隆原版射弹行为，请在 Projectile.HandleMovement 中搜索 ProjectileID 编号或射弹 aiStyle 编号以查找相关代码。改编指南中的 [Shadowbeam Staff Clone](https://github.com/tModLoader/tModLoader/wiki/Advanced-Vanilla-Code-Adaption#example-item-and-projectile-shadowbeam-staff-clone) 示例显示了查找 AI 代码未涵盖的普通代码片段所需的这一思考过程和其他思考过程。

# Drawing and Collision
你可能会发现自己注意到你的射弹在不应该撞到墙壁的时候会撞到墙壁，或者有一个奇怪的碰撞箱。首先，值得重申的是 projectile.width 和 projectile.height 对应于射弹的碰撞箱，而不是使用的精灵。您几乎从不希望宽度或高度不同，它应该是方形的。你也永远不想使用 projectile.scale 因为原版绘图代码并没有真正正确地考虑到它。 sprite 的绘制试图用 sprite 覆盖 hitbox，这个 sprite 的绘制受到 Main.DrawProj 方法中完成的各种数学位的影响。

## Vertical Sprite Example
让我们通过这个例子来探索碰撞和绘图问题并努力解决它们。这是精灵，它是 48x70 像素：
![](https://camo.githubusercontent.com/e90932fdde6de995464aeb36a3d1fd699eb606133f60cad535a0af811887935f/68747470733a2f2f692e696d6775722e636f6d2f79344f634a41762e706e67)
这个 ModProjectile 的重要部分如下：
```csharp
// SetDefatults
projectile.width = 8;
projectile.height = 8;
// AI
projectile.rotation = projectile.velocity.ToRotation() + MathHelper.ToRadians(90f);
```
我们的目标是让这个射弹的黄色部分成为碰撞箱。黄色区域是 8 x 8 像素，所以我们已经将宽度和高度设置为 8。那里的 projectile.rotation 代码将旋转设置为速度，同时添加 90 度旋转，因为我们使用的精灵碰巧面朝上，而不是游戏预期的向右。在本指南中，我们将使用 [Modders Toolkit](https://forums.terraria.org/index.php?threads/modders-toolkit-a-mod-for-modders-doing-modding.55738/) mod 来可视化命中框。这非常有用。

在这里，我们看到碰撞框，黄色方块，与我们的精灵的尖端不匹配：[高清视频](https://gfycat.com/SimpleMinorImperialeagle)
![](https://camo.githubusercontent.com/67da99f7836624d24205426df162fa44f2060da111a54a3fd9cae5f5c8ecb4a9/68747470733a2f2f7468756d62732e6766796361742e636f6d2f53696d706c654d696e6f72496d70657269616c6561676c652d736d616c6c2e676966)

vanilla 代码所做的数学运算有点令人困惑，但基本上我们需要将 drawOffsetX 和 drawOriginOffsetY 设置为偏移我们的精灵绘制的值，以尝试将精灵正确放置在命中框上。如果您尝试这样做，请使用 Modders Toolkit 更改游戏中的偏移值或使用编辑并继续调整游戏中的值。另一种方法是仅在图形程序中对精灵本身进行测量：
![](https://camo.githubusercontent.com/1e36d8d6b9576f9c7815842c5271dcd6fa3f32e061a5031b1935c718d254c1ea/68747470733a2f2f692e696d6775722e636f6d2f6d3544786b426d2e706e67)

在这里，我们看到使用 Modders Toolkit 测试各种值。确保在您的 SetDefaults 代码中复制这些值：[高清视频](https://camo.githubusercontent.com/d3b84c4db81b1fe4945faff5e15403273a915acb2894929779176e01dbe851d1/68747470733a2f2f7468756d62732e6766796361742e636f6d2f4d696e7479436861726d696e67436f70706572686561642d736d616c6c2e676966)
![](https://camo.githubusercontent.com/d3b84c4db81b1fe4945faff5e15403273a915acb2894929779176e01dbe851d1/68747470733a2f2f7468756d62732e6766796361742e636f6d2f4d696e7479436861726d696e67436f70706572686561642d736d616c6c2e676966)

经过一些实验或测量，我们知道添加 drawOffsetX = -20;对此 ModProjectile.SetDefaults 将修复绘图相对于命中框的位置。

现在让我们尝试将 hitbox 定位在精灵的蓝色部分上。这一次，让我们使用[编辑并继续](https://github.com/tModLoader/tModLoader/wiki/Why-Use-an-IDE#edit-and-continue)来完成此操作。在下面的剪辑中，您可以看到我们测试新值的速度有多快： [高质量视频](https://gfycat.com/WebbedUntimelyHarborseal)
![](https://camo.githubusercontent.com/ec354146b5e2cf2ae6e9f958c225a8976f4ebbd043850acbd366212f0696a5a2/68747470733a2f2f7468756d62732e6766796361742e636f6d2f576562626564556e74696d656c79486172626f727365616c2d736d616c6c2e676966)
如您所见，我们添加了 drawOriginOffsetY = -16;将碰撞箱定位在精灵的下方。

### Fixing upside-down sprite problem
您可能已经注意到，当向左侧发射时，精灵是颠倒的。请记住，在我们的 AI 中，我们有这行代码：projectile.rotation = projectile.velocity.ToRotation() + MathHelper.ToRadians(90f);。如果我们将精灵向左旋转，那么它是颠倒的。我们可以用 spriteDirection 解决这个问题。 spriteDirection 将水平翻转精灵的绘图。要实现这一点，只需添加 projectile.spriteDirection = projectile.direction;到 projectile.rotation = 行之后的 AI 代码。
未修复:
![](https://camo.githubusercontent.com/1890e343532b2167208182fb792490093144707c32ea5ceb204ac3eb8a981e50/68747470733a2f2f692e696d6775722e636f6d2f734b557139347a2e706e67)

已修复:
![](https://camo.githubusercontent.com/50ae94fce4701dd258883d5046ed5c7f0466e59a9e5b1e3e8171954b501878a9/68747470733a2f2f692e696d6775722e636f6d2f7733414c6844582e706e67)

## Horizontal Sprite Example
如果你的精灵是水平方向的，情况会发生一些变化。这是我们新的水平精灵，它现在是 70x48 并且水平定向，指向右侧而不是像以前那样指向上方：
![](https://camo.githubusercontent.com/4e315f1f5909320efcaa4aa7ce7aa774ec16f92106227483e24e0e175e6cdad7/68747470733a2f2f692e696d6775722e636f6d2f65747a627a73302e706e67)
再一次，我们可以看到 hitbox 没有对齐：
![](https://camo.githubusercontent.com/9cb069d29281f657fbc1aecdb7275b0d8bffd1eb4d0408bb3d9aa2fcf543ef54/68747470733a2f2f7468756d62732e6766796361742e636f6d2f436f6e6675736564536172646f6e6963436f77626972642d736d616c6c2e676966)

与水平示例不同，这次我们设置 projectile.rotation = projectile.velocity.ToRotation();直接而不是增加额外的 90 度。经过一些实验，我们得出了以下提示的碰撞框：
```csharp
drawOffsetX = -62;
drawOriginOffsetY = -20; 
drawOriginOffsetX = 31;
```
这些值有点奇怪，因为泰拉瑞亚正在做一些数学运算，所以这里是计算它们的算法：
```csharp
drawOffsetX = Negative X pixel position of the top left corner of the intended hitbox
drawOriginOffsetY = Negative Y pixel position of the top left corner of the intended hitbox
drawOriginOffsetX = X pixel position of center of hitbox minus Texture Width divided by 2 
```
这是一个图表：
![](https://camo.githubusercontent.com/d9baf48d02d866637c9516dfad0839b02f8699af166332b121d70dc691c2a887/68747470733a2f2f692e696d6775722e636f6d2f7a516678584d332e706e67)
如果您不喜欢与原版弹丸渲染代码作斗争，您可以随时自己绘制弹丸，如 [ExampleAnimatedPierce Projectile](https://github.com/tModLoader/tModLoader/blob/master/ExampleMod/Projectiles/ExampleAnimatedPierce.cs#L114) 中所示

### Fixing upside-down sprite problem again
对于垂直精灵，使用 projectile.spriteDirection 是有效的，因为它控制了射弹精灵的水平翻转。使用水平精灵，水平翻转使精灵面朝后移动：
![](https://camo.githubusercontent.com/545bd7f90d30f567a653f1e1b162b5d86e852c87a14e319a97f88af7e03a37ba/68747470733a2f2f692e696d6775722e636f6d2f76664b72527a5a2e706e67)
为了解决这个问题，我们需要动态调整偏移量，并有条件地将 180 度或 Pi 添加到旋转中。这是代码：
```csharp
// 将方向和 spriteDirection 都设置为 1 或 -1（分别为右和左）
// projectile.direction 在 Projectile.Update 中自动设置正确，但我们需要在这里设置它，否则纹理将在第一帧绘制不正确。
projectile.spriteDirection = projectile.direction = (projectile.velocity.X > 0).ToDirectionInt();
// 如果朝左，则将 Pi 添加到旋转中以更正绘图
projectile.rotation = projectile.velocity.ToRotation() + (projectile.spriteDirection == 1 ? 0f : MathHelper.Pi);
if (projectile.spriteDirection == 1) // facing right
{
	drawOffsetX = -62; // 这些值与 SetDefaults 中的值匹配
	drawOriginOffsetY = -20;
	drawOriginOffsetX = 31;
}
else
{
	// Facing left.
	// 如果您在绘图程序中翻转精灵，您可以计算出这些值。
	drawOffsetX = 0; // 0 因为现在hitbox的左上角在最左边的像素上。
	drawOriginOffsetY = -20; // doesn't change
	drawOriginOffsetX = -31; // 数学计算出这是另一个值的负数。
}
```
![](https://camo.githubusercontent.com/564dc03d1f5c3a2e8686769b151af5cbf5a918e6532ce51d791b88a35617bd93/68747470733a2f2f692e696d6775722e636f6d2f464b66687451302e706e67)
希望这些答案可以帮助您解决弹丸碰撞箱和绘图问题。
