shell
==============
命令行解释器

创建shell脚本
---------------

#.  ``vi hello.sh`` 创建shell文件
#.  ``chmod 777 hello.sh`` 添加权限
#.  第一行输入#!/bin/bash,意思为使用bash解释器

shell小游戏
-----------------

#.  ``vi game.sh`` 创建shell脚本
#.  ``nano game.sh`` 用nano打开game.sh 
    ::

        #!/bin/bash
        echo "请输入你的用户名:"
        read name
        echo "你好,$name"

#.  ``chmod +x game.sh`` 添加执行权限
#.  ``./game.sh`` 执行shell,并输入参数
#.  改为使用外部传递参数,修改game.sh中的内容
    ::

        #!/bin/bash
        echo "请输入你的用户名:"
        #read name
        name=$1
        channel=$2
        echo "你好,$name,欢迎来到$channel!"
    
    .. note:: 

        *   在shell中,#开头的代表注释
        *   特殊变量

        ::

            $# 传递给脚本或者函数的位置参数的个数
            $? 上一命令的退出状态码.0表示没有错误,非0值表示有错误
            $* 传递给脚本或函数的位置参数,双引号包围时作为一个整体
            $@ 传递给脚本或函数的位置参数
            $$ 当前shell进程的IP(PID)
            $! 最后一个后台命令的进程ID
            $0 当前脚本的名称
            $1-n 脚本或函数的位置参数     
            
#.  ``./game.sh Geek GeekHour`` 执行的时候直接传递参数
#.  尝试使用环境变量传递参数在命令行中直接赋值, ``name=Geek`` , ``channel=GeekHour``,
    并且使用 ``echo $name`` , ``echo $channel`` 查看,
    可以发现这两个变量的值也是没有问题的
#.  ``./game.sh`` 执行发现并没有输入任何参数,
    这是因为这两个变量仅仅是普通变量,不是环境变量,并且只能在当前的shell会话中有效
#.  使用 ``export`` 命令, ``export name=Geek`` , ``export channel=GeekHour``,设置变量并且变为环境变量
    ($name也被修改),export定义的变量只在当前shell会话中有效  
#.  修改game.sh,注释掉name和channel
    ::

        #!/bin/bash
        echo "请输入你的用户名:"
        #read name
        #name=$1
        #channel=$2
        echo "你好,$name,欢迎来到$channel!"

#.  ``./game.sh`` 执行文件,可以看到参数被自动赋于

#.  如何让参数一直存在,而不是只在一个会话里面,在用户根目录 ``ls -al`` ,存在 .bashrc 和 .profile 文件
    
    *   .profile 只在用户登录的时候执行,并且只执行一次
    *   .bashrc 在每次新打开一个终端,或者新建一个shell会话时候执行

    通常.profile里面会调用.bashrc,所以一般推荐把环境变量放到.bashrc里面,
    另外,系统根目录下也会有一些系统级别的配置文件

#.  ``cd .bashrc`` 进入.bashrc文件,在最下面添加 ``export name=Geek`` ,
    ``export channel=GeekHour``.
#.  输入 ``source .bashrc`` 或者 ``. .bashrc`` 或者 退出shell,再重新登录一下,以便.bashrc文件重新加载

#.  ``echo $name`` 和 ``echo $channel`` 可以看到重新出现变量, ``./game.sh`` 成功导入参数
    
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#.  生成一个随机数,打开game.sh文件,
    命令要用 `` 或者 $() 包括起来,才会被当成一个整体
    , **建议使用$()**,可读性和灵活性更好
    ::

        echo "请输入你的用户名:"
        #read name
        #name=$1
        #channel=$2
        echo "你好,$name,欢迎来到$channel!"
        number=$(shuf -i 1-10 -n 1)
        echo $number

#.  加入猜数字环节,使用  ``if`` 语句判断,是否正确,并且提示大小
    ::
        
        #!/bin/bash

        echo "请输入你的用户名:"
        #read name
        #name=$1
        hannel=$2
        echo "你好,$name,欢迎来到$channel!"
        number=$(shuf -i 1-10 -n 1)
        echo $number

        echo "请输入一个数字"
        read guess
        if [[ $guess -eq $number ]]; then
                echo "你猜对了"
                
        elif [[ $guess -lt $number ]]; then
                echo "太小了"
        else
                echo "太大了"
        fi

#.  加入 ``while`` 语句,可以多次猜答案,而不是猜一次就退出

    ::
            
        #!/bin/bash

        echo "请输入你的用户名:"
        #read name
        #name=$1
        hannel=$2
        echo "你好,$name,欢迎来到$channel!"
        number=$(shuf -i 1-10 -n 1)
        echo $number

        while [[ $guess -ne $number ]]
        do
        echo "请输入一个数字"
        read guess
        if [[ $guess -eq $number ]]; then
                echo "你猜对了"
        elif [[ $guess -lt $number ]]; then
                echo "太小了"
        else
                echo "太大了"
        fi
        done



#.  在猜中之后还可以选择是否继续, **将while后面的判断改为true** ,
    执行命令的运算使用 ``$(())``
    ::
            
        #!/bin/bash

        echo "请输入你的用户名:"
        #read name
        #name=$1
        hannel=$2
        echo "你好,$name,欢迎来到$channel!"
        number=$(shuf -i 1-10 -n 1)
        echo $number

        while true
        do
        echo "请输入一个数字"
        read guess
        if [[ $guess -eq $number ]]; then
                echo "你猜对了,你还要继续吗"
                read choice
                if [[ $choice = "y" ]] || [[ $choice = "Y" ]]; then
                        number=$((RANDOM % 10 + 1))
                        echo $number
                        continue
                else
                        break
                fi
        elif [[ $guess -lt $number ]]; then
                echo "太小了"
        else
                echo "太大了"
        fi
        done


shell语法
----------------

*   变量默认为全局的
*   局部变量需要在前面添加local关键词,只在函数内部有效 
*   命令要用 `` 或者 $() 包括起来,才会被当成一个整体,
    **建议使用$()**,可读性和灵活性更好,
    例如 ``$(shuf -i 1-10 -n 1)``
*   $里面需要运算的要用(())括起来,这样才会被当成一个整体,例如 ``$((RANDOM %10 + 1))``
*   变量的赋值不需要$,取值需要$
*   
    ::

        整数比较

        lt,less than,小于
        le,less than or equal to,小于等于
        gt,greater than,大于
        ge,greater than or equal to,大于等于
        eq,equal to,等于
        ne,not equal to,不等于

        字符串比较直接使用=

*   关键字前后需要空格

*   if 语句
    ::
        
        例:
        if [[ condition ]]; then
            #code...
        elif [[ condition ]]; then
            #code...
        else
            #code...
        fi

*   while 语句
    ::

        while [[ condition ]]
        do
        #code....
        done

        或者

        while true
        do 
        #code....
        done