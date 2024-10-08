preference Storage
=============================


xaml ::

            <Entry x:Name="UserEntry"
                   Grid.Row="0"
                   Grid.Column="0"/>
            
            <Button x:Name="SaveButton"
                    Text="Save"
                    Grid.Row="0"
                    Grid.Column="1"
                    Clicked="SaveButton_Clicked"/>
            
            <Button x:Name="LoadButton"
                    Text="Load"
                    Grid.Row="1"
                    Grid.Column="0"
                    Clicked="LoadButton_Clicked"/>

            <Label x:Name="LoadLabel"
                   Grid.Row="1"
                   Grid.Column="1"/>

cs ::

        private void SaveButton_Clicked(object sender, EventArgs e)
        {
			Xamarin.Essentials.Preferences.Set("Entry", UserEntry.Text);
        }

        private void LoadButton_Clicked(object sender, EventArgs e)
        {
			LoadLabel.Text=
			Xamarin.Essentials.Preferences.Get("Entry","no value");
        }