
# Post \#74566743 [Link](https://stackoverflow.com/questions/74566743/)

## MAUI app not working in release mode but it works perfectly in debug mode

**Vote**: 5 (499/702) **Views**: 1251 (641/702) 

**Internal ID** \#2-6-415

Created at 2022-11-24 22:24:26

Tags: `c#` `maui` `community-toolkit-mvvm`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

My MAUI App when running in Debug mode in a emulator is working good but it is not running when I run this in release mode ( does not work either in emulator or a physical device) it crashes.
I am using the `Mvvm` architecture using a library called `CommunityToolkit.Mvvm`. I am mentioning this because I do not know if this is causing the problem.
I downloaded an app called `Crash Log Viewer` in my physical device to inspect the crashes and errors that apps are giving and I got this error:
```
This information is generated from the Crash Log View application. - https://play.google.com/store/apps/details?id=com.arumcomm.crashlogviewer

Process: com.companyname.ventas_citel
PID: 12230
UID: 10313
Flags: 0x20c8be44
Package: com.companyname.ventas_citel v1 (1.0)
Foreground: No
Process-Runtime: 63820382
Build: samsung/a10sub/a10s:11/RP1A.200720.012/A107MUBU6CVG3:user/release-keys

android.runtime.JavaProxyThrowable: System.FieldAccessException: Field `Microsoft.Maui.Controls.VisualElement:ZIndexProperty' is inaccessible from method `Ventas_Citel.Views.Login.Login:InitializeComponent ()'

   at Ventas_Citel.Views.Login.Login..ctor(LoginViewModel viewModel)
   at System.Reflection.RuntimeConstructorInfo.InternalInvoke(Object , Object[] , Boolean )
   at System.Reflection.RuntimeConstructorInfo.DoInvoke(Object , BindingFlags , Binder , Object[] , CultureInfo )
   at System.Reflection.RuntimeConstructorInfo.Invoke(BindingFlags , Binder , Object[] , CultureInfo )
   at Microsoft.Extensions.DependencyInjection.ServiceLookup.CallSiteRuntimeResolver.VisitConstructor(ConstructorCallSite , RuntimeResolverContext )
   at Microsoft.Extensions.DependencyInjection.ServiceLookup.CallSiteVisitor`2[[Microsoft.Extensions.DependencyInjection.ServiceLookup.RuntimeResolverContext, Microsoft.Extensions.DependencyInjection, Version=7.0.0.0, Culture=neutral, PublicKeyToken=adb9793829ddae60],[System.Object, System.Private.CoreLib, Version=6.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]].VisitCallSiteMain(ServiceCallSite , RuntimeResolverContext )
   at Microsoft.Extensions.DependencyInjection.ServiceLookup.CallSiteRuntimeResolver.VisitCache(ServiceCallSite , RuntimeResolverContext , ServiceProviderEngineScope , RuntimeResolverLock )
   at Microsoft.Extensions.DependencyInjection.ServiceLookup.CallSiteRuntimeResolver.VisitScopeCache(ServiceCallSite , RuntimeResolverContext )
   at Microsoft.Extensions.DependencyInjection.ServiceLookup.CallSiteVisitor`2[[Microsoft.Extensions.DependencyInjection.ServiceLookup.RuntimeResolverContext, Microsoft.Extensions.DependencyInjection, Version=7.0.0.0, Culture=neutral, PublicKeyToken=adb9793829ddae60],[System.Object, System.Private.CoreLib, Version=6.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]].VisitCallSite(ServiceCallSite , RuntimeResolverContext )
   at Microsoft.Extensions.DependencyInjection.ServiceLookup.CallSiteRuntimeResolver.Resolve(ServiceCallSite callSite, ServiceProviderEngineScope scope)
   at Microsoft.Extensions.DependencyInjection.ServiceLookup.DynamicServiceProviderEngine.<>c_DisplayClass2_0.<RealizeService>b_0(ServiceProviderEngineScope )
   at Microsoft.Extensions.DependencyInjection.ServiceProvider.GetService(Type , ServiceProviderEngineScope )
   at Microsoft.Extensions.DependencyInjection.ServiceLookup.ServiceProviderEngineScope.GetService(Type )
   at Microsoft.Maui.MauiContext.WrappedServiceProvider.GetService(Type serviceType)
   at Microsoft.Maui.Controls.ShellContent.<>c_DisplayClass19_0.<Microsoft.Maui.Controls.IShellContentController.GetOrCreateContent>b_0()
   at Microsoft.Maui.Controls.ElementTemplate.CreateContent()
   at Microsoft.Maui.Controls.Internals.DataTemplateExtensions.CreateContent(DataTemplate self, Object item, BindableObject container)
   at Microsoft.Maui.Controls.ShellContent.Microsoft.Maui.Controls.IShellContentController.GetOrCreateContent()
   at Microsoft.Maui.Controls.Platform.Compatibility.ShellSectionRenderer.OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
   at AndroidX.Fragment.App.Fragment.n_OnCreateView_Landroid_view_LayoutInflater_Landroid_view_ViewGroup_Landroid_os_Bundle_(IntPtr , IntPtr , IntPtr , IntPtr , IntPtr )
   at Android.Runtime.JNINativeWrapper.Wrap_JniMarshal_PPLLL_L(_JniMarshal_PPLLL_L , IntPtr , IntPtr , IntPtr , IntPtr , IntPtr )
    at crc640ec207abc449b2ca.ShellSectionRenderer.n_onCreateView(Native Method)
    at crc640ec207abc449b2ca.ShellSectionRenderer.onCreateView(ShellSectionRenderer.java:42)
    at androidx.fragment.app.Fragment.performCreateView(Fragment.java:2995)
    at androidx.fragment.app.FragmentStateManager.createView(FragmentStateManager.java:523)
    at androidx.fragment.app.FragmentStateManager.moveToExpectedState(FragmentStateManager.java:261)
    at androidx.fragment.app.FragmentManager.executeOpsTogether(FragmentManager.java:1840)
    at androidx.fragment.app.FragmentManager.removeRedundantOperationsAndExecute(FragmentManager.java:1764)
    at androidx.fragment.app.FragmentManager.execPendingActions(FragmentManager.java:1701)
    at androidx.fragment.app.FragmentManager.dispatchStateChange(FragmentManager.java:2849)
    at androidx.fragment.app.FragmentManager.dispatchViewCreated(FragmentManager.java:2777)
    at androidx.fragment.app.Fragment.performViewCreated(Fragment.java:3020)
    at androidx.fragment.app.FragmentStateManager.createView(FragmentStateManager.java:551)
    at androidx.fragment.app.FragmentStateManager.moveToExpectedState(FragmentStateManager.java:261)
    at androidx.fragment.app.FragmentManager.executeOpsTogether(FragmentManager.java:1840)
    at androidx.fragment.app.FragmentManager.removeRedundantOperationsAndExecute(FragmentManager.java:1764)
    at androidx.fragment.app.FragmentManager.execPendingActions(FragmentManager.java:1701)
    at androidx.fragment.app.FragmentManager.dispatchStateChange(FragmentManager.java:2849)
    at androidx.fragment.app.FragmentManager.dispatchActivityCreated(FragmentManager.java:2784)
    at androidx.fragment.app.FragmentController.dispatchActivityCreated(FragmentController.java:262)
    at androidx.fragment.app.FragmentActivity.onStart(FragmentActivity.java:478)
    at androidx.appcompat.app.AppCompatActivity.onStart(AppCompatActivity.java:246)
    at android.app.Instrumentation.callActivityOnStart(Instrumentation.java:1435)
    at android.app.Activity.performStart(Activity.java:8231)
    at android.app.ActivityThread.handleStartActivity(ActivityThread.java:3872)
    at android.app.servertransaction.TransactionExecutor.performLifecycleSequence(TransactionExecutor.java:221)
    at android.app.servertransaction.TransactionExecutor.cycleToPath(TransactionExecutor.java:201)
    at android.app.servertransaction.TransactionExecutor.executeLifecycleState(TransactionExecutor.java:173)
    at android.app.servertransaction.TransactionExecutor.execute(TransactionExecutor.java:97)
    at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2336)
    at android.os.Handler.dispatchMessage(Handler.java:106)
    at android.os.Looper.loop(Looper.java:246)
    at android.app.ActivityThread.main(ActivityThread.java:8653)
    at java.lang.reflect.Method.invoke(Native Method)
    at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:602)
    at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:1130)
