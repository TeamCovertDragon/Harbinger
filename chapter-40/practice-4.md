# 实现 Thirst Mod、Tough As Nails、EnviroMine、TerraFirmaCraft 玩家饮水机制

  - 每一个玩家均拥有一个和饥饿值对应的口渴值（Thirsty）数据，和饥饿值一样随玩家保存
  - 喝水可以恢复口渴值。
    - 你可以假定这个水的来源只能是原版水瓶。
    - 加分项：允许用户指定哪些食物可以恢复口渴值。
  - 口渴值下降到一定程度时会给予玩家负面效果。
  - HUD
