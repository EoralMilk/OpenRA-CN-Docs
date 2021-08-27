# 添加VXL单位


**本教程提到的代码文件：**

- [单位的`yaml`脚本: ](#%e5%8d%95%e4%bd%8dyaml%e8%84%9a%e6%9c%ac%e6%96%87%e4%bb%b6%e7%9a%84%e4%bf%ae%e6%94%b9)
    本教程提到的阿帕奇属于空军，所以在 `rules/aircraft.yaml`中执行修改

- [单位的`Sequence yaml`脚本: ](#sequences%e6%96%87%e4%bb%b6%e7%9a%84%e7%bc%96%e8%be%91) 
    本文在`sequences/aircraft.yaml`中修改

- `voxels.yaml`：  
    在[`sequences/voxels.yaml`](#sequences%e5%ad%97%e6%ae%b5%e7%9a%84voxelsyaml)中修改
  
- `palettes.yaml`
  
- [mod.yaml](#modyaml%e7%9a%84%e4%bf%ae%e6%94%b9)

## mod.yaml的修改：

**以上提到的所有yaml文件都要在`mod.yaml`中注册，否则会导致游戏无法运行**

### sequences字段的voxels.yaml:

- 在`ModelSequences`下面增加一个:  
	mods/yourmod/sequences/`voxels.yaml`

- `ModelSequenceFormat`脚本内增加一个：  
    ModelSequenceFormat: VoxelModelSequence


## VXL单位的构成：

**我们以阿帕奇为例，接触过RA2 mod制作的玩家可能会知道这些文件：**

- `apache.vxl `//单位VXL模型
- `apache.hva `//单位HVA文件，指定了单位各部件相对位置和大小
- `apchicon.shp` //单位图标SHP
- `unittem.pal` //单位调色盘
- `cameo.pal `//图标调色盘

这些文件需要被置入`bits`文件夹内才能被游戏读取

## 单位YAML脚本文件的修改：

```yaml
APACHE: //单位定义名，类似于RA2 mod中注册单位
	Inherits: ^Helicopter //属性：直升机
	-SpawnActorOnDeath: //单位死亡后生成的单位
	-WithShadow: //SHP代码，本教程不涉及
	-WithFacingSpriteBody: //SHP代码，本教程不涉及
	Valued: //价格
		Cost: 2000 
	Tooltip:
		Name: Apache Longbow //单位名称
	Buildable:
		Queue: Aircraft
		BuildAtProductionType: Helicopter
		BuildPaletteOrder: 20
		IconPalette: tscameo //图标使用的调色盘，代码中不填写文件后缀
		Prerequisites: hpad, atek
		Description: Helicopter gunship armed\nwith dual chainguns.\n  Strong vs Infantry, Light armor\n  Weak vs Tanks, Aircraft //单位介绍
		Owner: allies //拥有者
	Selectable: //是否可选
		Bounds: 30,24
	Aircraft:
		RearmBuildings: hpad //在哪个建筑物里进行弹药再装填
		LandWhenIdle: false //是否会在不接受命令的时候降落
		InitialFacing: 224  
		TurnSpeed: 4 //转弯速度
		Speed: 112 //速度
	Health: //生命值
		HP: 200
	SelfHealing: //
自我治疗
        Step: 50
 //治疗步骤数量
        Delay: 3 //延迟
        HealIfBelow: 50 //当生命值低于多少时开始自我治疗
	Armor: //装甲类型
		Type: Light
	RevealsShroud: //揭露战场迷雾的距离
		Range: 12
	Armament@PRIMARY:  //主武器
		Weapon: HellfireAA
		LocalOffset: 0,-213,-85 //开火点，
	Armament@SECONDARY: //副武器
		Weapon: HellfireAG
		LocalOffset: 0,213,-85
	AttackHeli: 
		FacingTolerance: 20
	AmmoPool: //弹药数量
		Ammo: 24
		PipCount: 6
		ReloadDelay: 8 //装填时间
		AmmoCondition: ammo
	AutoTarget: 
	WithIdleOverlay@ROTORAIR:
		Sequence: rotor
		Offset: 85,0,384
	BodyOrientation:
		QuantizedFacings: 0
	RenderSprites:  //SHP代码，本教程不涉及
	RenderVoxels: //VXL定义代码
		PlayerPalette: tibsun //玩家调色盘
		Scale: 8  //颜色范围
		LightAmbientColor: 0.2, 0.2, 0.2 //环境光色
		LightDiffuseColor: 0.6, 0.6, 0.6 //漫反射光色
	WithVoxelBody:
```

## Sequences文件的编辑:

在Aircraft.yaml文件中加入如下代码：

```yaml
apache: //单位注册
	icon: apchicon //单位图标
		Start: 0 //图标不需要动画，所以Start和Length都是0
	rotor: lrotor
		Start: 0 //动画开始点
		Length: 4 //持续长度
	slow-rotor: lrotor
		Start: 4
		Length: 8
```

## Voxels.yaml的修改:

按照本文的单位属性（空军单位），代码无需额外注册炮塔和炮管，所以只须一个空的`idle`即可

```yaml
apache:
	idle:
但本文还是提供了车辆单位的设定方法：
4tnk: 
	idle: //单位注册
	turret: 4tnktur //炮塔组件注册
	barrel: 4tnkbarl //炮管组件注册

palettes.yaml修改注册调色盘：
（本文定义unittem.pal注册名为“tibsun”）

PaletteFromFile@tibsun: //调色盘注册
	Name: tibsun //调色盘名称
	Filename: unittem.pal //调色盘指向文件文件名，必须写文件后缀名
	ShadowIndex: 4 //阴影值
	AllowModifiers: false //允许修改
VoxelNormalsPalette@normals:
	Name: normals 
	Type: TiberianSun //属性
PaletteFromFile@tscameo:
	Name: tscameo
	Filename: cameo.pal
	AllowModifiers: false
PlayerColorPalette@tibsun:
	BasePalette: tibsun //基础调色盘，可以直接引用上面的调色盘
	BaseName: tibsun //基础调色盘名称，可以直接引用
	RemapIndex: 16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31 //阵营色色号，游戏中会随着玩家阵营色定义改动而改动
PaletteFromFile@player:
	Name: player
	Filename: temperat.pal 
	ShadowIndex: 4
```