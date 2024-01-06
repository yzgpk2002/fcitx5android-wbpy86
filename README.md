# fcitx5android-wbpy86
一个适合 fcitx5-android wubi86词库，包含大量常用wubi86词库、生僻字和拼音。因原始wubi86词库的词组很少，网上只能找到词组丰富的wubi98词库和电脑版搜狗五笔词库，就使用excel的VBA进行合并。
##  一、制作过程
### （一）下载wubi98词库，并转化为TXT文件

>   要在 Windows Subsystem for Linux (WSL) 中使用 libime_tablebin 来修改位于 Windows 系统上的 D:\backup\wubi\wbpy.main.dict 词库文件，你需要按照以下步骤操作：

> （1）访问 Windows 文件系统： 在 WSL 中，Windows 的文件系统被挂载在 /mnt/ 下。因此，你可以通过 /mnt/d/backup/wubi/wbpy.main.dict 路径来访问 D:\backup\wubi\wbpy.main.dict 文件。

> （2） libime_tabledict  -d  /mnt/d/backup/wubi/wbpy.main.dict /mnt/d/backup/wubi/wbpy.main.txt

> （3）导出电脑版搜狗五笔词库为TXT文件

### （二）把转化好的两个txt文件用excel打开，使用VBA把wubi98词库存在字和词组替换为搜狗五笔的86编码，并合并搜狗五笔独有的字和词组

 Sub UpdateData()
    ' 定义工作簿和工作表变量
    Dim wsMain As Worksheet, wsSystem As Worksheet
    Dim mainLastRow As Long, systemLastRow As Long, i As Long, j As Long
    Dim isMatchFound As Boolean
    ' 假设工作簿已经打开，并直接引用它们
    Set wsMain = Workbooks("wbpymain.xlsm").Sheets("wbpymain")
    Set wsSystem = Workbooks("wubilex86system.xlsm").Sheets("wubilex86system")
    
    ' 确定每个工作表的最后一行
    mainLastRow = wsMain.Cells(wsMain.Rows.Count, "D").End(xlUp).Row
    systemLastRow = wsSystem.Cells(wsSystem.Rows.Count, "B").End(xlUp).Row
    
    ' 打印总行数以供调试
    Debug.Print "Total rows in wsMain: " & mainLastRow
    Debug.Print "Total rows in wsSystem: " & systemLastRow
    
    ' 遍历wbpy.main工作表
    For i = 1 To mainLastRow
        Debug.Print "Checking row " & i ' 打印正在检查的行号
        ' 检查C列是否不包含@
        If InStr(wsMain.Cells(i, "C").Value, "@") = 0 Then
            isMatchFound = False
            For j = 1 To systemLastRow
                If wsMain.Cells(i, "D").Value = wsSystem.Cells(j, "B").Value Then
                    ' 复制数据并删除行
                    wsMain.Cells(i, "C").Value = wsSystem.Cells(j, "A").Value
                    wsSystem.Rows(j).Delete
                    systemLastRow = systemLastRow - 1 ' 更新行数因为一行已经被删除
                    isMatchFound = True
                    Debug.Print "Match found - Row " & j & " in wsSystem deleted"
                    Exit For
                End If
            Next j
            If Not isMatchFound Then
                Debug.Print "No match found for D" & i
            End If
        Else
            Debug.Print "Skipped row " & i & " because of '@' in column C"
        End If
    Next i
 End Sub

### 导出为wbpymain86.TXT文件 ，使用VScode把文件编码由utf-16改为utf-8。修改wbpy86.conf文件中的词库txt文件名和输入法名称为“五笔86.拼音”


![Image](https://github.com/orgs/gaoerji2024/projects/1/assets/110663021/ac1db312-eac0-4d85-aead-b0ccefab2e54)


## 二、使用方法
### 在fcitx5-android中导入wbpy86.conf和wbpymain86.TXT

> （1）输入法设置——附加组件——码表——管理码表输入法，添加——从单独的文件导入——分别选择wbpy86.conf和wbpymain86.TXT导入。码表输入法中出现“五笔86-拼音”。

> （2）输入法设置——输入法——配置输入法。添加“五笔86-拼音”，返回就可以使用。
