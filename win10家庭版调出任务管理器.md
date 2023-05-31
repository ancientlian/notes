1.管理员身份运行dos，输入
`reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System" /v disabletaskmgr /d 0 /t REG_DWORD /f`

2.开始/运行/输入regedit,打开注册表编辑器，依次展开`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies`分支，在右侧窗格中有一个名为`DisableTaskMgr`的`Dword`值（如果没有此键值新建），将其值为“1”禁用“任务管理器”，设置为“0”则为启用。
