---
layout  : wiki
title   : 
summary : 
date    : 2022-02-23 22:51:37 +0900
updated : 2022-02-23 23:03:56 +0900
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

|foldertree=D:\ahk\기본.ahk
|
|#2::reload
|#3::MouseGetPos, Mouse_X, Mouse_Y,,,
|#4::send, %Mouse_X%, %Mouse_Y%
|
|^e::edit %foldertree%
|
|f3::eeprom_read()
|
|
|eeprom_read()
|{
|	sleep 500
|	loop 256
|	{
|		winactivate Microsoft Excel
|		clipboard := aaaa
|		send {enter}{f2}+{home}^c
|		sleep 200
|		send ^c
|		winactivate NTP7518 
|;		click 247, 629
|;		click 247, 629  ;; 주소
|		click 606, 627
|		click 606, 627
|		send {enter}^v{tab}{tab}{enter}
|		sleep 300
|		send +{tab}^c
|		winactivate Microsoft Excel
|		send {tab}^v
|	}
|	return
|}
|
