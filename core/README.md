# Kau :core

> The framework of everything

## Contents

* [KPrefs](#kprefs)
* [Changelog XML](#changelog)
* [Ripple Canvas](#ripple-canvas)
* [Timber Logger](#timber-logger)
* [Email Builder](#email-builder)
* [Extensions](#extensions)

<a name="kprefs"></a>
## KPrefs

A typical SharedPreference contains items that look like so:

```Java
class MyPrefs {
    public static final String TEXT_COLOR = "TEXT_COLOR";

    private static SharedPreference prefs = ...

    public static void setTextColor(int color) {
        prefs.edit().putInt(TEXT_COLOR, color).apply();
    }

    public static int getTextColor() {
        prefs.getInt(TEXT_COLOR, Color.WHITE);
    }
}
```
  
KPrefs greatly simplifies it by using Kotlin's object pattern and delegates to achieve the following:

```Kotlin
object MyPrefs : KPref() {
    var textColor: Int by kpref("TEXT_COLOR", Color.WHITE)
    var bgColor: Int by kpref("BG_COLOR", Color.BLACK)
    var isFirstLaunch: Boolean by kpref("IS_FIRST_LAUNCH", true)
    ...
}
```

By using `KPrefSample.textColor = Color.RED` or `textView.setTextColor(KPrefSample.textColor)` we can effectively set and save the value.

The values are retrieved lazily and are only done so once; future retrievals will be done with a local value, and updating a preference will both save it in the SharedPreference and update it locally.

The object inherits the initializer method `fun initialize(c: Context, preferenceName: String)`, which must be invoked (preferably in the Application class) before using a preference.

There is also a `reset()` method to clear the local values and have them retrieve from the SharedPreference again.

In shared preferences, we often require a boolean that returns true once, so we can use it to showcase views or display prompts on the first load.
Kpref supports special preferences like these through the `KPrefSingleDelgate`

It can be used in a KPref like so:

```Kotlin
object MyPrefs : KPref() {
    val displayHelper: Boolean by kprefSingle("KEY_HERE")
    ...
}
```

Notice that it is a `val` and takes no default. It will return true the first time and false for all subsequent calls.

<a name="changelog"></a>
## Changelog XML

Create an xml resource with the following structure:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <version title="v0.1" />
    <item text="Initial Changelog" />
    <item text="Bullet point here" />
    <item text="More points" />
    <item text="" /> <!-- this one is empty and therefore ignored -->
</resources>
```

And show it with `context.showChangelog(@XmlRes xmlRes: Int)`
There is an optional `customize` argument to modify the builder before showing the changelog.

As mentioned, blank items will be ignored, so feel free to create a bunch of empty lines to facilitate updating the items in the future.

<a name="ripple-canvas"></a>
## Ripple Canvas

Ripple canvas provides a way to create simultaneous ripples against a background color. 
They can be used as transitions, or as a toolbar background to replicate the look for [Google Calendar](https://stackoverflow.com/questions/27872324/how-can-i-animate-the-color-change-of-the-statusbar-and-toolbar-like-the-new-ca)

![KPref Accent Gif](https://github.com/AllanWang/Storage-Hub/blob/master/kau/kau_kpref_accent.gif)

Many ripples can be stacked on top of each other to run at the same time from different locations.
The canvas also supports color fading and direct color setting so it can effectively replace any background.

<a name="timber-logger"></a>
## Timber Logger

[Timber](https://github.com/JakeWharton/timber)'s DebugTree uses the tag to specify the current class that is being logged. 
To add the tag directly in the message, create an object that extends the TimberLogger class with the tag name as the argument.
Along with the timber methods (`v`, `i`, `d`, `e`), Timber Logger also supports `eThrow` to wrap a String in a throwable

<a name="email-builder"></a>
## Email Builder

Easily send an email through `Context.sendEmail`. 
Include your email and subject, along with other optional configurations such as retrieving device info.

<a name="extensions"></a>
## Extension Functions

> "[Extensions](https://kotlinlang.org/docs/reference/extensions.html) provide the ability to extend a class with new functionality without having to inherit from the class"
Note that since KAU depends on [ANKO](https://github.com/Kotlin/anko), all of the extensions in its core package is also in KAU.

### AnimUtils
> Extends View
* Fade In/Fade Out/Circle Reveal with callbacks
* Switch texts in a TextView with a fade transition

### ColorUtils
> Extends Int
* Check if color is dark or light
* Check if color is visible against another color
* Convert color to HSV (float[]) or hex (String)
* Get the disabled color of a theme
* Adjust alpha
> Extends String
* Parses color; adds onto the original parser by supporting #AAA values
> Extends View
* Various tinting for different views; taken from [MDTintHelper](https://github.com/afollestad/material-dialogs/blob/master/core/src/main/java/com/afollestad/materialdialogs/internal/MDTintHelper.java)

### ContextUtils
> Extends Activity
* Restart an activity
> Extends Context
* Start Activity using the class, with optional intents and stack clearing
* Create a toast directly
* Get resource values through `.color(id)`, `.dimen(id)`, `.drawable(id)`, `.integer(id)`, `.string(id)`
* Get attribute values through resolve methods
* Show a Changelog by parsing an xml resource
* Check if network is available

### IIconUtils
> Extends [IIcon](https://github.com/mikepenz/Android-Iconics)
* `toDrawable` method that only requires a context; defaults to a white icon of size 24dp and uses a ColorStateList to allow for dimming

### Utils [Misc]
> Extends Int
* dpToPx & pxToDp conversions
* Check sdk version
* Check if app is installed

### ViewUtils
> Extends View
* `visible()`, `invisible()`, `gone()`, `isVisible()`, `isInvisible()`, `isGone()` methods
* matchParent method to set the layout params to match_parent
* Create snackbar directly
* Set IIcon into ImageView directly