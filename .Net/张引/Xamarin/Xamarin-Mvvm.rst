Mvvm
==================================

安装包 MvvmLightlibs

MainPageViewModel.cs ::

    using GalaSoft.MvvmLight;
    using GalaSoft.MvvmLight.Command;
    using System;
    using System.Collections.Generic;
    using System.Text;

    namespace Mvvm.ViewModels
    {
        public class MainPageViewModel : ViewModelBase
        {
            /// <summary>
            /// 结果
            /// </summary>
            private string _result ;
            public string Result { 
                get => _result; 
                #这里Set()函数是个框架里面的方法,会触发属性更改信号
                set => Set(nameof(Result), ref _result, value); }

            #RelayCommand是ICommand的实现类,ICommand有个action事件在里面
            private RelayCommand _helloCommand;
            public RelayCommand HelloCommand => 
                _helloCommand ?? (_helloCommand = new RelayCommand(() => Result = "Hello World!"));



        }
    }


ViewModelLocator.cs ::
    
    using GalaSoft.MvvmLight.Ioc;
    using System;
    using System.Collections.Generic;
    using System.Text;

    namespace Mvvm.ViewModels
    {
        public class ViewModelLocator
        {   
            public ViewModelLocator() {
                #容器注册MainPageViewModel
                SimpleIoc.Default.Register<MainPageViewModel>();    
            }
            #获取MainPageViewModel的时候由容器供给
            public MainPageViewModel MainPageViewModel=>
                SimpleIoc.Default.GetInstance<MainPageViewModel>();
        }
    }

App.xaml ::

    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                #将Mvvm.ViewModels创建名字空间为vm,程序集为Mvvm
                xmlns:vm="clr-namespace:Mvvm.ViewModels;assembly=Mvvm"
                x:Class="Mvvm.App">

        <Application.Resources>
            #将vm名字空间里面的ViewModelLocator设置为全局静态资源
            <vm:ViewModelLocator x:Key="ViewModelLocator"/>
        </Application.Resources>
    </Application> 

MainPage.xaml ::
    
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                x:Class="Mvvm.MainPage"
                #绑定ViewModelLocator里面的MainPageViewModel
                BindingContext="{Binding MainPageViewModel, Source={StaticResource ViewModelLocator}}">


        <StackLayout>
            <Label FontSize="48"
                x:Name="ResultLabel"
                #绑定MainPageViewModel里面的Result
                Text="{Binding Result}"/>

            <Button x:Name="ClickMeButton"
                    FontSize="48"
                    Text="Click Me!"
                    #绑定MainPageViewModel里面的HelloCommand
                    Command="{Binding HelloCommand}"/>
            
        </StackLayout>

    </ContentPage>

