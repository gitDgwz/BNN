鼠标拖动相机
====================
用鼠标中键拖拽


因为要计算不同帧的数据,不可以将变量放方法中

::
    
    #记录的鼠标位置
    var mouse_positoin:Vector2
    #鼠标中键按下
    var mouse_wheel_press:bool

以下三个状态分别在不同帧里

::

    func camer_drag():
        #中键按下的那一帧
        if Input.is_action_just_pressed("mouse_wheel_press"):
            mouse_wheel_press=true
            mouse_positoin=get_viewport().get_mouse_position()

        #中键松开的那一帧    
        elif Input.is_action_just_released("mouse_wheel_press"):
            mouse_wheel_press=false
        
        #中键按下到松开的那一帧
        if mouse_wheel_press:
            #与上一帧的鼠标位置计算
            var mouse_position_drag:Vector2=get_viewport().get_mouse_position()-mouse_positoin
            self.position+=mouse_position_drag
            #在末尾记录鼠标位置,以下一帧使用
            mouse_positoin=get_viewport().get_mouse_position()