---
title: date and time functions in lua
date: 2017-09-13 16:55:22
tags: lua
---


os.date ([format [, time]])

Returns a string or a table containing date and time, formatted according to the given string format.

If the time argument is present, this is the time to be formatted (see the os.time function for a description of this value). Otherwise, date formats the current time.

If format starts with '!', then the date is formatted in Coordinated Universal Time. After this optional character, if format is the string "*t", then date returns a table with the following fields: year (four digits), month (1--12), day (1--31), hour (0--23), min (0--59), sec (0--61), wday (weekday, Sunday is 1), yday (day of the year), and isdst (daylight saving flag, a boolean).

If format is not "*t", then date returns the date as a string, formatted according to the same rules as the C function strftime.

When called without arguments, date returns a reasonable date and time representation that depends on the host system and on the current locale (that is, os.date() is equivalent to os.date("%c")).


os.time ([table])

Returns the current time when called without arguments, or a time representing the date and time specified by the given table. This table must have fields year, month, and day, and may have fields hour, min, sec, and isdst (for a description of these fields, see the os.date function).

The returned value is a number, whose meaning depends on your system. In POSIX, Windows, and some other systems, this number counts the number of seconds since some given start time (the "epoch"). In other systems, the meaning is not specified, and the number returned by time can be used only as an argument to date and difftime.



code examples



get the current time as a table variable
```lua

os.date("*t")


```

get the time based on the time offset, if we know the time offset, we can get time with a different timezone with current system setting

```lua

-- offset is -10 hours, which is 36000 in seconds
local t = os.date("*t",os.time()-36000)
```

what if we don't know the time offset, but know the timezone in linux environment,
one of the possible way is to using 'zdump' command in linux system
```lua

local command= "zdump "
local handle = io.popen(command)
local result = handle:read("*a")
print(result)
handle:close()
```
