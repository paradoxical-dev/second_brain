---
id: tables_as_classes_lua
aliases: []
tags:
  - OOP
  - Lua
---

# Tables
- Tables are the primary data structure in lua, similar to objects in javascript.
- They take key/value pairs surrounded by curly brackets
- Lua tables must only contain unique keys within them; no duplicate key values are allowed
```lua
local table = {
    key1 = value1,
    key2 = value2
}
```

## Meta Tables
- `Metatables` define how other tables respond in certain operations (i.e.indexing, addition, string conversion).
- `Metamethods` are functions that are defined to override or extend table behavior. Foe example the `_index` metamethod
    - The `_index` metamethod defines what happens when a key is accessed that does not exist in the given table.
- The `setmetatable` function is used to specify the custom behaviors for operations on the specified table
    - It takes 2 parameters: the table the metatable should be applied to, and the metatable to use for this table.

# Example using metatables and metamethods
## 1. Creating a base table to store functions and metatables
```lua
Set = {}
```
- You can then populate this table with necessary methods or fields. In this example, it is used for storing and comparing sets of numbers, using unions and intersects
```lua
-- Constructor for creating and storing sets
function Set.new (t)
    local set = {}
    for _, l in ipairs (t) do set[l] = true end
    return set
end

-- Find union of 2 given tables
function Set.union (a,b)
    local res = Set.new{}
    for k in pairs(a) do res[k] = true end
    for k in pairs(b) do res[k] = true end
    return res
end

-- Find intersects of 2 given tables
function Set.intersection (a,b)
    local res = Set.new{}
    for k in pairs(a) do
        res[k] = b[k]
    end
    return res
end

-- Function to print resulted sets
function Set.tostring (set)
    local s = "{"
    local sep = ""
    for e in pairs(set) do 
        s = s .. sep .. e
        sep = ","
    end
    return s .. "}"
end

function Set.print (s)
    print(Set.tostring(s))
end
```
## 2. Initialize and attach metatable
- Next we can ensure all sets contain the same metatable upon creation by modifying our constructor function and initializing the metatable itself
```lua
Set.mt = {} -- metatable for sets

-- Updated `new` function
function Set.new (t)
    local set = {}
    setmetatable(set, Set.mt) -- set each instance of set to contain new metatable
    for _, l in ipairs(t) do set[l] = true end
    return set
end
```
- With a set metatable, we can apply different fields and corresponding metamethod values to it
```lua
-- Set _add field to point to the Set.union function
Set.mt._add = Set.union
```
- Now any time lua attempts to add 2 sets it will recognize their metatable with the attached _add field and call the `union` function with the 2 operands used as arguments
### Ex)
```lua
s1 = Set.new{10,20,30,50}
s2 = Set.new{30,1}
s3 = s1 + s2

Set.print(s3) -- Output: {1, 10, 20, 30, 50}
```
- Since all sets contain the same metatable, using `s1 + s2` is equivalent to using `Set.union(s1, s2)` returning the union of the 2 tables

# Example using `_index` for inheritance
## 1. Define class table
```lua
local Example = {}
Example._index = Example
```
- `Example` is the table to act as a class
- `Example._index = Example` sets the metamethod to look through the `Example` table when a key is unable to be accessed in an instance.

## 2. Define a constructor
```lua
function Example:new()
    local self = setmetatable({}, Example)
    -- Initialization logic
    return self
end
```
- When this constructor method `:new()` is called a variable `self` is initialized, creating an empty table and setting its metatable to `Example`
    **NOTE** A method in lua passes its `self` variable (similar to `this` in javascript) as the first argument within the method. So calling `self:function(example)` would pass the self table in as the example paramater.
- Doing this makes the variable `self` an instance of `Example` and `self` will now share inheritance with `Example` including other defined methods or fields and their metamethod values
- With the `_index` field set to the `Example` table, any key accessed in `self` (which does not currently exist in `self`) will be accessed from `Example`
