---
layout  : wiki
title   : 
summary : 
date    : 2022-02-23 22:44:43 +0900
updated : 2022-02-23 23:03:45 +0900
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

* <Capslock> key를 한번 누르면 <Esc>로
* 조합 때는 <Control> 로 적용하게 한다.

|SetCapsLockState, alwaysoff
|Capslock::
|Send {LControl Down}
|KeyWait, CapsLock
|Send {LControl Up}
|if ( A_PriorKey = "CapsLock" )
|{
|    Send {Esc}
|    if(IME_CHECK("A"))
|    Send, {VK15}    ;영문이라면 한영전환 키를 입력해준다.
|
|}
|return
|
|
|
|/*
|  IME check 
|*/
|IME_CHECK(WinTitle) {
|  WinGet,hWnd,ID,%WinTitle%
|  Return Send_ImeControl(ImmGetDefaultIMEWnd(hWnd),0x005,"")
|}
|Send_ImeControl(DefaultIMEWnd, wParam, lParam) {
|  DetectSave := A_DetectHiddenWindows
|  DetectHiddenWindows,ON
|   SendMessage 0x283, wParam,lParam,,ahk_id %DefaultIMEWnd%
|  if (DetectSave <> A_DetectHiddenWindows)
|      DetectHiddenWindows,%DetectSave%
|  return ErrorLevel
|}
|ImmGetDefaultIMEWnd(hWnd) {
|  return DllCall("imm32\ImmGetDefaultIMEWnd", Uint,hWnd, Uint)
|}
