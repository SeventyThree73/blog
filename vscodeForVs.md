# 在VScode中使用VS项目（用命令调用VS项目）

不习惯VS的编辑器  
'ctrl' + '/' 都不支持  
果断决定转移至VScode

* 注意区分 VS 和 VScode 是不同的东西
* VS全名：Visual Studio
* VScode全名：Visual Studio Code

## 安装相关软件及插件
编者主要想写cpp，所以以下以cpp展开
* 安装Virsual Studio 和 VS code
* Virsual Studio 中安装 `使用C++的桌面开发`
* VScode 安装 `C/C++` 插件

## 添加环境变量
* 查找VS中`使用C++的桌面开发`的安装位置
* 在系统环境变量PATH中添加（具体位置根据自己电脑调整）
```
D:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE
```

## 在VS中新建一个CPP项目
依次选择
* 创建新项目
* C++空项目
* 项目名自定义，然后创建
* 记住创建位置
* 编译运行一次Hello World
* 查看目录下多出的`x64`或者`x86`文件夹

## 在VScode中打开项目文件夹
* 创建`.vscode`文件夹
* `.vscode`文件夹中创建`tasks.json`文件
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Build Solution",
            "type": "shell",
            "command": "devenv",
            "args": [
                "${workspaceFolder}/${workspaceFolderBasename}.sln",
                "/Project",
                "${workspaceFolder}/${workspaceFolderBasename}.vcxproj",
                "/Build",
                "Debug"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "presentation": {
                "echo": true,
                "reveal": "always",
                "focus": false,
                "panel": "shared"
            },
            "problemMatcher": [],
            "detail": "Build Debug version using devenv"
        },
    ]
}
```
* `.vscode`文件夹中创建`launch.json`文件
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "C++ MSVC Debug",
            "type": "cppvsdbg",
            "request": "launch",
            "preLaunchTask": "Build Solution",
            "program": "${workspaceFolder}/x64/Debug/${workspaceFolderBasename}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}/x64/Debug",
            "environment": [],
            "externalConsole": false
        }
    ]
}

```
* `launch.json`中的x64的位置根据自己电脑决定，和前面检查的文件夹相同

## 建立自动化脚本
博主习惯把脚本放在`E:\Tools\Scripts`目录下
* 选择一个存放脚本的目录，例如`E:\Tools\Scripts`
* 将路径添加到系统环境变量PATH中
* 新建文本文件`vs_cpp.bat`
```batch
@echo off
:: Create .vscode directory if it doesn't exist
if not exist ".vscode" (
  mkdir .vscode
)

:: Generate launch.json
(
  echo {
  echo   "version": "0.2.0",
  echo   "configurations": [
  echo     {
  echo       "name": "C++ MSVC Debug",
  echo       "type": "cppvsdbg",
  echo       "request": "launch",
  echo       "preLaunchTask": "Build Solution",
  echo       "program": "${workspaceFolder}/x64/Debug/${workspaceFolderBasename}.exe",
  echo       "args": [],
  echo       "stopAtEntry": false,
  echo       "cwd": "${workspaceFolder}/x64/Debug",
  echo       "environment": [],
  echo       "externalConsole": false
  echo     }
  echo   ]
  echo }
) > .vscode\launch.json

:: Generate tasks.json
(
  echo {
  echo   "version": "2.0.0",
  echo   "tasks": [
  echo     {
  echo       "label": "Build Solution",
  echo       "type": "shell",
  echo       "command": "devenv",
  echo       "args": [
  echo         "${workspaceFolder}/${workspaceFolderBasename}.sln",
  echo         "/Project",
  echo         "${workspaceFolder}/${workspaceFolderBasename}.vcxproj",
  echo         "/rebuild",
  echo         "Debug"
  echo       ],
  echo       "group": {
  echo         "kind": "build",
  echo         "isDefault": true
  echo       },
  echo       "presentation": {
  echo         "echo": true,
  echo         "reveal": "always",
  echo         "focus": false,
  echo         "panel": "shared"
  echo       },
  echo       "problemMatcher": [],
  echo       "detail": "Build Debug version using devenv"
  echo     }
  echo   ]
  echo }
) > .vscode\tasks.json

echo Files created successfully.
```

