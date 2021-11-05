---
layout: post
title:  "Custom Fonts in Android"
date:   2021-11-05 10:21:22 -0700
categories: android fonts custom
---

This post walk through steps on how to create custom fonts in Android.

Android documentation for this here
https://jira.intuit.com/browse/ITDS-6180

In Android 8.0 (API Level 26), Android introduces support for Fonts in XML, which lets you use fonts as resources -- By the way, after years, I still hate the way Android structure their API Level and their versioning.  There's no way that you could map these things and remember which version of Android maps to which API level.  On top of that, all the internal codename that associated with it.

Steps to add and use custom font:

- Create a font resource directory.
- Add the font files to the font folder.
- Preview the font in the editor.
- Create a font family

What is a font-family?

- It is a set of font files along with its style and weight details. You can create a new font family as an xml resource, and access it as a single unit, instead of referencing each style and weight as separate resources.

Here's what the font resource file looks like:

```XML

<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android">
    <font
        android:fontStyle="normal"
        android:fontWeight="400"
        android:font="@font/lobster_regular" />
    <font
        android:fontStyle="italic"
        android:fontWeight="400"
        android:font="@font/lobster_italic" />
</font-family>

```

To add font in TextView

```XML
<TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:fontFamily="@font/lobster"/>
```        


To add font to styles

```XML

<style name="customfontstyle" parent="@android:style/TextAppearance.Small">
    <item name="android:fontFamily">@font/lobster</item>
</style>

```

Using the support library

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:app="http://schemas.android.com/apk/res-auto">
    <font app:fontStyle="normal" app:fontWeight="400" app:font="@font/myfont-Regular"/>
    <font app:fontStyle="italic" app:fontWeight="400" app:font="@font/myfont-Italic" />
</font-family>
```
