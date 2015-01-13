title: 一键切换jdk版本
tags:
  - 工作
  - 开发
  - 软件
id: 3782
categories:
  - 学习笔记
date: 2014-11-07 16:08:29
---

工作中有时候会切换jdk版本.有时候需要用个64位的.有时候需要用32位的.频繁手动很不方便,参考了一下,写了一个bat脚本,保存为bat,运行即可切换.同理可以改改,运来切换1.7,1.8这样的版本.
<pre class="lang:default decode:true ">@echo off

:init
set JAVA_HOME_32=D:\5.Program\Jdk32Home
set JAVA_HOME_64=D:\Software\JdkHome

:start
echo.
echo =============================================
echo jdk版本列表
echo 32 (%JAVA_HOME_32%)
echo 64 (%JAVA_HOME_64%)
echo =============================================

:select
set /p opt=请选择jdk版本：
if %opt%==32 (
  start  /I /WAIT /B wmic ENVIRONMENT where name='JAVA_HOME' set VariableValue="%JAVA_HOME_32%" &gt;nul
rem reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v JAVA_HOME /t reg_sz /d "%JAVA_HOME_32%" /f
  goto success
)
if %opt%==64 (
    start /I /WAIT /B wmic ENVIRONMENT where name='JAVA_HOME' set VariableValue="%JAVA_HOME_64%" &gt;nul
rem reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v JAVA_HOME /t reg_sz /d "%JAVA_HOME_64%" /f
    goto success
)
echo 选择的版本错误,请重新选择！
PAUSE
goto start

:success
echo.
echo 设置环境变了成功.</pre>
&nbsp;