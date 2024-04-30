---
sidebar_position: 1
---

# 答桑下麻薯问

## 指令

/salt 
  - reload 重载插件
  - opengui <玩家> <界面：inlay、level、decompose> 打开界面
  - expansion <玩家> 为玩家手上物品扩容

## 配置

### config.yml

```yaml
#配置各种操作的花费
#格式为
#ui名:
#  Rune/Enchant:
#    货币类型id: "{ calculate '${level} * 2' }" 这里解析Asahi语句，不知道什么意思照抄就行 在''里面是公式，公式中${level}将被替换为等级
eco:
  decompose:
    Rune:
      eco1: "{ calculate '${level} * 2' }"
      eco2: "{ calculate '${level} * 3' }"
    Enchant:
      eco1: "{ calculate '${level} * 4' }"
      eco2: "{ calculate '${level} * 5' }"
  level:
    Rune:
      eco1: "{ calculate '${level} * 2' }"
      eco2: "{ calculate '${level} * 3' }"
    Enchant:
      eco1: "{ calculate '${level} * 4' }"
      eco2: "{ calculate '${level} * 5' }"
```

### embeddings.yml

```yaml
#容易理解，
#宝石id
红符文:
  attributes:
    #1级的属性，注意缩进和"-"
    - - "&7物理伤害: &f+50"
      - "&4吸血: &f+20%"
    #2级的属性，以此类推
    - - "&7物理伤害: &f+60"
      - "&4吸血: &f+25%"
红附魔:
  attributes:
    - - "&7物理伤害: &f+50"
      - "&4吸血: &f+20%"
```

## NeigeItems部分

### 核心原理

NeigeItems配置中，每个物品通过配置随机节点并在物品的lore、显示名等位置调用节点实现物品随机生成，

节点数据是树状的，每个节点有一个或多个子节点，子节点可以是叶子节点，也可以是节点。简单理解就是yaml结构

NeigeItems会在物品nbt上保存用到的节点数据，并可以通过api获取和修改

同时NeigeItems在生成物品时可以读取现成的节点数据

任何与Salt功能相关的物品数据（如宝石内容、等级、灵魂绑定等）都是节点

任何更改Salt物品的操作，都是通过修改节点数据后重新生成物品实现的
