---
layout: post
title: "Minecraft Forge: кастомные тексты в GUI контейнеров"
date: 2015-02-08 11:40:45 +0300
comments: true
categories: 
  Minecraft
  Forge
  Modding
  API
image:
  feature: /images/splash-1.png
---

При разработке очередной версии SmartCursor"а мне потребовалось добавить новую возможность - отображение мода, из которого загружен очередной блок или предмет, в GUI контейнеров, будь то инвентарь игрока, сундуки или даже рюкзаки из мода Backpacks. 

<!-- more -->
Задача, мягко говоря, не совсем обычная, и возникает она у модмейкеров не слишком часто, однако после изучения API я обнаружил то, что мне нужно.

#### Событие ItemTooltipEvent
Данное событие вызывается системой, когда игрок наводит курсор на слот, заполенный некоторым предметом. Событие содержит три поля: `ItemStack`, который вызвал это событие, список текстов для подсказки, а также флаг `showAdvancedItemTooltips`, указывающий на то, что подсказка вызывается в режиме отображения дополнительной информации, которая, кстати говоря, активируется клавишами `F3 + H`. Класс события расположен в пакете `net.minecraftforge.event.entity.player`.

#### Подписка на событие
Подписка ничем не отличается от подписки на другие события. Создаем класс, в котором описываем некоторый метод, принимающий в качестве аргумента событие, а затем вешаем на метод аннотацию `SubscribeEvent`:
```java Sample Code
@SubscribeEvent
public void addTooltipText(ItemTooltipEvent event) { }
```
После чего подключаем экземпляр к шине событий Forge:
```java Sample Code
MinecraftForge.EVENT_BUS.register(new SomeClass());
```

#### Добавление текстов
Собственно, ничего сложного, достаем из события список текстов и добавляем в него свои тексты:
```java Sample Code
ItemStack stack = event.itemStack;
event.toolTip.add("Unlocalized name:" + stack.getUnlocalizedName());
```
На примере выше мы добавим к подсказке нелокализованную версию имени блока.

#### Раскраска текстов
Опционально мы можем сделать наши тексты более яркими. Воспользовавшись перечислением `EnumChatFormatting` из пакета `net.minecraft.util` мы можем сделать текст таким, как хотим этого мы. В качестве примера сделаем слова `Unlocalized name:` из примера выше голубого цвета, а шрифт изменим на курсив:
```java Sample Code
event.toolTip.add(EnumChatFormatting.AQUA + "" + EnumChatFormatting.ITALIC + "Unlocalized name:" + EnumChatFormatting.RESET + stack.getUnlocalizedName());
```

#### Результат
Вот собственно и весь результат:

![](/images/screen-1.jpg)