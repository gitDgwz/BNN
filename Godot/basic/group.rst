group
=============

添加到组::

    add_to_group("guards")


调用组内所有成员的某个方法::

    get_tree().call_group("guards", "enter_alert_mode")

获得某个组的所有成员::

    get_nodes_in_group()

返回指定组中的第一个节点，如果组为空或不存在，则返回 null。::

    get_first_node_in_group()