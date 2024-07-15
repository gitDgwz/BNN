UI
============


CanvasLayer
------------

可以隐藏,可以跟随视口,常用于制作HUD

默认 2D 场景使用的索引为 0,
因此索引为 -1 的 CanvasLayer 会在下方绘制，
而索引为 1 的 CanvasLayer 会在上方绘制。
无论节点在图层中的 CanvasItem.z_index 是多少，
这一顺序都成立。