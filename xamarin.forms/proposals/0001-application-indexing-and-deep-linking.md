# Application Indexing and Deep Linking

* Proposal: [SE-0001](https://github.com/xamarin/xamarin-evolution/blob/master/proposals/0001-application-indexing-and-deep-linking.md)
* Author(s): [Jason Smith](https://github.com/jassmith)
* Status: **[Implemented]**
* Review manager: TBD

## Introduction

This feature is intended to support two primary cases:
* To provide events for developers (and MVVM frameworks such as Prism) to handle and respond to deep-link requests
* To provide an API for developers to publish metadata for app indexing as users navigate through their applications.

(Note, this proposal is ported from the old RFE format, so is not perfectly aligned with this document format)

## Motivation

Deep linking functionality is increasingly common and expected in mobile apps. Users, marketers, and app designers all find it very useful and satisfying. App indexing allows apps that would otherwise be forgotten after a few uses to stay relevant by appearing in search results.

## Proposed solution

Fixme

## Detailed design

```
public class Application : Element 
{
  ...
  // New API
  protected virtual void OnAppLinkRequestReceived(Uri uri)
  public IAppLinks AppLinks {get;}

  …

  void IApplicationController.SetAppIndexingProvider (IAppIndexingProvider appIndexing);
}

public interface IApplicationController
{
  // only new api shown
  void SetAppIndexingProvider(IAppIndexingProvider appIndexing);
}

public interface IAppLinks
{
  IEnumerable<IAppLinkEntry> KnownLinks {get;}

  void RegisterLink (IAppLinkEntry appLink);
  void DeregisterLink (IAppLinkEntry appLink);
  void DeregisterLink (Uri appLinkUri);
  void DeregisterAll (Uri appLinkUri);

}

public interface IAppIndexingProvider 
{
  IAppLinks AppLinks { get; }
}

public interface IAppLinkEntry
{
  // These are all implemented as BindableProperties on AppLinkEntry except KeyValues
  string Title { get; set; }
  string Description { get; set; }
  ImageSource Thumbnail { get; set; } // might require special handling for remote images
  Uri AppLinkUri { get; set; } // can this handle “funky” uri schemes?
  IDictionary<string, string> KeyValues { get; }
  bool IsLinkActive { get; set; }
}

public class AppLinkEntry : Element, IAppLinkEntry
{
  // Implements interfaces

  public static AppLinkEntry FromUri (Uri uri, ...);
}

Application.Current.AppLinks.Remove (AppLinkEntry.FromUri (uri));
Application.Current.AppLinks.Remove (uri);

// responding to incoming events
protected override void OnAppLinkRequestReceived (Uri uri)
{
  DoTheThingWithTheUri (uri);
  AppLinks.DeregisterLink (uri);
  AppLinks.RegisterLink (AppLinkEntry.Create (uri, …));
}

// Dumb usage
public class MyApp : Application
{
  public MyApp ()
  {
    // Populate all links early that we want in search no matter what
    if (!databasePopulated) {
      foreach (IAppLinkEntry link in MyLinks) {
        AppLinks.RegisterLink (link);
      }
    }
  }
}

public class ProductPage : ContentPage
{
  private IAppLinkEntry linkEntry;
  private IAppLinkEntry LinkEntry { 
    get {
      return linkEntry ?? (linkEntry = AppLinkEntry.Create (...));
    }
  } // returns sames instance each time

  protected override void OnAppearing ()
  {
    LinkEntry.IsLinkActive = true;
  }

  protected override void OnDisappearing ()
  {
    LinkEntry.IsLinkActive = false;
  }
}

// App.xaml
<Application>
  <Application.Resources>
    <ResourceDictionary>
      <LinkEntry x:Name=”detailLinkEntry” IsLinkActive=”{Binding IsActive}” Title=... />
    </ResourceDictionary>
  </Application.Resources>
</Application>

```

### Application Changes
Application will get an OnAppLinkRequestReceived method and a AppLinks property. If the user doesn’t want to handle deep links or app-indexing they can ignore these two members.

A user should be able to use the OnDeepLinkRequestReceived and OnAppLinkRequestReceived with or without AppIndexing.  
App-indexing Implementations
We will need to build samples to prove it can work. A Xamarin.Forms port of James Montemagno’s “MonkeysApp” would make a good starting point:
https://github.com/jamesmontemagno/MonkeysApp-AppIndexing
iOS-specific implementation
NSUserActivity is the recommended approach by Apple to add the item to the on-device index.

To handle AppLink requests the implementation will need to do the equivalent of the following:
```
// in AppDelegate.cs
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
  if (userActivity.UserInfo.ContainsKey (new NSString("link")))
  {
    var link = new Uri( userActivity.UserInfo[new NSString("link")].ToString());
    App.OnAppLinkRequestReceived (link);
  }
  return true;
}
```


Here is a sample of adding content to the app index

```
var activity = new NSUserActivity ("com.company.productname");
		
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString(deepLinkUri.ToString() ));

// Populate Activity
activity.Title = title;
activity.UserInfo = info;

//Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```
### Android-specific implementation 

### Windows-specific implementation
Windows will not have a working backend, just a dummy no-op implementation.



The implementation of actually responding to inbound OnApplicationContextReceived calls is up to the MVVM framework or the user depending on how they set up their app.

## Impact on existing code

Adds new API to Application class.

## Alternatives considered

Not ported over from RFE

-------------------------------------------------------------------------------

# Rationale

On Feb 10, 2016, the core team decided to accept this proposal.