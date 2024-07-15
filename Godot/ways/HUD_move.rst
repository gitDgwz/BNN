视角移动
=============


鼠标拖动
------------

#.  创建一个CanvasLayer 作为HUD
#.  创建一个camera作为其子类
#.  加入一下脚本 ::

        extends CanvasLayer

        @onready var camera_2d = $Camera2D

        func _physics_process(delta):
            camer_move(delta)
            
        func camer_move(delta):
            if (get_viewport().get_mouse_position().x<=ProjectSettings.get_setting("display/window/size/viewport_width")/20):
                camera_2d.position.x-=200*delta
            if (get_viewport().get_mouse_position().x>=ProjectSettings.get_setting("display/window/size/viewport_width")/20*19):
                camera_2d.position.x+=200*delta
            if (get_viewport().get_mouse_position().y<=ProjectSettings.get_setting("display/window/size/viewport_height")/20):
                camera_2d.position.y-=200*delta
            if (get_viewport().get_mouse_position().y>=ProjectSettings.get_setting("display/window/size/viewport_height")/20*19):
                camera_2d.position.y+=200*delta
        
.. note:: 

    *   ``ProjectSettings.get_setting("display/window/size/viewport_height")`` 获得项目设置里面的值,路径在项目设置里有显示,
        ProjectSettings是储存全局变量的类,可以直接调用
    *   为什么要使用ProjectSettings而不是直接使用 ``get_viewport().size`` 来获得视图大小,
        是因为我的窗口设置为1920*1080,但是我启用了视图拉伸,拉伸后为1152*648.
        ``get_viewport().get_mouse_position()`` 获得的是游戏原本分辨率的位置,
        ``get_viewport().size`` 获得的是拉伸后的大小,也就是视口大小,因此两个数值不匹配

