---
title: "Colors and Styling for Visual Studio | Microsoft Docs"
ms.custom: ""
ms.date: 11/15/2016
ms.prod: "visual-studio-dev14"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "vs-ide-sdk"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 0e384ea1-4d9e-4307-8884-6e183900732c
caps.latest.revision: 7
ms.author: "gregvanl"
manager: "ghogen"
---
# Colors and Styling for Visual Studio
[!INCLUDE[vs2017banner](../../includes/vs2017banner.md)]

## Using color in Visual Studio  
 In Visual Studio, color is used primarily as a communication tool, not just as decoration. Use color minimally and reserve it for situations where you want to:  
  
- Communicate meaning or affiliation (for example, platform or language modifiers)  
  
- Attract attention (for example, indicating a status change)  
  
- Improve readability and provide landmarks for navigating the UI  
  
- Increase desirability  
  
  Several options exist for assigning colors to UI elements in Visual Studio. Sometimes it can be difficult to figure out which option you’re supposed to use, or how to use it correctly. This topic will help you:  
  
1. Understand the different services and systems used to define colors in Visual Studio.  
  
2. Select the correct option for a given element.  
  
3. Correctly use the option you have chosen.  
  
   **IMPORTANT:** Never hardcode hex, RGB, or system colors to your UI elements. Using the services allows for flexibility in tuning hue. Additionally, without the service, you will not be able to take advantage of the theme-switching capabilities of the [The VSColor Service](../../extensibility/ux-guidelines/colors-and-styling-for-visual-studio.md#BKMK_TheVSColorService).  
  
### Methods for assigning color to Visual Studio interface elements  
 Choose the method best suited to your UI elements.  
  
|Your UI|Method|What are they?|  
|-------------|------------|--------------------|  
|You have embedded or standalone dialog boxes.|**System colors**|System names that allow the operating system to define the color and appearance of the UI elements, such as for common dialog controls.|  
|You have custom UI that you want to be consistent with the overall VS environment and you have UI elements that match the category and semantic meaning of the shared tokens.|**Common shared colors**|Existing predefined color token names for specific UI elements|  
|You have an individual feature or group of features and there is no shared color for similar elements.|**Custom colors**|Color token names that are specific to an area and not meant to be shared with other UI|  
|You want to allow the end user to customize UI or content (for example, for text editors or specialized designer windows).|**End-user customization**<br /><br /> **(Tools > Options dialog)**|Settings defined in the “Fonts and Colors” page of the **Tools > Options** dialog or a specialized page specific to one UI feature.|  
 
  
### Visual Studio themes  
 Visual Studio features three different color themes: light, dark, and blue . It also detects High Contrast mode, which is a system-wide color theme designed for accessibility.  
  
 Users are prompted to select a theme during their first use of Visual Studio and are able to switch themes later by going to **Tools > Options > Environment > General** and choosing a new theme from the “color theme” dropdown menu.  
  
 Users can also use Control Panel to switch their entire systems into one of several High Contrast themes. If a user selects a High Contrast theme, then the Visual Studio color theme selector no longer affects colors in Visual Studio, although any theme changes are saved for when the user exits High Contrast mode. For more information about High Contrast mode, see [Choosing High Contrast colors](../../extensibility/ux-guidelines/colors-and-styling-for-visual-studio.md#BKMK_ChoosingHighContrastColors).  
  
### The VSColor service  
 Visual Studio provides an environment color service, known as the VSColor service, which allows you to bind the color values of your UI elements to a named entry containing color values for each Visual Studio theme. This ensures that your colors will automatically change to reflect the current user-selected theme or system High Contrast mode. Use of the service means that the implementation of all theme-related color changes is handled in one place, and if you are using common shared colors from the service, your UI will automatically reflect new themes in future versions of Visual Studio.  
  
### Implementation  
 The Visual Studio source code includes several package definition files that contain lists of token names and the respective color values for each theme. The color service reads the VSColors defined in these package definition files. These colors are referenced in XAML markup or in code and then loaded through either the **IVsUIShell5.GetThemedColor** method or a DynamicResource mapping.  
  
### System colors  
 Common controls reference the system colors by default. If you want your UI to use system colors, such as when you are creating an embedded or standalone dialog, you don’t need to do anything.  
  
### Common shared colors in the VSColor service  
 Your interface elements should reflect the overall Visual Studio environment. By reusing the common shared colors that are appropriate for the UI component you’re designing, you ensure that your interface is consistent with other Visual Studio interfaces, and that your colors will update automatically when themes are added or updated.  
  
 Before using common shared colors, make sure that you understand how to use them correctly. Incorrect use of common shared colors might result in an inconsistent, frustrating, or confusing experience for your users.  
  
### User-customizable colors  
 See: [Exposing colors for end users](../../extensibility/ux-guidelines/colors-and-styling-for-visual-studio.md#BKMK_ExposingColorsForEndUsers)  
  
 Sometimes, you will want to allow the end user to customize your UI, such as when you are creating a code editor or design surface. Customizable UI components are found in the **Fonts and Colors** section of the **Tools > Options** dialog, where users can choose to change the foreground color, background color, or both.  
  
 ![Tools &#62; Options dialog in Visual Studio](../../extensibility/ux-guidelines/media/0301-a-toolsoptionsdialog.png "0301-a_ToolsOptionsDialog")  
  
 **Tools>Options dialog**  
  
 
##  <a name="BKMK_TheVSColorService"></a> The VSColor Service  
 Visual Studio provides an environment color service, also called the VSColor service or the shell color service. This service allows you to bind the color values of your UI elements to a name-value color set containing colors for each theme. The VSColor service must be used for all UI elements, so that colors automatically change to reflect the current user-selected theme, and so that UI bound to the environment color service will integrate with new themes in future versions of Visual Studio.  
  
### How the service works  
 The environment color service reads VSColors defined in the .pkgdef for the UI component. These VSColors are then referenced in XAML markup or code and are loaded through either the **IVsUIShell5.GetThemedColor** or a DynamicResource mapping.  
  
 ![Environment color service architecture](../../extensibility/ux-guidelines/media/0302-a-environmentcolorservicearchitecture.png "0302-a_EnvironmentColorServiceArchitecture")  
  
 **Environment color service architecture**  
  
### Accessing the service  
 There are several different ways to access the VSColor service, depending on what kind of color tokens you are using and what kind of code you have.  
  
#### Predefined environment colors  
  
##### From native code  
 The shell provides a service that gives access to the COLORREF of the colors. The service/interface is:  
  
```  
IVsUIShell2::GetVSSysColorEx(VSSYSCOLOR dwSysColIndex, DWORD *pdwRGBval)  
```  
  
 In the file VSShell80.idl, the enumeration **__VSSYSCOLOREX** has shell color constants. To use it, pass in as the index value either one of the values from the enum __VSSYSCOLOREX documented in MSDN or a regular index number that the Windows system API, **GetSysColor**, accepts. Doing this gets back the RGB value of the color that should be used in the second parameter.  
  
 If storing a pen or brush with a new color, you must AdviseBroadcastMessages (off of the Visual Studio shell) and listen for WM_SYSCOLORCHANGE and WM_THEMECHANGED messages.  
  
```  
// To access the color service in native code, you'll make a call that resembles this:  
pUIShell2->GetVSSysColorEx(VSCOLOR_COLOR_NAME, &rgbLOCAL_COLOR);  
  
```  
  
 **NOTE:** The COLORREF values returned by **GetVSSysColorEx()** contain just R,G,B components of a theme color. If a theme entry uses transparency, the alpha-channel value is discarded before returning. Therefore, if the environment color of interest needs to be used in a place where transparency channel is important, you should use IVsUIShell5.GetThemedColor instead of IVsUIShell2::GetVSSysColorEx, as described later in this topic.  
  
##### From managed code  
 Accessing the VSColor service through native code is fairly straightforward. If you are working through managed code, however, determining how to use the service can be tricky. With that in mind, here is a C# code snippet demonstrating this process:  
  
```  
private void VSColorPaint(object sender, System.Windows.Forms.PaintEventArgs e)  
{  
    //getIVSUIShell2  
    IVsUIShell2 uiShell2 = Package.GetService(typeof(SVsUIShell)) as IVsUIShell2;  
    Debug.Assert (uiShell2 != null, "failed to get IVsUIShell2");  
  
    if (uiShell2 != null)  
    {  
        //get the COLORREF structure  
        uint win32Color;  
        uiShell.GetVSSysColorEx(VSSYSCOLOREX.VSCOLOR_SMARTTAG_HOVER_FILL, out win32Color);  
  
        //translate it to a managed Color structure  
        Color myColor = ColorTranslator.FromWin32((int)win32Color);  
        //use it  
        e.Graphics.FillRectangle(new SolidBrush(myColor), 0, 0, 100, 100);  
    }  
}  
```  
  
 If you are working in Visual Basic, use:  
  
```  
Dim myColor As Color = ColorTranslator.FromWin32((Integer)win32Color)  
```  
  
##### From WPF UI  
 You can bind to Visual Studio colors through values exported into the Application's ResourceDictionary. Below is an example of using resources from the color table as well as binding to the environment font data in XAML.  
  
```  
<Style TargetType="{x:Type Button}">  
    <Setter Property="TextBlock.FontFamily"  
            Value="{DynamicResource VsFont.EnvironmentFontFamily}" />  
    <Setter Property="TextBlock.FontSize"  
            Value="{DynamicResource VsFont.EnvironmentFontSize}" />  
    <Setter Property="Background"  
            Value="{DynamicResource VsBrush.EnvironmentBackgroundGradient}" />  
</Style>  
```  
  
#### Helper classes and methods for managed code  
 For managed code, the shell’s Managed Package Framework library (Microsoft.VisualStudio.Shell.12.0.dll) contains a couple of helper classes facilitating the use of themed colors.  
  
 The helper methods in the **Microsoft.VisualStudio.Shell.VsColors** class in MPF include **GetThemedGDIColor()** and **GetThemedWPFColor()**. Those helper methods return the color value of a theme entry as System.Drawing.Color or System.Windows.Media.Color, to be used in WinForms or WPF UI.  
  
```  
IVsUIShell5 shell5;  
Button button = new Button();  
button.BackColor = GetThemedGDIColor(shell5, SolutionExplorerColors.SelectedItemBrushKey);  
button.ForeColor = GetThemedGDIColor(shell5, SolutionExplorerColors.SelectedItemTextBrushKey);  
  
/// <summary>  
/// Gets a System.Drawing.Color value from the current theme for the given color key.  
/// </summary>  
/// <param name="vsUIShell">The IVsUIShell5 service, used to get the color's value.</param>  
/// <param name="themeResourceKey">The key to find the color for.</param>  
/// <returns>The current theme's value of the named color.</returns>  
public static System.Drawing.Color GetThemedGDIColor(this IVsUIShell5 vsUIShell, ThemeResourceKey themeResourceKey)  
{  
   Validate.IsNotNull(vsUIShell, "vsUIShell");  
   Validate.IsNotNull(themeResourceKey, "themeResourceKey");  
  
   byte[] colorComponents = GetThemedColorRgba(vsUIShell, themeResourceKey);  
  
   // Note: The Win32 color we get back from IVsUIShell5.GetThemedColor is ABGR  
   return System.Drawing.Color.FromArgb(colorComponents[3], colorComponents[0], colorComponents[1], colorComponents[2]);  
}  
  
private static byte[] GetThemedColorRgba(IVsUIShell5 vsUIShell, ThemeResourceKey themeResourceKey)  
{  
   Guid category = themeResourceKey.Category;  
   __THEMEDCOLORTYPE colorType = __THEMEDCOLORTYPE.TCT_Foreground  
   if (themeResourceKey.KeyType == ThemeResourceKeyType.BackgroundColor || themeResourceKey.KeyType == ThemeResourceKeyType.BackgroundBrush)  
   {  
      colorType = __THEMEDCOLORTYPE.TCT_Background;  
   }  
  
   // This call will throw an exception if the color is not found  
   uint rgbaColor = vsUIShell.GetThemedColor(ref category, themeResourceKey.Name, (uint)colorType);  
   return BitConverter.GetBytes(rgbaColor);  
}  
public static System.Windows.Media.Color GetThemedWPFColor(this IVsUIShell5 vsUIShell, ThemeResourceKey themeResourceKey)  
{  
   Validate.IsNotNull(vsUIShell, "vsUIShell");  
   Validate.IsNotNull(themeResourceKey, "themeResourceKey");  
  
   byte[] colorComponents = GetThemedColorComponents(vsUIShell, themeResourceKey);  
  
    return System.Windows.Media.Color.FromArgb(colorComponents[3], colorComponents[0], colorComponents[1], colorComponents[2]);  
}  
  
```  
  
 The class can also be used to obtain VSCOLOR identifiers for a given WPF color resource key, or vice versa.  
  
```  
public static string GetColorBaseKey(int vsSysColor);  
public static bool TryGetColorIDFromBaseKey(string baseKey, out int vsSysColor);  
```  
  
 The methods of **VsColors** class query the VSColor service to return the color value each time they are invoked. To obtain a color value as **System.Drawing.Color**, an alternative with better performance is to instead use the methods of the **Microsoft.VisualStudio.PlatformUI.VSThemeColor** class, which caches the color values obtained from the VSColor service. The class subscribes internally to shell broadcast messages events, and discards the cached value when a theme changing event occurs. Also, the class provides a .NET-friendly event to subscribe to theme changes. Use the **ThemeChanged** event to add a new handler, and use the **GetThemedColor()** method to obtain color values for the **ThemeResourceKeys** of interest. A sample code could look like this:  
  
```  
public MyWindowPanel()  
{  
    InitializeComponent();  
  
    // Subscribe to theme changes events so we can refresh the colors  
    VSColorTheme.ThemeChanged += VSColorTheme_ThemeChanged;  
  
    RefreshColors();  
}  
  
private void VSColorTheme_ThemeChanged(ThemeChangedEventArgs e)  
{  
    RefreshColors();  
  
    // Also post a message to all the children so they can apply the current theme appropriately  
    foreach (System.Windows.Forms.Control child in this.Controls)  
    {  
        NativeMethods.SendMessage(child.Handle, e.Message, IntPtr.Zero, IntPtr.Zero);  
    }  
}  
  
private void RefreshColors()  
{  
    this.BackColor = VSColorTheme.GetThemedColor(EnvironmentColors.ToolWindowBackgroundColorKey);  
    this.ForeColor = VSColorTheme.GetThemedColor(EnvironmentColors.ToolWindowTextColorKey);  
}  
  
protected override void Dispose(bool disposing)  
{  
    if (disposing)  
    {  
        VSColorTheme.ThemeChanged -= this.VSColorTheme_ThemeChanged;  
        base.Dispose(disposing);}  
}  
```  
  
##  <a name="BKMK_ChoosingHighContrastColors"></a> Choosing High Contrast colors  
  
### Overview  
 Windows uses several high-contrast system-level themes that increase the color contrast of text, backgrounds, and images, making elements appear more distinct on the screen. For accessibility reasons, it is important that Visual Studio interface elements respond correctly when users switch to a High Contrast theme.  
  
 Only a handful of system colors can be used for High Contrast themes. When choosing your system color names, remember the following tips:  
  
1.  **Choose system colors that have the same semantic meaning** as the element that you are coloring. For instance, if you are choosing a high-contrast color for text within a window, use WindowText and not ControlText.  
  
2.  **Choose foreground/background pairs** together or you will not be confident that your color choice will work in all High Contrast themes.  
  
3.  **Determine which parts of your UI are the most important and ensure that content areas will stand out.** You will lose a lot of detail that subtle differences in color hue would normally distinguish, so the use of strong border colors is common to define content areas, because there are no color variants for different content areas.  
  
### System color set  
 The table at [WPF Team Blog: SystemColors Reference](http://blogs.msdn.com/b/wpf/archive/2010/11/30/systemcolors-reference.aspx) indicates the complete set of system color names, and the corresponding hues displayed in each theme.  
  
 When applying this limited set of colors to your UI, *it is expected that you will lose subtle details that were present in the “normal” themes*. Here is an example of UI with subtle gray colors that are used to distinguish areas within a tool window. When paired with the same window displayed in High Contrast mode, you can see that all the backgrounds are the same hue and the borders of those areas are indicated by border alone:  
  
 ![Properties window](../../extensibility/ux-guidelines/media/030303-a-propertieswindow.png "030303-a_PropertiesWindow")  
  
 **Example of how subtle details are lost when in High Contrast**  
  
#### Choosing text colors in an editor  
 Colorized text is used in an editor or on a design surface to indicate meaning, such as allowing for easy identification of groups of similar items. In a High Contrast theme, however, you do not have the ability to differentiate between more than three text colors. WindowText, GrayText and HotTrackText are the only colors available on WindowBackground surfaces. Since you cannot use more than three colors, carefully choose the most important differences that you want to display when in High Contrast mode.  
  
 Hues for each of the token names allowed on an editor surface, as they appear in each High Contrast theme:  
  
 ![High Contrast editor comparison](../../extensibility/ux-guidelines/media/030303-b-hceditorcomparison.png "030303-b_HCEditorComparison")  
  
 **High Contrast editor comparison**  
  
 Examples of the editor surface in the Blue theme:  
  
 ![Editor in Blue theme](../../extensibility/ux-guidelines/media/030303-c-editorblue.png "030303-c_EditorBlue")  
  
 **Editor in Blue theme**  
  
 ![Editor in High Contrast theme](../../extensibility/ux-guidelines/media/030303-d-editorhc1.png "030303-d_EditorHC1")  
  
 **Editor in High Contrast #1 theme**  
  
### Usage patterns  
 Many common UI elements already have high-contrast colors defined. You can reference these usage patterns when choosing your own system color names, so that your UI elements are consistent with similar components.  
  
|System Color|Usage|  
|------------------|-----------|  
|ActiveCaption|-   Active IDE and rafted window button glyphs on hover and press<br />-   Title bar background for IDE and rafted windows<br />-   Default status bar background|  
|ActiveCaptionText|-   Active IDE and rafted windows for title bar foreground (text and glyphs)<br />-   Background and border of active window buttons on hover and press|  
|Control|-   Combo box, dropdown list, and search control default and disabled background, including dropdown button<br />-   Dock target button background<br />-   Command bar background<br />-   Tool window background|  
|ControlDark|-   IDE background<br />-   Menu and command bar separators<br />-   Command bar border<br />-   Menu shadows<br />-   Tool window tab default and hover border and separator<br />-   Document well overflow button background<br />-   Dock target glyph border|  
|ControlDarkDark|-   Unfocused, selected document tab window|  
|ControlLight|-   Auto-hide tab border<br />-   Combo box and drop-down list border<br />-   Dock target background and border|  
|ControlLightLight|-   Selected, focused provisional border|  
|ControlText|-   Combo box and drop-down list glyph<br />-   Tool window unselected tab text|  
|GrayText|-   Combo box and dropdown list disabled border, dropdown glyph, text, and menu item text<br />-   Disabled menu text<br />-   Search control 'search options' header text<br />-   Search control section separator|  
|Highlight|-   All hover and pressed backgrounds and borders, except combo box dropdown button background and document well overflow button border<br />-   Selected item backgrounds|  
|HighlightText|-   All hover and pressed foregrounds (text and glyphs)<br />-   Focused tool window and document tab window control foreground<br />-   Focused tool window title bar border<br />-   Focused, selected provisional tab foreground<br />-   Document well overflow button border on hover and press<br />-   Selected icon border|  
|HotTrack|-   Scrollbar thumb background and border on press<br />-   Scrollbar arrow glyph on press|  
|InactiveCaption|-   Inactive IDE and rafted window button glyphs on hover<br />-   Title bar background for IDE and rafted windows<br />-   Disabled search control background|  
|InactiveCaptionText|-   Inactive IDE and rafted windows title bar foreground (text and glyphs)<br />-   Inactive window buttons background and border on hover<br />-   Unfocused tool window button background and border<br />-   Disabled search control foreground|  
|Menu|-   Dropdown menu background<br />-   Checked and disabled checkmark background|  
|MenuText|-   Dropdown menu border<br />-   Checkmark check<br />-   Menu glyphs<br />-   Drop-down menu text<br />-   Selected icon border|  
|Scrollbar|-   Scrollbar and scrollbar arrow background, all states|  
|Window|-   Auto-hide tab background<br />-   Menu bar and command shelf background<br />-   Unfocused or unselected document window tab background and document border, for both open and provisional tabs<br />-   Unfocused tool window title bar background<br />-   Tool window tab background, both selected and unselected|  
|WindowFrame|-   IDE border|  
|WindowText|-   Auto-hide tab foreground<br />-   Selected tool window tab foreground<br />-   Unfocused document window tab and unfocused or unselected provisional tab foreground<br />-   Tree view default foreground and hover over unselected glyph<br />-   Tool window selected tab border<br />-   Scrollbar thumb background, border, and glyph|  
  
##  <a name="BKMK_ExposingColorsForEndUsers"></a> Exposing colors for end users  
  
### Overview  
 Sometimes you will want to allow the end user to customize your UI, such as when you are creating a code editor or design surface. The most common way to do this is by using the **Tools > Options** dialog. Unless you have highly specialized UI that requires special controls, the easiest way to present the customization is through the **Fonts and Colors** page within the **Environment** section of the dialog. For each element that you expose for customization, the user can choose to change the foreground color, background color, or both.  
  
### Building a VSPackage for your customizable colors  
 A VSPackage can control the fonts and colors through custom categories and display items on the Fonts and Colors property page. When using this mechanism, VSPackages must implement the [IVsFontAndColorDefaultsProvider](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolordefaultsprovider.aspx) interface and its associated interfaces.  
  
 In principle, this mechanism can be used to modify all existing display items and the categories that contain them. However, it should not be used to modify the Text Editor category or its display items. For more information on the Text Editor category, see [Font and Color Overview](https://msdn.microsoft.com/library/bb165065.aspx).  
  
 To implement custom categories or display Items, a VSPackage must:  
  
-   **Create or identify categories in the registry.** The IDE's implementation of the **Fonts and Colors** property page uses this information to correctly query for the service supporting a given category.  
  
-   **Create or identify groups in the registry (optional).** It might be useful to define a group, which represents the union of two or more categories. If a group is defined, the IDE automatically merges subcategories and distributes display items within the group.  
  
-   **Implement IDE support.**  
  
-   **Handle font and color changes.**  
  
#### To create or identify categories  
 Construct a special type of category registry entry under [HKLM\SOFTWARE\Microsoft \Visual Studio\\<Visual Studio version\>\FontAndColors\\<Category\>]. \<Category> is the non-localized name of the Category.  
  
 Populate the registry with two values:  
  
|Name|Type|Data|Description|  
|----------|----------|----------|-----------------|  
|Category|REG_SZ|GUID|A GUID created to identify the category|  
|Package|REG_SZ|GUID|The GUID of the VSPackage service that supports the category|  
  
 The service specified in the registry must provide an implementation of [IVsFontAndColorDefaults](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolordefaults.aspx) for the corresponding category.  
  
#### To create or identify groups  
 Construct a special type of category registry entry under [HKLM\SOFTWARE\Microsoft \Visual Studio\\<Visual Studio version\>\FontAndColors\\<group\>]. \<group> is the non-localized name of the group.  
  
 Populate the registry with two values:  
  
|Name|Type|Data|Description|  
|----------|----------|----------|-----------------|  
|Category|REG_SZ|GUID|A GUID created to identify the category|  
|Package|REG_SZ|GUID|The GUID of the VSPackage service that supports the category|  
  
 The service specified in the registry must provide an implementation of **T:Microsoft.VisualStudio.Shell.Interop.IVsFontAndColorGroup** for the corresponding group.  
  
 ![IVsFontAndColorGroup](../../extensibility/ux-guidelines/media/0304-a-fontandcolorgroup.png "0304-a_FontAndColorGroup")  
  
### To implement IDE support  
 Implement [GetObject](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolordefaultsprovider.getobject.aspx), which returns either an [IVsFontAndColorDefaults](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolordefaults.aspx) interface or an **T:Microsoft.VisualStudio.Shell.Interop.IVsFontAndColorGroup** interface to the IDE for each category or group GUID supplied.  
  
 For every category it supports, a VSPackage implements a separate instance of the [IVsFontAndColorDefaults](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolordefaults.aspx) interface.  
  
 The methods implemented through [IVsFontAndColorDefaults](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolordefaults.aspx) must provide the IDE with:  
  
- Lists of display items in the category  
  
- Localizable names for display items  
  
- Display information for each member of the category  
  
  **NOTE:** Every category must contain at least one display item.  
  
  The IDE uses the **T:Microsoft.VisualStudio.Shell.Interop.IVsFontAndColorGroup** interface to define a union of several categories.  
  
  Its implementation provides the IDE with:  
  
- A list of the Categories that make up a given group  
  
- Access to instances of [IVsFontAndColorDefaults](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolordefaults.aspx) supporting each Category within the group  
  
- Localizable group names  
  
#### Updating the IDE  
 The IDE caches information about Font and Color settings. Therefore, after any modification of the IDE Font and Color configuration, ensuring that the cache is up to date is a best practice.  
  
 Updating the cache is done through the [IvsFontAndColorCacheManager](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolorcachemanager.aspx) interface and can be performed globally or just on selected items.  
  
### Handling font and color changes  
 To properly support the colorization of text that a VSPackage displays, the colorization service supporting the VSPackage must respond to the user-initiated changes made through the Fonts and Colors properties page.  
  
 To do this, a VSPackage must:  
  
- **handle IDE-generated events** by implementing the [IVsFontAndColorEvents](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolorevents.aspx) interface. The IDE calls the appropriate method following user modifications of the Fonts and Colors page. For example, it calls the [OnFontChanged](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolorevents.onfontchanged.aspx) method if a new font is selected.  
  
  **OR**  
  
- **poll the IDE for changes**. This can be done through the system-implemented [IVsFontAndColorStorage](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolorstorage.aspx) interface. Although primarily for support of persistence, the [GetItem](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolorstorage.getitem.aspx) method can obtain font and color information for Display Items. For more information on font and color settings, see the MSDN article [Accessing Stored Font and Color Settings](https://msdn.microsoft.com/library/bb166382.aspx).  
  
  **NOTE:** To ensure that polling results are correct, use the [IVsFontAndColorCacheManager](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolorcachemanager.aspx) interface to determine if a cache flush and update are needed prior to calling the retrieval methods of the [IVsFontAndColorStorage](https://msdn.microsoft.com/library/microsoft.visualstudio.shell.interop.ivsfontandcolorstorage.aspx) interface.  
  
#### Registering custom font and color Category without implementing interfaces  
 The following code example demonstrates how to register the custom font and color Category without implementing interfaces:  
  
```xml  
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\VisualStudio\8.0Exp\FontAndColors\CSharp Tool Window]  
"Package"="{F5E7E71D-1401-11D1-883B-0000F87579D2}"  
"Category"="{9FF46859-A47E-47bf-8AC5-EC3DBE69D1FE}"  
"ToolWindowPackage"="{7259e420-6241-4e0d-b535-5b820671d183}"  
  
    "NameID"=dword:00000064  
```  
  
 **NOTE:**  
  
-   "NameID" = the resource ID of the localized category name in your package  
  
-   "ToolWindowPackage" = Package GUID  
  
-   "Category"="{9FF46859-A47E-47bf-8AC5-EC3DBE69D1FE}" is just an example and the actual value can be a new GUID provided by the implementer.  
  
### Set the Font and Color property category GUID  
 The code example below demonstrates setting Category GUIDs.  
  
```cs  
// m_pView is your IVsTextView  
IVsTextEditorPropertyCategoryContainer spPropCatContainer =  
(IVsTextEditorPropertyCategoryContainer)m_pView;  
if (spPropCatContainer != null)  
{  
  IVsTextEditorPropertyContainer spPropContainer;  
  Guid GUID_EditPropCategory_View_MasterSettings =  
  new Guid("{D1756E7C-B7FD-49a8-B48E-87B14A55655A}");  
  hr = spPropCatContainer.GetPropertyCategory(  
  ref GUID_EditPropCategory_View_MasterSettings, out spPropContainer);  
  if(hr == 0)  
  {  
    hr = spPropContainer.SetProperty(VSEDITPROPID.VSEDITPROPID_ViewGeneral_FontCategory, catGUID);  
    hr = spPropContainer.SetProperty(VSEDITPROPID.VSEDITPROPID_ViewGeneral_ColorCategory, catGUID);  
  }  
}  
```  
