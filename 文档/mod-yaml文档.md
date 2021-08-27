## mod.yaml Entries

| Entry                         | Purpose |
|-------------------------------|---------|
| [Metadata](#metadata)         | 定义基本属性，如mod的名称、版本和作者。
| [RequiresMods](#requiresmods) | 列出运行此mod所需的mod的名称和版本(如果有的话)。
| [Assemblies](#assemblies)     | 它列出了mod使用的带有游戏代码的.dll。
| ContentInstaller              | 用于从CD或互联网上安装游戏数据。
| [Packages](#packages)         | 列出包含mod数据和游戏文件的路径和存档。还可以设置方便的快捷方式。
| MapFolders           | 可以找到地图的目录列表。
| SupportMapsFrom      | 地图与此mod兼容的mod列表。不会自动从这些mod加载地图。
| Missions             | 列出一个或多个yaml文件，这些文件依次列出应该显示在单人游戏任务窗口中的所有地图。
| MapGrid              | 定义地图坐标系统的布局和游戏使用的单元格。
| TileSets             | 列出一个或多个yaml文件，这些文件定义了地图可用的地块类型集，如雪、温带、内陆等。
| Rules                | 列出一个或多个yaml文件，这些文件定义了组成游戏中的单位、建筑、装饰等的角色。
| Sequences            | 列出一个或多个为所有角色设置素材的yaml文件。
| Weapons              | 列出一个或多个定义游戏中使用的武器和弹头的yaml文件。
| Chrome               | 列出从spritesheet创建可用于UI的图形的一个或多个yaml文件。
| ChromeLayout         | 列出一个或多个定义UI布局的yaml文件。
| ChromeMetrics        | 列出一个或多个为UI定义默认颜色、字体、文本大小等的yaml文件。
| Fonts                | 列出一个或多个yaml文件，这些文件定义了UI可以使用的所有可用字体。
| Cursors              | 列出一个或多个定义所有可用光标的yaml文件。
| LoadScreen           | 设置用于在菜单和游戏内切换的加载屏幕。
| SoundFormats         | 列出所有需要的声音格式。
| Voices               | 列出一个或多个yaml文件，这些文件定义用于单元声音的所有声音。
| Notifications        | 列出一个或多个定义由UI生成的所有声音的yaml文件。
| Music                | 列出一个或多个定义可用音乐轨的yaml文件。
| Translations         | 列出一个或多个定义可用语言的yaml文件。
| SpriteFormats        | 列出所有需要的sprite格式。
| SpriteSequenceFormat | 可用于为图形文件定义额外的每个分块文件扩展名。
| ServerTraits         | 列出所有应该只在服务器上运行的特征。
| GameSpeeds           | 定义在大厅中可供选择的不同的游戏速度。
| ColorValidator       | 用于多人游戏大厅的颜色验证。

### Metadata

`Metadata`定义了在mod选择器中显示的一些基本属性。

| 属性    | 数据类型 | 用途 |
|-------------|--------|---------|
| Title       | string | mod名字 |
| Version     | string | mod版本 |
| Description | string | mod的描述。该文本将在mod选择器中显示在预览图像旁边。 |
| Author      | string | mod作者(们) |
| Hidden      | bool   | mod是否应该显示在mod选择器中。 |

### RequiresMods

`RequiresMods`列出了这个mod需要安装才能运行的所有其他mod。每个列表条目都是一个**键-值对**，键是mod的**id**(例如“ra”)，值是所需的版本。

- 如果您不需要任何mod，在这里提到modchooser仍然是一个很好的实践，它的版本设置为您的mod使用的任何OpenRA版本。

例如:

```yaml
RequiresMods:
	ra: playtest-20160424
	modchooser: playtest-20160424
```

### Assemblies


`Assemblies`模块列出了你的mod需要工作的所有游戏逻辑.dll，每行一个。您很可能至少需要使用`OpenRA.Mods.Common.dll`。因为它包含了大量的游戏逻辑。

例如:

```yaml
Assemblies:
	common|OpenRA.Mods.Common.dll
	ts|OpenRA.Mods.TS.dll
```

<br/>

### Packages

`Packages`部分列出了所有包含mod游戏数据文件的位置(存档和目录)。每当在mod的yaml文件中引用了一个文件名，这些位置将被用作“搜索路径”。它还可以设置方便的快捷方式，减少路径引用的冗长。

每个条目都是一行。它可以包含单个路径规范，也可以包含键-值对，其中键是路径规范，而值是一个快捷方式(称为“显式挂载”或“显式包”)，从那时起可以用来引用该路径。

**路径规范可以有多种形式，并且有一些具有特殊含义的字符。现在将对这些进行解释。**

- 路径规范最简单的形式就是一个文件名。该文件将在所有其他已经声明的包中搜索，因此不需要使用完整路径。在文件名前加上 `~` 使该文件**可选**。如果一个必须的文件没找到，mod加载时游戏将崩溃。

- 有时需要引用OpenRA安装的特定位置。一个`.`。指向mod的安装目录(其中有`mod.yaml`)。以`./`开头的路径表示游戏的安装目录。而 `^` 表示用户的支持目录 **(我的文档)** 的简写。

- 不同的mod也可以用 `$` 符号来引用。将这样引用的mod添加到[`RequiredMods`](#requiredmods)中可能是有意义的.

- 对于mod包和包含与其他包相同文件名的位置，设置显式挂载非常有用。可以将它们用作一种简写符号，以减少路径引用的冗长，并且还会导致文件名查找更喜欢显式的包而不是其他包。只有当在显式的包中找不到文件时，才会参考其他文件。


<br/>

特殊符号表:

| 符号 | 意义 |
|-----------|---------|
| .         | 指向mod的安装目录(其中有`mod.yaml`) |
| ./        | 以`./`开头的路径表示游戏的安装目录。|
| ~         | 在文件名前加上`~`使该文件**可选**。|
| ^         | 指向用户支持目录（不同平台下进行游戏） |
| $         | 指示对mod包的引用(例如`$ra`的意思是**安装了ra mod的任何地方**) |
| \|        | 用于指示对显式包的引用(例如。“ra \ | missions.yaml”) |

<br/>

一个虚构的XY mod的例子::

```yaml
Packages:
	.
	~^Contents/xy
	./mods/common: common
	$ra: ra
	$xy: xy
	MAIN.MIX
	~VIDEOS.MIX
	ra|bits
	xy|bits
```