---
layout  : wiki
title   : 
summary : 
date    : 2022-02-23 22:53:03 +0900
updated : 2022-02-23 23:04:11 +0900
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}


|foldertree=D:\ahk\base.ahk
|
|; SetTimer, timechange, 100
|; return
|
|^+3::
|	MouseGetPos, Mouse_X, Mouse_Y,,,
|	PixelGetColor, color, %Mouse_X%, %Mouse_Y%
|	return
|^+4::
|    send, click, %Mouse_X%, %Mouse_Y%{enter}
|	send, while(color{!}=%color%){{}{enter}
|    send,     PixelGetColor, color, %Mouse_X%, %Mouse_Y%{enter}
|	send, sleep 1000{enter}
|	send, {}}{esc}{k 3}
|	return
|	
|^+5::pixel_get_color()
|f8::ClipBoardPaste()
|
|
|pixel_get_color()
|{
|	MouseGetPos, MouseX, MouseY
|	PixelGetColor, color, %MouseX%, %MouseY%
|	MsgBox The Color position %MouseX%, %MouseY% is %color%.
|	return
|}
|
|window_get_title()
|{
|	WinGetTitle, clipboard, A
|	return
|}
|
|^e::edit %foldertree%
|
|::dsgtd::
|send, 안녕하세요.  LGD OTS Task 김세영입니다.`n`n`n`n--------------------------------------------------------------`n김세영 책임 (Se-Yeong Kim) (OTS Task)`n010-3921-7931, rudyan@lgdisplay.com`n--------------------------------------------------------------{up 5}
|return
|
|
|ClipBoardPaste()
|{
|
|		StringLen, Length_str, clipboard
|		sleep, 50
|		index_strLen = %Length_str%
|		sleep, 50
|		loop
|		{
|			if index_strLen = 0
|				break
|			StringMid, outputChar, clipboard, Length_str-index_strLen+1, 1
|			send,{%outputChar%}
|			index_strLen-=1
|		}
|		sleep, 200
|	return
|}
|
|
|
|timechange:
|user_hour = 22		;시각 keyin
|user_min = 0		;분 keyin
|user_sec = 0		;초 keyin
|
|if A_Hour = %user_hour%
|{
|	if A_Min = %user_min%
|	{					
|		if A_Sec = %user_sec%
|		{
|			/*
|			   여기에 실행문을 입력
|			   */
|		}
|	}
|}
|
|/*
|
|`::	;via 뚫을때 layer 원복
|	
|	MouseGetPos, Mouse_X, Mouse_Y, , ,
|	widthh=%widthhh%
|	widthh-=220
|	click,left, %widthh%,150
|	MouseMove,%Mouse_X%, %Mouse_Y%
|	return
|
|f1::	;Line Width 조정
|	MouseGetPos, Mouse_X, Mouse_Y, , ,
|	widthh=%widthhh%
|	widthh-=200 
|	click,left,%widthh%,128
|	MouseMove,%Mouse_X%, %Mouse_Y%
|	return
|
|f2::	;add via
|	MouseGetPos, Mouse_X, Mouse_Y, , ,
|	widthh=%widthhh%
|	widthh-=130
|	click,left,%widthh%, 636
|	MouseMove,%Mouse_X%, %Mouse_Y%
|	return
|
|f5::	; cut fill
|	MouseGetPos, Mouse_X, Mouse_Y, , ,
|	widthh=%widthhh%
|	widthh-=130 
|	click,left,%widthh%,706
|	MouseMove,%Mouse_X%, %Mouse_Y%
|	return
|
|searching()
|{
|	sleep 300
|	click, 665, 497
|	loop 2000
|	{
|		PixelGetColor, color, 1909, 840
|		sleep 100
|		send {down}
|		sleep 100
|		if(color=0xF0F0F0)
|		{
|			send {up}
|			macro2()
|			return
|		}
|	}
|	return
|}
|
|copyHanja()
|{
|	while(color!=0x5AC703)
|	{
|		send {home}
|		WinActivate 네이버 한자
|		PixelGetColor, color, 384, 138
|		sleep 1000
|	}
|	click, 713, 253 ;; 검색창
|	send {end}+{home}{BackSpace 3}^v{enter}
|	sleep 2000
|	click, 513, 471 ;; 첫 한자 클릭
|	sleep 2000
|}
|	
|*/
|
