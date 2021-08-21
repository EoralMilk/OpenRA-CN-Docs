# OpenRA - Yaml 语法

## 基本语法

* 大小写敏感（诸如 Buildable 不能写成 buildable， 否则无效，虽然不一定报错）

* 绝大部分代码只能用英文字符

* 使用缩进表示层级关系（推荐用 tab 键）

    ```yaml
    TNK:                       #第一层
      Buildable:                 #第二层
        Description: ...           #第三层
        Queue: Air                 #第三层
        BuildPaletteOrder: 13      #第三层
    ```

*   '#'之后到本行结尾的文本表示注释。注释必须遵从层级关系，注释在谁的下一层就要跟
    谁下一层的元素同缩进。可以接在已有语句后面。

    `正确`

    ```yaml
    Buildable:
      Description: ...
      Queue: Air
      BuildPaletteOrder: 13
      Prerequisites: ~disabled #~cabair, cabradr, gadept
    ```

    `正确`
    
    ```yaml
    ############################################
    # Conditions
    ############################################
    Cargo:
      MaxWeight: 1
      PipCount: 1
    ```

    `错误`

    ```yaml
    TNK:
    ##与第一层同缩进导致报错
      Cargo:
      MaxWeight: 1
      PipCount: 1
    ```



## 层级

*   基本层级

    ```yaml
    TNK:                       #第一层
      Buildable:                 #第二层
        Description: ...           #第三层
        Queue: Air                 #第三层
        BuildPaletteOrder: 13      #第三层
    ```

    *   第一层。 代表一个 yaml 对象，
        *   1.有且只有 yaml 对象才能被 OpenRA 引擎程序转为 Actor。
        
        *   2.然而，并非所有的 yaml 对象都必须具备转成合法 Actor 的所有条件，它们可以是存放共用代码的东西。
        
        *   3.可以随意命名。如果重名的对象，最后程序里这个对象名中的语句等于所有重名对象语句的中总集合。如果重名的对象内有相同名称的二层语句，见下文*`第二层第三点`*。
        
        （注意：有部分名字会被引擎里的特殊 Actor 使用，比如 Player 和 World。）
        
        （注意：合理的 yaml 对象名有助于 yaml 代码可读性和可维护性）
        
    *   第二层。一般代表特质（Trait），但是有两种情况是例外，见后文特殊用法。
        *   1.所有 Actor 的绝大部分属性和表现都被特质所定义。
        
        *   2.处于第二层的特质名本名不能随意命名，不然无从起效，拼错的特质会被忽略。
        
        *   3.同一个对象里重复的定义的特质名，最后程序里这个对象名中的该特质的属性等于所有重名特质中属性语句的总集合。如果重名的特质含有相同名称的第三层，见下文*`第三层第二点`*。
        
        *   4.特质由引擎代码编写定义。使用特质最好在引擎代码里搜索其属性和用法。
        
    *   第三层。代表特质的属性。
        *   1.属性由引擎代码编写定义。使用特质最好在引擎代码里搜索其属性和用法。
        
        *   2.同一个特质名里重复的属性，后录入属性值覆盖前属性。
        
            
    
*   第二层特殊语法：Inherits: yaml 对象名

    示例：

    ```yaml
    PDOX:
    Inherits: ^ScienceBuilding 
    Inherits@IDISABLE: ^DisableOnLowPowerOrPowerDown
    Inherits@shape: ^2x2Shape
    ```

    注意：此处\^ScienceBuilding，\^DisableOnLowPowerOrPowerDown，\^2x2Shape 都是 yaml对象，\^可以用在 yaml 对象命名里
    
*   “Inheirts”（继承）不是特质，而是直接使用某个 yaml 对象里的全部代码。利用这一点，我们可以先编写诸如代码方块里“Inheirts”的一些 yaml 对象，里面放上公用代码，之后多个相关 yaml 对象就可以复用它的代码，减少代码工作量提高 yaml 的可维护性。
    
