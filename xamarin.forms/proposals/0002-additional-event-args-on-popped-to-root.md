# Additional EventArgs On PoppedToRoot

* Proposal: [SE-0002](https://github.com/xamarin/xamarin-evolution/blob/master/proposals/0002-additional-event-args-on-popped-to-root.md)
* Author(s): [Johan Karlsson](https://github.com/johankson)
* Status: **[Awaiting review](#rationale)**
* Review manager: TBD

## Introduction

When calling NavigationPage.PopToRoot there is no way to determine what pages
that are dismissed in that process. This proposal is about extending forms
to give the user a list of pages that was dismissed.

Xamarin-evolution thread: [link to the discussion thread for that proposal](http://lists.ximian.com/pipermail/forms-devel/2016-May/000023.html)

## Motivation

The motivation for this proposal is that information is that as a developer 
you have no way of knowing when pages are removed from the NavigationPage when you call
PopToRoot().

In certain cases this is a bad situation since you might want to dereference/unsubscribe/clear 
resources as soon as the page is removed from the stack.

## Proposed solution

The solution to the problem is to create a new EventArgs class that inherits from NavigationEventArgs and that
takes the extra information as a property.

## Detailed design

The design would be

A) Create a new event args inheriting from NavigationEventArgs

```csharp
  public class PoppedToRootNavigationEventArgs : NavigationEventArgs
  {
    public PoppedToRootNavigationEventArgs(Page page) : base(page)
    {
    }
    
    public List<Page> PoppedPages { get; set; }
  }
```

B) Modify PopToRootAsync

```csharp
  async Task PopToRootAsyncInner(bool animated)
  {
    if (((INavigationPageController)this).StackDepth == 1)
      return;

    var root = (Page)InternalChildren.First();

    // start new
    var childrenToRemove = InternalChildren.ToArray().Where(c => c != root);  
    childrenToRemove.ForEach(c => InternalChildren.Remove(c));                
    // end new

    CurrentPage = root;

    var args = new NavigationRequestedEventArgs(root, animated);

    EventHandler<NavigationRequestedEventArgs> requestPopToRoot = PopToRootRequestedInternal;
    if (requestPopToRoot != null)
    {
      requestPopToRoot(this, args);

      if (args.Task != null)
        await args.Task;
    }
            
    if (PoppedToRoot != null)
    // start new
      PoppedToRoot(this, new PoppedToRootNavigationEventArgs(root) { PoppedPages = childrenToRemove.OfType<Page>().ToList()});
    // end new
	}
```

## Impact on existing code

The impact will be low since we cannot change the signature of PoppedToRoot. (see the discussion thread). 
Even if we change the signature it should work without breaking changes if the
PoppedToRootNavigationEventArgs inherit from NavigationEventArgs. I don't however have the 
proper knowledge in Xamarin Forms in general to determine that.

## Alternatives considered

We discussed an alternative option to call the Popped event for each page but that might most
likely break current behavior.

-------------------------------------------------------------------------------

# Rationale

-
