# A multiline extended tooltip control

A drop-in multiline extendable tooltip control





![sample image 1](ToolTipEx\ToolTipEx1.gif)  ![sample image 2](ToolTipEx\ToolTipEx2.gif) 

## COXToolTipCtrl Overview

###### ![Dundas](Dundas.gif)This class is a free sample from Dundas Software's Ultimate Toolbox. Copyright © Dundas Software Ltd. 1997-1999, All Rights Reserved

`COXToolTipCtrl` is an extended tooltip control that allows multiline tooltips, plus extended tooltip text. Extended tooltip text is extra text that is displayed if the user clicks on the tooltip window. If the tooltip contains extended text (as well as a standard tooltip string) then the info window will contain a small arrow that prompts the user to click on the window. Once the window is clicked, the extended text is shown. If the window is clicked again then the window reduces to showing just the standard text. 

The maximum width of the tooltips can be specified, and if the info text is too big to fit within these bounds then the text will be wrapped over multiple lines. The control also allows you to specify a different text and background colors for the tooltips, and the display font can also be changed. 

This class is a direct replacement for the `CToolTipCtrl` class. It incorporates the entire API of the standard `CToolTipCtrl`, and introduces new features not found in the standard tooltip. 

The control is used just like any other tooltip control. To use the tool simply call `Create(...)` and specify the parent window of the tool, then add tools to the control using the `AddTool(...)` member function. eg. (In a formview or dialog) 

```cpp
tooltip.Create(this)
tooltip.AddTool(GetDlgItem(IDC_CONTROL), 
               _T("Tooltip text\rThis is the extended\ntooltip text"));
```

where `ID_CONTROL` is the ID of a control. 

To specify **extended text** for a tooltip, simply append a '\r' after your tooltip text, and then append the extended tooltip info. 

As with the standard tooltip control you can specify the actual text for the tool at creation time (as shown above), or you can specify the `LPSTR_TEXTCALLBACK` value and provide a `TTN_NEEDTEXT` handler to return the text dynamically at runtime. 

To handle the `TTN_NEEDTEXT` message, you will need to add a message handler in the parent window, and an entry in the message map, eg. in you view or form 

```cpp
BEGIN_MESSAGE_MAP(CMyDlg, CDialog)
... 
ON_NOTIFY_EX( TTN_NEEDTEXT, 0, OnToolTipNotify)
END_MESSAGE_MAP()

BOOL CMyDlg::OnInitDialog()
{
    CDialog::OnInitDialog();
    tooltip.Create(this);
    tooltip.AddTool(GetDlgItem(IDC_CONTROL), LPSTR_TEXTCALLBACK);
    ...
}

BOOL CMyDlg::OnToolTipNotify(UINT id, NMHDR* pNMHDR, LRESULT* pResult)
{ 
    TOOLTIPTEXT *pTTT = (TOOLTIPTEXT *)pNMHDR; 
    UINT nID = pNMHDR->idFrom;

    if (nID == IDC_CONTROL) // Fill in the text buffer
    {
        _tcscpy(pTTT->szText, _T("Tooltip text\rExtended tooltip text"));
        return TRUE;
    }

    return FALSE;
}
```

You can also supply text two alternate ways, either by supplying a string resource 

```cpp
pTTT->lpszText = MAKEINTRESOURCE(nID);
pTTT->hinst = AfxGetResourceHandle(); 
return TRUE;
```

or by supplying a pointer to the text: 

```cpp
pTTT->lpszText = _T("Tooltip text\rExtended tooltip text");
return TRUE;
```

Newline characters ('\n') can be embedded anywhere within the text or extended text to produce a multiline tooltip. If the width of the tooltip window is specified using `SetMaxTipWidth()` then the tooltip text will be wrapped to this length, and if necessary displayed on more than one line. 

To change the font of the tooltips simply use the `SetFont()` member function. 

The `GetToolInfo/SetToolInfo` functions, and the `HitTest` functions are very similar to the `CToolTipCtrl` versions except that they use a `OXTOOLINFO` structure instead of a `TOOLINFO` structure. This structure is defined as 

```cpp
struct 
OXTOOLINFO : public TOOLINFO {
#if
(_WIN32_IE <  0x0300)
    LPARAM lParam; //Application defined value that is associated with 
                   //the tool
#endif
    int nWidth; //Width of box, or 0 for default
    COLORREF clrTextColor; //text color
    COLORREF clrBackColor; //background color
}
```

and so is very similar to the standard `TOOLINFO`, and is used in the same way, with the exception that the uFlags member is not (yet) used. 

To change the color of an individual tip, use the `GetToolInfo/SetToolInfo` functions 

```cpp
OXTOOLINFO ToolInfo;
if (m_toolTip.GetToolInfo(ToolInfo, GetDlgItem(IDC_CONTROL)))
{
    ToolInfo.clrBackColor = RGB(255, 255, 255);
    ToolInfo.clrTextColor = RGB( 0, 0, 255);
    m_toolTip.SetToolInfo(&ToolInfo);
}
```