```

Seems like the error is in the `LoginViewModel` so I am sharing you my code files:

```
public static class MauiProgram
{
    public static MauiApp CreateMauiApp()
    {
        var builder = MauiApp.CreateBuilder();
        builder.UseMauiApp<App>().UseMauiCommunityToolkit();

        builder
            .UseMauiApp<App>()
            .ConfigureFonts(fonts =>
            {
                fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
                fonts.AddFont("OpenSans-Semibold.ttf", "OpenSansSemibold");
            });


        //views
        builder.Services.AddSingleton<Login>();
        builder.Services.AddSingleton<Inventario>();
        builder.Services.AddSingleton<Clientes>();
        builder.Services.AddSingleton<Ventas>();
        builder.Services.AddSingleton<CuentasPorCobrar>();


        //viewModels
        builder.Services.AddSingleton<LoginViewModel>();
        builder.Services.AddSingleton<InventarioViewModel>();
        builder.Services.AddSingleton<ClientesViewModel>();
        builder.Services.AddSingleton<VentasViewModel>();
        builder.Services.AddSingleton<CuentasPorCobrarViewModel>();


        // services
        builder.Services.AddHttpClient<ILoginService, LoginService>();


        return builder.Build();


    }
}
```


```
public partial class LoginViewModel : BaseViewModel
{
    [ObservableProperty]
    string uuid;

