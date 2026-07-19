no detailed explanation, find out how to do it by yourself


```lua
_G.wait = function( time ) print( 'tamper bypass works :D', time ) end
```

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