*   同一个对象里重复的“Inheirts”，后录入“Inheirts: xxx”中的语句会覆盖它。为了使用多个继承，你可以使用“Inheirts@任意名称:”。后续只要“@任意名称”不重复，就不会覆盖。（@号后面合理的名称有助于 yaml 代码可读性和可维护性）
    
    
    
*   第二层特殊语法：删除（特质）

    ```yaml
    CLOUD1:
      Inherits: ^CombatHelicopter
      Inherits@1: ^AutoTargetAllAssaultMove
      -Selectable: #删除该特质。记住使用时，特质名后要跟冒号。
      -SelectionDecorations:
      -Repairable:
      -UpdatesPlayerStatistics:
    ```

    删除指将该特质以及其语句全部删除。可用于 Inherits 之后删除不需要的特质。你也可以先删除再添加，重新写该特质，这样可以规避诸如继承再继承带来的一系列同名特质语句总和效果、覆盖效果造成的困惑：

    ```yaml
    CLOUD1:
      Inherits: ^CombatHelicopter
      -Aircraft:
      Aircraft:
        CruiseAltitude: 1
        TurnSpeed: 6
        Repulsable: false
        AltitudeVelocity: 400
        Speed: 20
        MoveIntoShroud: true
    ```

    

## 特质

特质（Trait）是构成第二层的主要组成部分。在忽略特质的属性的前提下，目前引擎已有的特质有以下三个特征，在编写 yaml 时需要格外注意。

*   “可多重存在” 与 “只能有一个”

    有的特质可以存在多个，比如 Armament（武器装备），Power（电力）。为了使用多个可多重存在的特质，需要使用“该特质@任意名称:”。后续只要“@任意名称”不重复，就不会触发同名覆盖的规则。例如：

    ```yaml
    #这是一个可以同时主炮开火和发导弹坦克中的装备实现
    Armament@PRIMARY:
      Weapon: 4tnkgun
      LocalOffset: 1150,120,550, 1150,-120,550
      MuzzleSequence: muzzle
      Recoil: 127
      RecoilRecovery: 26
      PauseOnCondition: empdisable
    Armament@SECONDARY:
      Weapon: MammothTusk
      LocalOffset: 100,400,650, 100,-400,650
      PauseOnCondition: empdisable
    Armament@SECONDARYSECONDARY:
      Weapon: MammothTuskAA
      LocalOffset: 100,400,650, 100,-400,650
      PauseOnCondition: empdisable
    ```

    （@号后面合理的名称有助于 yaml 代码可读性和可维护性。）

    删除可多重存在特质时也要注意“@任意名称”这个标识。例如：

    ```yaml
    -Targetable@GROUND:
    -Targetable@AIRBORNE:
    -Targetable@Academy:
    -Targetable@FullHealth:
    ```

    有些特质有且只能有一个，比如 HP 特质、Aircraft 特质（飞行特质）。此类特质不能“特质@任意名称”实现多个，否则报错。
    
*   受到 Condition 控制和不受 Condition 控制

    有些特质受到 Condition 的控制。“Condition”是管辖域只在单个 Actor 内的“状态”，当 Actor可以获得/失去某种“状态”（靠一些特质提供），并且有特质名有针对该“状态”开启或者关闭的设置，那么“状态”就可以控制这个特质名的功能。并非所有特质都可以受 Condition 控制。

    ```yaml
    GrantConditionOnTerrain:
      Condition: inwater                          #提供某个状态
      TerrainTypes: Water
    WithFacingSpriteBody:
      RequiresCondition: inwater                  #这个特质会因获得该状态开启
    Armament:
      Weapon: HoverMissile
      LocalOffset: 100,150,384, 100,-150,384
      PauseOnCondition: inwater                   #这个特质会因获得该状态暂停作用
    ```

    状态支持简单的逻辑运算，比如 RequiresCondition: !inwater 和 PauseOnCondition: inwater功能基本一致。!，||，&&，()是可使用的基本逻辑运算符。
    
*   依赖其他特质的特质

    这种特质需要其他某特质存在才能起作用，否则报错，报错信息会提示你哪个特质需要以作为前置。（例子非常少）