    [ObservableProperty]
    private string _email;

    [ObservableProperty]
    private string _password;

    [ObservableProperty]
    private bool _isEnabled = true;

    private readonly ILoginService _loginService;
    public LoginViewModel(ILoginService loginService)
    {
        ComprobarUUID();
        _loginService = loginService;
        IsEnabled = true;
    }

    private async void ComprobarUUID()
    {
        // .. code
    }

    private async void CargarUUIDPorPrimeraVez()
    {
        // .. code
    }

    [RelayCommand]
    async void Copiar()
    {
        // .. code
    }



}
```


```
public partial class BaseViewModel : ObservableObject
    {
        [ObservableProperty]
        private bool _isBusy;

        [ObservableProperty]
        private string _title;
    }
```


```
public partial class Login : ContentPage
{
    public Login(LoginViewModel viewModel)
    {
        InitializeComponent();
        BindingContext = viewModel;
    }
}
```

I have working so hard in this app and I always been building this app through debug mode but today I had to deliver this app to the team but this did not work.
How do I solve this error? Do you need any more code?


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2022-12-29 07:31:26

------------

Remove zIndex from xaml. and use alternate any approch.
i had very bad hours finding out what causing crash in release mode. and this was the fix. can't beleave Maui is in production.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-11-25 01:16:05

------------

You can try to disable the compiled binding like below:
```
[XamlCompilation (XamlCompilationOptions.Skip)]
public partial class Login : ContentPage
{
    public Login(LoginViewModel viewModel)
    {
        InitializeComponent();
        BindingContext = viewModel;
    }
}
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2023-02-08 06:27:16

------------

I have same issue.
In My Case Xaml Error.
Below Code has Error.
```
<StackLayout Orientation="Horizontal"
            RadioButtonGroup.GroupName="interest" 
            RadioButtonGroup.SelectedValue="{Binding rdinterestTaxRATIO}">
<RadioButton Content="15.4%" 
             Value="15.4"
             RadioButtonGroup.GroupName="interest" 
             Margin="15"
             IsChecked="True"/>
<RadioButton Content="1.4%" 
             Value="1.4"
             RadioButtonGroup.GroupName="interest" 
             Margin="15"
             IsChecked="False"/>
<RadioButton Content="0%" 
             Value="0"
             RadioButtonGroup.GroupName="interest" 
             Margin="15"
             IsChecked="False"/>
```


Below code everything okay
```
<StackLayout Orientation="Horizontal"
            RadioButtonGroup.GroupName="interest" 
            RadioButtonGroup.SelectedValue="{Binding rdinterestTaxRATIO}">
<RadioButton Content="15.4%" 
                Value="15.4"
                Margin="15"
                IsChecked="True"/>
<RadioButton Content="1.4%" 
                Value="1.4"
                Margin="15"
                IsChecked="False"/>
<RadioButton Content="0%" 
                Value="0"
                Margin="15"
                IsChecked="False"/>
```


    
I just delete duplicated property.


------------
    
    