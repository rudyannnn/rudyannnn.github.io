---
layout  : wiki
title   : 
summary : 
date    : 2022-06-05 00:07:04 +0900
updated : 2022-06-20 01:49:59 +0900
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}



>#s::  ;;백장열기
>    sleep 300
>    loop, 50
>    {
>	sleep 50
>	send {tab}
>	send ^{enter}
>    }
>    return
>
>f7:: ;; 짝수페이지 읽기
>	sleep 300
>	send +#s
>            PixelGetColor, color, 856, 12
>	while !(color="0xD77800")
>	PixelGetColor, color, 856, 12
>
>	sleep 300
>	MouseClickDrag, L, 0,0,960,1080
>	sleep 300
>	readHanBook()
>	return
>
>f8:: ;; 홀수페이지 읽기
>	sleep 300
>	send +#s
>	PixelGetColor, color, 856, 12
>	while !(color="0xD77800")
>	PixelGetColor, color, 856, 12
>	
>	sleep 300
>	MouseClickDrag, L, 1920,1080,960,0
>	sleep 300
>	readHanBook()
>	return
> 
>
>^f5:: readHanBook()
>
>
>readHanBook()
>{
>  ;; 국문책 읽기
>	sleep 300
>	send +o
>	sleep 100
>	send https://lens.google.com/search?p={enter}
> 
>	mousemove 1662, 33
>	PixelGetColor, color, 1662, 33
>	while !(color="0x242120")
>	PixelGetColor, color, 1662, 33
>	click, 1662, 33
>
>
>	PixelGetColor, color, 1451, 172 ;;검색하려면
>	while !(color="0xFACBAE")
>	PixelGetColor, color, 1451, 172
>	click, 1451, 172
>
>	send ^v
>	sleep 1000
>
>	PixelGetColor, color, 842, 940
>	while (color="0xFFFFFFFF")
>	PixelGetColor, color, 842, 940
>
>	PixelGetColor, color, 825, 984
>	while !(color="0x242120")
>	PixelGetColor, color, 825, 984
>
>	sleep 1500
>
>	mousemove 492, 963
>	PixelGetColor, color, 492, 963
>	while (color="0xFFFFFFFF")
>	PixelGetColor, color, 492, 963
>	click, 492, 963
>
>	mousemove 1368, 687
>	PixelGetColor, color, 1368, 687
>	while !(color="0xCA661B")
>	PixelGetColor, color, 1368, 687
>	click 1368, 687
>
>	sleep 1000
>	send !p
>	sleep 1000
>	send x
>
>	return
>}
>
>^f6::   ;; 영문 책읽기
>	sleep 300
>	send +o
>	sleep 100
>	send https://lens.google.com/search?p={enter}
> 
>	mousemove 1662, 33
>	PixelGetColor, color, 1662, 33
>	while !(color="0x242120")
>	PixelGetColor, color, 1662, 33
>	click, 1662, 33
>
>
>	PixelGetColor, color, 1451, 172 ;;검색하려면
>	while !(color="0xFACBAE")
>	PixelGetColor, color, 1451, 172
>	click, 1451, 172
>
>	send ^v
>	sleep 1000
>
>	PixelGetColor, color, 842, 940
>	while (color="0xFFFFFFFF")
>	PixelGetColor, color, 842, 940
>
>	PixelGetColor, color, 825, 984
>	while !(color="0x242120")
>	PixelGetColor, color, 825, 984
>
>	sleep 1500
>
>	mousemove 492, 963
>	PixelGetColor, color, 492, 963
>	while (color="0xFFFFFFFF")
>	PixelGetColor, color, 492, 963
>	click, 492, 963
>
>	mousemove 1368, 687
>	PixelGetColor, color, 1368, 687
>	while !(color="0xCA661B")
>	PixelGetColor, color, 1368, 687
>	click 1368, 687
>
>	sleep 1000
>	send !p
>
>	mousemove, 1280, 172
>	PixelGetColor, color, 1280, 172
>	while !(color="0xEE8D51")
>	PixelGetColor, color, 1280, 172
>	click, 1280, 172
>
>	return
