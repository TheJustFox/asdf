---
title: "How to update offsets in ballistic"
---
# Information
I would suggest you write your own exploit if you actually manage to get ballistic running with this tutorial 🙏.
This will work not only for ballistic but it is based on ballistic, also don't try to copy these offsets because it's for version-b591875ddfbc4294 lol.

here is what we will get (for now):
- Print
- GetTaskScheduler
- task.defer
- LuaVM_Load
- DecryptLuaState
- PushInstance
- Luau_Execute

# Print offset
Really simple.
1. Search for string "**Video recording started**".
2. Xref to it's call.
3. You will see something like this:
```c++
sub_11FBE70(1u, (__int64)"Video recording started");
```
the **sub_11FBE70** is our function to __print__.
Implementation:
```c++
// rebase
#define rebase(offset) offset + (uintptr_t)GetModuleHandle(nullptr)
// in offsets...
const uintptr_t print = rebase(0x11FBE70);
// in functions...
using _print = int(__fastcall*)(int, const char*, ...);
inline auto print = (_print)offsets::print; // offsets::print - path to your offset
```
Usage:
```c++
print(1, "some text");
```
First argument is the **style of your text**. There is:
- Information (0)
- Information (1)
- Warning (2)
- Error (3)
Second and last argument is your text.

# GetTaskScheduler
1. Search for string "**initialThermalStatus**".
2. Xref to it's call.
3. Scroll up until you find this:
```c++
sub_69420(); // this is our function
v33 = 1000.0 / v4;
```

# task.defer
Way 1:
1. Search for "**defer**".
2. Xref to last call.
3. Scroll all the way up and you got yourself the function
```c++
__int64 __fastcall sub_69420(__int64 a1)
{
  unsigned int v2; // er15
  __int64 v3; // rax
  __int64 v4; // rax
  __int64 v5; // rdi
  bool v6; // bp
  __int64 v7; // r14
  __int64 v8; // rbx
  __int64 v9; // rax
  __int64 v10; // r14
  __int64 v11; // rax
  __int64 v12; // rax
  __int64 v13; // rax
  // ...
}
```
Way 2:
1. Search for "**Maximum re-entrancy depth (%i) exceeded calling task.defer**"
2. Xref to it's call.
3. Scroll all the way up and it's the function itself (like before)

# LuaVM_Load
There is a few ways to find it, you can choose the one you think is easier for you.

Way 1:
1. Search for "**for k,v in pairs(t1) do\n**"
2. Xref to it's call.
3. Scroll to bottom **else** statement
```c++
sub_1405F4D10(
      v73,
      "oldResult, moduleRef  = ...\n"
      "newResult = require(moduleRef)\n"
      "t1 = newResult\n"
      "t2 = oldResult\n"
      "if type(t2) ~= \"table\" then return end\n"
      "for k,v in pairs(t2) do\n"
      "    if type(t1[k]) == \"nil\" then \n"
      "        t2[k] = nil\n"
      "    end\n"
      "end\n"
      "for k,v in pairs(t1) do\n"
      "    t2[k] = v\n"
      "end\n");  // we start here
    v30 = *(_QWORD *)(sub_141275950(&v59, v73) + 8);
    if ( dword_1452FFF58 )
    {
      v32 = sub_142C58B50();
      v33 = qword_145363F48;
      v34 = sub_142C79B60();
      *(_QWORD *)sub_142C79A30(&word_145571BA8, v34, v33) = v32;
      v31 = luavm_load(v20, v30 + 16, (__int64)&unk_1444D9B67, 0);
      v35 = qword_145363F48;
      v36 = sub_142C79B60();
      *(_QWORD *)sub_142C79A30(&word_145571BA8, v36, v35) = 0i64;
      v29 = v77;
    }
    else
    {
      v31 = sub_69420(v20, v30 + 16, (__int64)&unk_1444D9B67, 0); // this is our luavm_load
    }
```

Way 2:
1. Search for "**syntax error: %s**"
2. Xref to it's call.
3. Scroll up until you find **else** statement like before
```c++
else
    {
      v31 = sub_69420(v20, v30 + 16, (__int64)&unk_1444D9B67, 0); // here is our luavm_load
    }
    sub_140783730((char *)&v59 + 8);
    sub_140783730(&v59);
    if ( v31 )
    {
      v56 = sub_140A63830(v20, 0xFFFFFFFFi64);
      sub_1405F4D10(v60, v56);
      v57 = (const char *)sub_1405F4A20(v60);
      sub_142C67300("syntax error: %s", v57); // we start here
    }
```

# DecryptLuaState
1. Search for "**Script Start**"
2. Xref to it's call.
3. Scroll up until you see something like this:
```c++
v30 = sub_69420((_DWORD *)(v29 + 136)); // this is our function
sub_140A7C200(v298, v30, "Script Start");
```
