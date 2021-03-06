---
title: "Device Class"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
---

# Device Class

The [`Device`](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) class contains a number of properties and methods to help developers customize layout and functionality on a per-platform basis.

In addition to methods and properties to target code at specific hardware types and sizes, the `Device` class includes the [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) method which should be used when interacting with UI controls from background threads.

<a name="providing-platform-values" />

## Providing Platform-Specific Values

Prior to Xamarin.Forms 2.3.4, the platform the application was running on could be obtained by examining the [`Device.OS`](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) property and comparing it to the [`TargetPlatform.iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/), [`TargetPlatform.Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/), [`TargetPlatform.WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/), and [`TargetPlatform.Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) enumeration values. Similarly, one of the [`Device.OnPlatform`](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) overloads could be used to provide platform-specific values to a control.

However, since Xamarin.Forms 2.3.4 these APIs have been deprecated and replaced. The [`Device`](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) class now contains public string constants that identify platforms – [`Device.iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), [`Device.Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), [`Device.WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/), [`Device.WinRT`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinRT/), [`Device.UWP`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), and [`Device.macOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.macOS/). Similarly, the  [`Device.OnPlatform`](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) overloads have been replaced with the [`OnPlatform`](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) and [`On`](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) APIs.

In C#, platform-specific values can be provided by creating a `switch` statement on the [`Device.RuntimePlatform`](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) property, and then providing `case` statements for the required platforms:

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.WinPhone:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

The [`OnPlatform`](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) and [`On`](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) classes provide the same functionality in XAML:

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, WinPhone, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

The [`OnPlatform`](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) class is a generic class and so must be instantiated with an `x:TypeArguments` attribute that matches the target type. In the [`On`](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) class, the [`Platform`](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) attribute can accept a single `string` value, or multiple comma-delimited `string` values.

> [!IMPORTANT]
> Providing an incorrect `Platform` attribute value in the `On` class will not result in an error. Instead, the code will execute without the platform-specific value being applied.

<a name="Device_Idiom" />

## Device.Idiom

The `Device.Idiom` can be used to alter layouts or functionality depending on the device the application is running on. The [`TargetIdiom`](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/) enumeration contains the following values:

-  **Phone** – iPhone, iPod touch, Windows Phone, Android devices narrower than 600 dips^
-  **Tablet** – iPad, Windows 8.1 computers, Android devices wider than 600 dips^
-  **Desktop** – only returned in [UWP apps](~/xamarin-forms/platform/windows/installation/universal.md) on Windows 10 desktop computers (returns `Phone` on mobile Windows devices, including in Continuum scenarios)
-  **TV** – Tizen TV devices
-  **Unsupported** – unused

*^ dips is not necessarily the physical pixel count*

`Idiom` is especially useful for building layouts that take advantage of larger screens, like this:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

<a name="Device_Styles" />

## Device.Styles

The [`Styles` property](~/xamarin-forms/user-interface/styles/index.md) contains built-in style definitions that can be applied to some controls' (such as `Label`) `Style` property. The available styles are:

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

<a name="Device_GetNamedSize" />

## Device.GetNamedSize

`GetNamedSize` can be used when setting [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) in C# code:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

<a name="Device_OpenUri" />

## Device.OpenUri

The `OpenUri` method can be used to trigger operations on the underlying platform, such as open a URL in the native web browser (**Safari** on iOS or **Internet** on Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

The [WebView sample](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) includes an example using `OpenUri` to open URLs and also trigger phone calls.

The [Maps sample](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) also uses `Device.OpenUri` to display maps and directions using the native **Maps** apps on iOS and Android.

<a name="Device_StartTimer" />

## Device.StartTimer

The `Device` class also has a `StartTimer` method which provides a simple way to trigger time-dependent tasks that works in Xamarin.Forms common code (including PCLs). Pass a `TimeSpan` to set the interval and return `true` to keep the timer running or `false` to stop it after the current invocation.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

If the code inside the timer interacts with the user-interface (such as setting the text of a `Label` or displaying an alert) it should be done inside a `BeginInvokeOnMainThread` expression (see below).

<a name="Device_BeginInvokeOnMainThread" />

## Device.BeginInvokeOnMainThread

User interface elements should never be accessed by background threads, such as code running in a timer or a completion handler for asynchronous operations like web requests. Any background code that needs to update the user interface should be wrapped inside [`BeginInvokeOnMainThread`](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/). This is the equivalent of `InvokeOnMainThread` on iOS, `RunOnUiThread` on Android, and `Dispatcher.BeginInvoke` on Windows Phone.

The Xamarin.Forms code is:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Note that methods using `async/await` do not need to use `BeginInvokeOnMainThread` if they are running from the main UI thread.

## Summary

The Xamarin.Forms `Device` class allows fine-grained control over functionality and layouts on a per-platform basis - even in common code (either PCL or Shared Projects).


## Related Links

- [Device Sample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Styles Sample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)
