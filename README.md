<h1 align="center">no detailed explanation, find out how to do it by yourself</h1>


```lua
_G.wait = function( time ) print( 'tamper bypass works :D', time ) end
```
![Alt text](https://raw.githubusercontent.com/wildhornets/moondeob/refs/heads/main/img/%7BEAF93D06-8E45-4A0D-8BB6-9B8206EBC1B7%7D.png)
```lua
local function serializeTable(tbl, indent, seen)
    indent = indent or 0
    seen = seen or {}

    if seen[tbl] then
        return '"<cycle>"'
    end
    seen[tbl] = true

    local str = "{"
    local prefix = ""

    for key, value in pairs(tbl) do
        local formattedKey = type(key) == "string" and string.format("[%q]", key) or "[" .. tostring(key) .. "]"
        str = str .. prefix .. formattedKey .. "="

        if type(value) == "table" then
            str = str .. serializeTable(value, indent + 1, seen) .. ","
        elseif type(value) == "string" then
            str = str .. string.format("%q", value) .. ","
        else
            str = str .. tostring(value) .. ","
        end
    end

    str = str .. string.rep("", indent) .. "}"
    return str
end
```

```lua
_G.asciidump = function( str )
    local str = '';
    for i = 1, #str do
        str = str + '\' .. string.byte( string.sub( str, i, i ) )
    end
    print( str == '' and 'empty' or str )
end

_G.serializeTable = function(tbl, indent, seen)
    indent = indent or 0
    seen = seen or {}

    if seen[tbl] then
        return '"<cycle>"'
    end
    seen[tbl] = true

    local str = "{"
    local prefix = ""

    for key, value in pairs(tbl) do
        local formattedKey = type(key) == "string" and string.format("[%q]", key) or "[" .. tostring(key) .. "]"
        str = str .. prefix .. formattedKey .. "="

        if type(value) == "table" then
            str = str .. serializeTable(value, indent + 1, seen) .. ","
        elseif type(value) == "string" then
            str = str .. string.format("%q", value) .. ","
        else
            str = str .. tostring(value) .. ","
        end
    end

    str = str .. string.rep("", indent) .. "}"
    return str
end

_G.wait = function( time )
    print( 'no' );
end;

string.dump = nil;
```
![Alt text](https://raw.githubusercontent.com/wildhornets/moondeob/refs/heads/main/img/%7BC6ED2CA0-6224-40ED-B08C-7A723245B3F4%7D.png)
Uses opcode and does something. For example, opcode `LOADK` is:

```lua
VM[ instr[ 2 ] ] = instr[ 3 ];
```

If you want to do:

```lua
local a = 'yes';
```

you gotta do:

```lua
{ 'LOADK', 0, 'yes' }
```

also, one tip:

```lua
getfenv()
```

returns `_G`, which contains all globals, including the `print` function. This might help when deobfuscating msec v3.

result bytecode deobfuscated:

![Alt text](https://raw.githubusercontent.com/wildhornets/moondeob/refs/heads/main/img/2.png)

![Alt text](https://raw.githubusercontent.com/wildhornets/moondeob/refs/heads/main/img/1.png)

![Alt text](https://raw.githubusercontent.com/wildhornets/moondeob/refs/heads/main/img/3.png)

![Alt text](https://raw.githubusercontent.com/wildhornets/moondeob/refs/heads/main/img/4.png)

![Alt text](https://raw.githubusercontent.com/wildhornets/moondeob/refs/heads/main/img/5.png)
