---
title: 开机自启动tomcat和redis
tags: 批处理
categories: bat
date: 2017-11-04
---

配置文件 conf.ini
```ini
[reids]
redis-port=6379
redis-bin=E:\Redis ;redis所在文件夹
[tomcat-dc]
dc-port=8888
dc-bin=E:\apache-tomcat-dc ;tomcat-dc所在文件夹
[tomcat-app]
app-port=8080
app-bin=E:\apache-tomcat-app ;tomcat-app所在文件夹
```

start.bat
```bat
rem 获取配置文件 使用 %key% 获取值
@setlocal enabledelayedexpansion
for /f "usebackq delims=" %%a in (conf.ini) do (
    set content=%%a
    if not "!content:~0,1!" == "[" (
        for /f "delims=; tokens=1" %%b in ("!content!") do (
            set content=%%b
            for /f "delims== tokens=1-2" %%i in ("!content!") do (
                set key=%%i
                    set key=!key: =!
                set value=%%j
                    set value=!value: =!
                set !key!=!value!
            )
        )
    )
)
rem 使用 netstat -ano| findstr 端口号  查看当前端口是否占用
rem 没有占用的话启动相应项目
@netstat -an  | findstr "%redis-port%"
if %ERRORLEVEL% == 0 (
		@echo "redis had started"
	) else (
		@echo "start redis..."
		REM @start "redis-server" cmd /k "%redis-bin%/redis-server.exe %redis-bin%/redis.windows.conf"
	)

@netstat -an  | findstr "%dc-port%"
if %ERRORLEVEL% == 0 (
		@echo "tomcat-dc had started"
	) else (
		@echo "start tomcat-dc"
		cd /d "%dc-bin%/bin"
		start "tomcat-dc" cmd /c  "%dc-bin%/bin/startup.bat"
	)

@netstat -an  | findstr "%app-port%"
if %ERRORLEVEL% == 0 (
		@echo "tomcat-app had started"
	) else (
		@echo "start tomcat-app"
		cd /d "%app-bin%/bin"
		@start "tomcat-app" cmd /c "%app-bin%\bin\startup.bat"
```

由start.bat生成runSystem.bat文件的脚本 generate.bat
```bat
echo off
echo hello

set name=runSystem.bat
echo echo off > %name%
rem %cd% 当前目录 
echo cd /d %cd% >> %name%
echo start.bat >> %name%
echo pause >> %name%

```
- 双击generate.bat生产runSystem.bat文件
- 点击Window --> 所有程序 --> 启动 --> 右键打开 --> 将runSystem.bat文件放入 -->重启计算机
