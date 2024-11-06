Avalonia4单元测试
========================
*   安装 ``semi.Avalonia`` 包
*   App.axaml修改
    ::

        <!--上面添加-->
        xmlns:semi="https://irihi.tech/semi"

        <Application.Styles>
            <!--将Sytle替换为semi-->
            <!--<FluentTheme />-->
            <semi:SemiTheme Locale="zh-CN"></semi:SemiTheme>
        </Application.Styles>

*   在解决方案下在建一个项目dpa.Library,右键dpa.Library属性->可空引用类型禁用，并且删除Class1初始类。
*   在dpa.Library中创建Models文件夹，并在其中创建Poetry.cs
    ::

        [SQLite.Table("works")]
        public class Poetry
        {
            [SQLite.Column("id")] public int Id { get; set; }
            
            [SQLite.Column("name")] public int Name { get; set; }

            [SQLite.Column("author_name")] public string Author { get; set; } = string.Empty;

            [SQLite.Column("dynasty")] public string Dynasty { get; set; } = string.Empty;
            
            [SQLite.Column("content")] public string Content { get; set; } = string.Empty;

            private string _snippet;

            [SQLite.Ignore] public string Snippet => _snippet ??= Content.Split('。')[0].Replace("\r\n", " ");
        }