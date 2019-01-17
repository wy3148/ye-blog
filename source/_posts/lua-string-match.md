---
title: lua-string match the magic characters
date: 2019-01-18 10:47:59
tags: lua
---


to escapse the  magic character , we have to use '%' to turn off pattern match when call string.find or gsub functions


```
function test_string()
    local name = "res${CALLERID(name)}"
    local s1,s2 = string.gsub(name,"$%{CALLERID%(name%)%}", "lilizhou")
    print(s1)
    print(s2)
end
```

in the exampe, we try to replace the whole string '${CALLERID(name)}' with a new string.
