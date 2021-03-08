title: 删除ppt文件内所有同一位置的内容
author: dogh
tags:
  - ppt
  - 重复操作
categories:
  - 杂项目录
date: 2020-12-01 22:43:00
---
[原文链接](https://blog.csdn.net/weixin_43856199/article/details/97284359)
转载遵循 CC 4.0 BY-SA 版权协议

在视图中创建宏，然后选中要删除的区域和内容，运行宏。done。
```vbnet
Sub Test()

Dim oSlide As Slide, oShape As Shape
Dim myWidth As Single, myHeight As Single, myTop As Single, myLeft As Single

On Error Resume Next
If ActiveWindow.Selection.ShapeRange.Count <> 1 Then
If Err.Number <> 0 Then
MsgBox "none" & vbCrLf & "choose one", vbExclamation + vbOKOnly
Err.Clear
Exit Sub
End If
MsgBox "choose exceed 1" & vbCrLf & "choose one", vbExclamation + vbOKOnly
Exit Sub
End If

Set oShape = ActiveWindow.Selection.ShapeRange(1)
myTop = oShape.Top
myLeft = oShape.Left
myHeight = oShape.Height
myWidth = oShape.Width

For Each oSlide In ActivePresentation.Slides
For Each oShape In oSlide.Shapes
If Abs(myTop - oShape.Top) < 1 And Abs(myLeft - oShape.Left) < 1 And Abs(myHeight - oShape.Height) < 1 And Abs(myWidth - oShape.Width) < 1 Then
oShape.Delete
End If
Next
Next
End Sub

```
