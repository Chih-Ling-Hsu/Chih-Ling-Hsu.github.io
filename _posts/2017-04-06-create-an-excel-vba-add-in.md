---
title: 'Create an Excel VBA Add-In'
layout: post
tags:
  - VBA
category: Programming
mathjax: false
---

An Excel Add-In is a file (usually with an .xla or .xll extension) that Excel can load when it starts up. The file contains code (VBA in the case of an .xla Add-In) that adds additional functionality to Excel, usually in the form of new functions.

Add-Ins provide an excellent way of increasing the power of Excel and they are the ideal vehicle for distributing your custom functions. Excel is shipped with a variety of Add-Ins ready for you to load and start using, and many third-party Add-Ins are available.

<!--more-->

## Setup
### Open VBA Editor

**Step 1. Open any Excel file**

**Step 2. With your new template open in Excel, on the `Developer` tab, click `Visual Basic`**

- Hot key - `Alt` + `F11`
- If you cannot find your `Developer` tab, follow the instructions at [this page](https://msdn.microsoft.com/zh-tw/library/bb608625.aspx).

**Step 3. Now you can work on your VBA code**

- For `Sub` as `Workbook_Open()`, `Workbook_BeforeClose(Cancel As Boolean)`, please add your code to `ThisWorkbook`.
- For other `Sub` or `Function`, you can either write in `ThisWorkbook` or create a module to write in.

### Save your add-in

Save your workbook as Microsoft Excel Add-in (`*.xla`, `*.xlam`) to `C:\WINDOWS\Application Data\Microsoft\AddIns\`.

Then this add-in would be automatically add to your add-in group.

## Create Buttons in Excel Add-in Menu

### Create Custom Buttons

Create your custom buttons whenever a workbook is opened.

```VBA
Private Sub Workbook_Open()

Application.DisplayAlerts = False

Dim CmdBar As CommandBar
Dim CmdBarCtl As CommandBarControl
Dim cmdBarSubCtl As CommandBarControl

On Error GoTo Err_Handler

Set CmdBar = Application.CommandBars("Worksheet Menu Bar")
CmdBar.Visible = True
CmdBar.Protection = msoBarNoMove


Set CmdBarCtl = CmdBar.Controls.Add(Type:=msoControlButton)
With CmdBarCtl
   .BeginGroup = True
   .Caption = "Single Button"
   .Style = msoButtonCaption
   .OnAction = "NameOfASub"
End With
Application.DisplayAlerts = True
Exit Sub


Set CmdBarCtl = CmdBar.Controls.Add(Type:=msoControlPopup)
CmdBarCtl.Caption = "Drop Down Button"

Set cmdBarSubCtl = CmdBarCtl.Controls.Add(Type:=msoControlButton)
With cmdBarSubCtl
   .Style = msoButtonIconAndCaption
   .Caption = "option 1"
   .FaceId = 317
   .OnAction = "NameOfASub"
   .Parameter = 1
   .BeginGroup = True
End With

Set cmdBarSubCtl = CmdBarCtl.Controls.Add(Type:=msoControlButton)
With cmdBarSubCtl
   .Style = msoButtonIconAndCaption
   .Caption = "option 2"
   .FaceId = 318
   .OnAction = "NameOfASub"
   .Parameter = 2
   .BeginGroup = True
End With

Set cmdBarSubCtl = CmdBarCtl.Controls.Add(Type:=msoControlButton)
With cmdBarSubCtl
   .Style = msoButtonIconAndCaption
   .Caption = "option 3"
   .FaceId = 224
   .OnAction = "NameOfASub"
   .Parameter = 3
   .BeginGroup = True
End With


Err_Handler:
MsgBox "Error " & Err.Number & vbCr & Err.Description
Application.DisplayAlerts = True
Exit Sub

End Sub
```

Note that you can refer to [this link](http://skp.mvps.org/faceid.htm) to choose the icon you want to show in the button.

### Define Actions
 `.OnAction = "NameOfASub"` means that a subfunction named `NameOfASub` is being called as an action of a click on the certain button. That is, you need to self-defined these actions.
 
 ```VBA
 Public Sub NameOfASub()
     MsgBox("Hello World!")
 End Sub
 ```


### Delete Custom Buttons Before Closed

Make sure you delete the button on closing Excel, otherwise an additional one will be added everytime you open a workbook.

```VBA
Private Sub Workbook_BeforeClose(Cancel As Boolean)
Dim CmdBar As CommandBar
Set CmdBar = Application.CommandBars("Worksheet Menu Bar")
CmdBar.Controls("Single Button").Delete
CmdBar.Controls("Drop Down Button").Delete
End Sub
```

## VBA Models

For detail information, please refer to [Excel VBA official Webpage](https://msdn.microsoft.com/zh-tw/library/office/ee861528.aspx)

### Variable Declaration

Declare a variable

```VBA
Dim cmd
```

Declare a variable with data type

```VBA
Dim cmd As String
```

Declare a global variable

```VBA
Public path As String
```

### Assign value to a variable

Remember to add `Set` at the beginning.

```VBA
Set path = "C:\my_folder"
```

### Range

> Represents a cell, a row, a column, a selection of cells containing one or more contiguous blocks of cells, or a 3-D range.


Get a specific cell

```VBA
Dim cell As Range
cell = ActiveSheet.Cells(1, 1)
```

Get a Range object that represents the used range on the specified worksheet. Read-only.

```VBA
Dim range As Range

range = ActiveSheet.UsedRange
'OR
range = ActiveSheet.Range("A1").CurrentRegion
```

Set value/formula of a Range object

```VBA
ActiveSheet.Cells(1, 1).Value = 24
ActiveSheet.Cells(2, 1).Formula = "=Sum(B1:B5)"
```


### Get the number of rows used

```VBA
Dim uTotalRows As Integer
uTotalRows = ActiveSheet.UsedRange.Rows.Count
MsgBox (uTotalRows)
```


### Define/Call a Function

Define a function.

> [Modifiers] Function FunctionName [(ParameterList)] As ReturnType  
> 
> ....Statements.... 
> 
> End Function  

```VBA
Function hypotenuse(ByVal side1 As Single, ByVal side2 As Single) As Single
    Return Math.Sqrt((side1 ^ 2) + (side2 ^ 2))
End Function
```

Call your defined function.

```VBA
Dim testLength, testHypotenuse As Single
testHypotenuse = hypotenuse(testLength, 10.7)
```

### String Concatenation

To concat string `str1` with string `str2`, you can use the operator `&`.

```VBA
Dim str1 As String
Dim str2 As String
...
Dim concat_str As String
concat_str = str1 & str2
```

### Save the current workbook

```VBA
ActiveWorkbook.Save
```

### Get path and name of current workbook/sheet

Get path of current workbook

```VBA
Dim path As String
path = Application.ActiveWorkbook.path
```

Get full path of current workbook (include workbook name)

```VBA
Dim fullpath As String
fullpath = Application.ActiveWorkbook.FullName
```

Get name of current workbook

```VBA
Dim bookname As String
bookname = ActiveWorkbook.Name
```

Get name of current sheet

```VBA
Dim sheetname As String
sheetname = ActiveSheet.Name
```

### Run an executable with parameters

Method 1. Use Shell

```VBA
cmd = "myApp.exe" & " " & "myParameter"
retval = Shell(cmd, vbNormalFocus)
```

Method 2. Use WScript

(This method allows you to wait until the execution completes and returns.)

```VBA
Set wsh = VBA.CreateObject("WScript.Shell")
Dim waitOnReturn As Boolean: waitOnReturn = True
Dim windowStyle As Integer: windowStyle = 1
cmd = "myApp.exe" & " " & "myParameter"
wsh.Run cmd, windowStyle, waitOnReturn
```

### Open up an existing workbook

Open an existing workbook (pop-up)

```VBA
Dim objXLApp, objXLWb
Set objXLApp = CreateObject("Excel.Application")
objXLApp.Visible = True
Set objXLWb = objXLApp.Workbooks.Open(report)
objXLWb.Sheets(sheetname).Activate
```

Open an existing workbook (invisible) in read-only mode

```VBA
Dim wb As Workbook
Set wb = Workbooks.Open(Path_to_WB, True, True)
```

## Flow Control

### If Else

```VBA
If condition [ Then ]  
    [ statements ]  
[ ElseIf elseifcondition [ Then ]  
    [ elseifstatements ] ]  
[ Else  
    [ elsestatements ] ]  
End If  
```

For more details, please refer to [VBA condition syntax](https://msdn.microsoft.com/zh-tw/library/752y8abs.aspx)

### Loop

`For Loop` simple example:

```VBA
For i = 1 To 10
    Total = Total + iArray(i)
Next i
```

`For Loop` example with `Step`:

```VBA
For d = 0 To 10 Step 0.1
    dTotal = dTotal + d
Next d
```

To break the for loop, use `Exit For`:

```VBA
For i = 1 To 100
    If dValues(i) = dVal Then
        indexVal = i
        Exit For
    End If
Next i
```

`For Each` simple example:

```VBA
For Each wSheet in Worksheets
    MsgBox "Found Worksheet: " & wSheet.Name
Next wSheet
```

For more information, please refer to [Excel VBA - For Loop, For Each, and Do Loop](https://blog.gtwang.org/programming/excel-vba-programming-loop/) and [Excel VBA Tutorial Part 6 - VBA Loops](http://www.excelfunctions.net/VBA-Loops.html).


## References
- [Build an Excel Add-In](http://www.fontstuff.com/vba/vbatut03.htm)
- [如何：在功能區顯示開發人員索引標籤](https://msdn.microsoft.com/zh-tw/library/bb608625.aspx)
- [Creating VBA Add-ins to Extend and Automate Microsoft Office Documents](https://msdn.microsoft.com/en-us/library/office/gg597509(v=office.14).aspx)
- [Excel VBA 參考](https://msdn.microsoft.com/zh-tw/library/office/ff194068.aspx)
- [face ID Table](http://www.ne.jp/asahi/fuji/lake/excel/faceid_02.html)
- [VBA condition syntax](https://msdn.microsoft.com/zh-tw/library/752y8abs.aspx)
- [how to create an Excel VBA Userform](http://www.excel-easy.com/vba/userform.html)
- [Excel VBA Tutorial Part 6 - VBA Loops](http://www.excelfunctions.net/VBA-Loops.html)
- [Excel VBA - For Loop, For Each, and Do Loop](https://blog.gtwang.org/programming/excel-vba-programming-loop/)
