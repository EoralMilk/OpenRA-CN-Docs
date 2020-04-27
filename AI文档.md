# AI编写指南

## OpenRA AI的编写核心：

- 一般情况下，AI不会作弊，也就是AI在游戏中无法使用人类玩家无法使用的资源、情报和生产方式，但是AI可以在游戏中建造专属建筑和单位（也就是说通过专属的单位和建筑物可以强行让AI作弊）

- AI对于游戏系统的冲击应当尽量最小（因为目前版本中AI会涉及C#硬编码）

- AI的行为应当与MOD的资源和单位适应

- 应当在每个核心MOD中都完备测试AI

- 为了自身的便利性，应该优先考虑简单的AI解决方案

- AI应当给予玩家挑战性
  
- AI不是玩家的保姆，从RTS游戏中移除微操不是OpenRA的目标

- OpenRA的AI在性能或表现上应该接近WW的原作AI


    目前OpenRA工作组在考虑使用LUA语言制作AI以免除现有的硬编码系统。

## AI的基础行为：

- 通过查表和权重选择建造建筑物.建筑物将会从科技树的从上至下的方式被排程。
  
- 通过查表方式选择建造随机单位。当有八个以上单位集结的时候，选择一个敌对玩家并进行攻击
- 在电量小于20%或小于50的时候建造发电厂
- 选择建筑物集结点以防止单位路径堵塞
- 跟踪并攻击每个仇恨值最高的敌人
- 保护基地和矿车
- 在没有敌对防空单位的情况下，使用空军按上述行为列表攻击敌人
- 在资源地附近建筑矿石精炼厂并保护它
- 在小队中依单位数值权重选择单位行为
- 保证矿石精炼厂和矿仓的数量在一定值期间
- 在允许的时候试图使用超级武器

## ai.yaml的编写:

**范例：**
```
AI@RushAI:
		Name: Rush AI //AI的名称
//一般名字 CommonNames 部分可以不用管它
		BuildingCommonNames: //建筑物的一般名字
			ConstructionYard: fact
			Refinery: proc
			Power: powr,apwr
			Barracks: barr,tent
			VehiclesFactory: weap
			Production: barr,tent,weap,afld,hpad,spen,syrd
			Silo: silo
		UnitsCommonNames: //单位的一般名字
			Mcv: mcv
//以下是核心部分
		BuildingLimits: //AI的建筑上限
			proc: 4
			barr: 1
			tent: 1
			dome: 1
			weap: 1
			spen: 1
			syrd: 1
			hpad: 4
			afld: 4
			atek: 1
			stek: 1
			fix: 1
		BuildingFractions: //AI的建筑物建造权重值，如果设置了新建筑，可以在这里加上新建筑的注册名和权重值
			proc: 30%
			powr: 35%
			barr: 1%
			tent: 1%
			weap: 1%
			pbox: 7%
			gun: 7%
			tsla: 5%
			ftur: 10%
			agun: 5%
			sam: 5%
			atek: 1%
			stek: 1%
			fix: 0.1%
			dome: 10%
		UnitsToBuild: //AI的单位建造权重值，如果设置了新单位，可以在这里加上新单位注册名和权重值
			e1: 50%
			e3: 10%
			apc: 30%
			jeep: 40%
			arty: 15%
			v2rl: 40%
			ftrk: 50%
			1tnk: 70%
			2tnk: 25%
			3tnk: 50%
		SquadSize: 20 //小队单位数量值
```

## 超级武器范例：
```
SupportPowerDecision@nukepower: //AI注册名
			OrderName: NukePowerInfoOrder //AI名称
			MinimumAttractiveness: 3000 //AI在敌人单位/建筑物总价为多少钱的时候选择投放超级武器
			Consideration@1: //AI选择1
				Against: Enemy //进攻：对方
				Types: Structure //选择：建筑物
				Attractiveness: 1 //目标金钱权重倍数（即按照指定倍数计算目标的MinimumAttractiveness值，如果达到了MinimumAttractiveness的值，则发动超级武器攻击）
				TargetMetric: Value //目标指定：敌人单位金钱数
				CheckRadius: 5c0 //打击范围：以核心目标5格以内单位全部计入MinimumAttractiveness值的计算范围内
			Consideration@2: //AI选择2
				Against: Ally //进攻：我方
				Types: Air, Ground, Water //选择：海陆空单位
				Attractiveness: -10 //当敌方单位金钱权重值大于我方权重10倍时，无视攻击范围内的我方单位，发动超级武器
				TargetMetric: Value 
				CheckRadius: 7c0 //打击范围，以核心目标7格以内单位全部计入MinimumAttractiveness值的计算范围内
```