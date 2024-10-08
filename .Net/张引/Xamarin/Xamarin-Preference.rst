Preferences
====================
偏好存储:储存数据,键值对,但是是持久型数据,关闭仍然有值,卸载则无.

xaml ::

        #输入文本框
        <Entry Grid.Row="0"
               Grid.Column="0"
               Margin="4"
               x:Name="PreferenceEntry"/>

        <Button Grid.Row="0"
                Grid.Column="1"
                x:Name="PreferenceSaveButton"
                Text="Save"
                Margin="4"
                #点击事件,保存
                Clicked="PreferenceSaveButton_Clicked"/>

        <Button Grid.Row="1"
                Grid.Column="0"
                x:Name="PreferenceReadButton"
                Text="Read"
                Margin="4"
                #点击事件,读取
                Clicked="PreferenceReadButton_Clicked"/>

        #用于显示的Label
        <Label Grid.Row="1"
               Grid.Column="1"
               Margin="4"
               x:Name="PreferenceResultLabel"/>


cs ::

        private void PreferenceSaveButton_Clicked(object sender, EventArgs e)
        {
            #储存键值对,"Capabilities.Preference.Key"-PreferenceEntry.Text
            Xamarin.Essentials.Preferences.Set("Capabilities.Preference.Key",
                PreferenceEntry.Text);
        }

        private void PreferenceReadButton_Clicked(object sender, EventArgs e)
        {   
            #获取键值,"Capabilities.Preference.Key",如果没有则为"No Key"
            PreferenceResultLabel.Text =
            Xamarin.Essentials.Preferences.Get("Capabilities.Preference.Key",
                "No Key");
        }