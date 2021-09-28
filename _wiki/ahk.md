---
layout  : wiki
title   : 
summary : 
date    : 2021-09-16 00:01:57 +0900
updated : 2021-09-24 08:25:41 +0900
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

# autohotkey 작성할 때 기본 양식 

'''autohotkey
foldertree=D:\ahk\기본.ahk
#Include base.ahk
#2::reload

;;;;;;;;;;;;;;;;;;;;;;
;;;;;여기에 함수를 작성합시다.

f3:: sub12()
;;;;;;;;;;;;;;;;;;;;;;

sub12()
{
	loop, 10
	{
		sleep 100
		sub11()
		send ^{tab}
		sleep 300
	}
	return
}

sub11()
{
	sleep 300
	send !d
	sleep 300
	send {home}
	sleep 100
	send {right 19}pp{enter}
	return
}
'''

# base

''' autohotkey
foldertree=D:\ahk\base.ahk

; SetTimer, timechange, 100
; return

^+3::
	MouseGetPos, Mouse_X, Mouse_Y,,,
	PixelGetColor, color, %Mouse_X%, %Mouse_Y%
	return
^+4::
    send, click, %Mouse_X%, %Mouse_Y%{enter}
	send, while(color{!}=%color%){{}{enter}
    send,     PixelGetColor, color, %Mouse_X%, %Mouse_Y%{enter}
	send, sleep 1000{enter}
	send, {}}{esc}{k 3}
	return
	
^+5::pixel_get_color()
f8::ClipBoardPaste()


pixel_get_color()
{
	MouseGetPos, MouseX, MouseY
	PixelGetColor, color, %MouseX%, %MouseY%
	MsgBox The Color position %MouseX%, %MouseY% is %color%.
	return
}

window_get_title()
{
	WinGetTitle, clipboard, A
	return
}

^e::edit %foldertree%

ClipBoardPaste()
{

		StringLen, Length_str, clipboard
		sleep, 50
		index_strLen = %Length_str%
		sleep, 50
		loop
		{
			if index_strLen = 0
				break
			StringMid, outputChar, clipboard, Length_str-index_strLen+1, 1
			send,{%outputChar%}
			index_strLen-=1
		}
		sleep, 200
	return
}



timechange:
user_hour = 22		;시각 keyin
user_min = 0		;분 keyin
user_sec = 0		;초 keyin

if A_Hour = %user_hour%
{
	if A_Min = %user_min%
	{					
		if A_Sec = %user_sec%
		{
			/*
			   여기에 실행문을 입력
			   */
		}
	}
}

'''
# [[앙키한자추가]]

# 테스트글 삽입
