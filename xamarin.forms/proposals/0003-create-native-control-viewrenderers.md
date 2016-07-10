# Add GetNativeControl to `ViewRenderer<TView, TNativeView>`

* Proposal: [SE-0003](https://github.com/xamarin/xamarin-evolution/blob/master/proposals/0003-name.md)
* Author(s): [Toni Petrina](https://github.com/tpetrina)
* Status: **[Awaiting review](#rationale)**
* Review manager: TBD

## Introduction

Enable providing own subclass of native control in platform renderers. Platform renderers create instances of native controls and it is hard to simply use user-provided subclass of such control.

Xamarin-evolution thread: [link to the discussion thread for that proposal](http://lists.ximian.com/pipermail/forms-devel/2016-July/000078.html).

## Motivation

Platform renderers based on `ViewRenderer<TView, TNativeView>` create instances of `TNativeView` in `OnElementChanged` by default unless  `SetNativeControl` is called before it. However, the implementation in most renderers does a lot more than simply create the control - it also hooks up events and sometimes even creates other controls (see `ListViewRenderer`). This presents problems for custom renderers where no logic is added to the renderer, but a subclass of the original native control is required.

Using `SetNativeControl` to supply custom subclass of the appropriate native control doesn't offer the same functionality and may even break apps offered by the default renderer (`ListViewRenderer` doesn't even work if you supply subclass).

In certain cases a subclass of native control must be used for certain features. Example is overriding the default behavior of the platform control in renderer which cannot be done via properties and events.  Consider the following snippet taken from `ButtonRenderer` on Android:

```csharp
if (button == null)
{
	button = new AButton(Context);
	button.SetOnClickListener(ButtonClickListener.Instance.Value);
	button.Tag = this;
	SetNativeControl(button);
	_textColorSwitcher = new TextColorSwitcher(button.TextColors);
	button.AddOnAttachStateChangeListener(this);
}
```

Let's assume the user subclassed the native `AButton` control and named it `AButtonSubclass`. If the user only wants to replace the default control from the renderer while keeping everything else intact, naive approach would look like the following snippet:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
  protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
  {
    var control = new AButtonSubclass(Context);
    SetNativeControl(control);
    base.OnElementChanged(e);
  }
}
```

However, by using the above approach user is missing three important things:

 1. The `SetOnClickListener` method won't be called.
 2. The `Tag` property won't be set correctly.
 3. Private field `_textColorSwitcher` won't be created at all.

As mentioned earlier, if such approach is taken with `ListViewRenderer`, code won't work at all! Swipe detection logic for pull-to-refresh won't be created and, what's worse, running such code will crash the application since the expected swipe control is not created but is nonetheless accessed.

## Proposed solution

Proposed solution is rather simple: instead of creating controls in `OnElementChanged` methods, add the following method to the `ViewRenderer<,>` class:

```csharp
public abstract TNativeView CreateNativeControl();
```

There is already such method in `Xamarin.Forms.Platform.Android.AppCompat` namespace in a special class only used in that namespace:

```csharp
public abstract class ViewRenderer<TView, TControl>
                       : Android.ViewRenderer<TView, TControl>
                         where TView : View
                         where TControl : global::Android.Views.View
{
    protected abstract TControl CreateNativeControl();
}
```


### Applying proposed solution in `ButtonRenderer` on Android

First, implement the new method in the renderer:

```csharp
protected override AButton CreateNativeControl() => new AButton(Context);
```

Then change the snippet in the existing `OnElementChanged` override:

```csharp
if (button == null)
{
	//button = new AButton(Context);
	button = CreateNativeControl();
	button.SetOnClickListener(ButtonClickListener.Instance.Value);
	button.Tag = this;
	SetNativeControl(button);
	_textColorSwitcher = new TextColorSwitcher(button.TextColors);
	button.AddOnAttachStateChangeListener(this);
}
```

## Detailed design

This approach requires fixing all renderers to use the factory method instead of creating controls inside the `OnElementChanged` method. It is a simple extraction and no additional code or logic needs to be added, it is just that all control creation has to happen in the factory method instead of inside `OnElementChanged`.

This affects all renderers based on `ViewRenderer<TView, TNativeView>` on all platforms.

## Impact on existing code

There is no functional impact in applications using Xamarin.Forms.

## Alternatives considered

Alternative is to completely reimplement renderers with all internal helpers reimplemented again since they are unavailable to user.

-------------------------------------------------------------------------------

# Rationale

On [Date], the core team decided to **(TBD)** this proposal.
When the core team makes a decision regarding this proposal,
their rationale for the decision will be written here.