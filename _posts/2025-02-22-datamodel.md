---
title: "How to update offsets in ballistic"
---
This tutorial is for people that are only starting making exploits and shows my way of getting datamodel. There is both external and internal ways and easy to implement. This one uses **visualengine** offset/pointer which is really useful for external and also allows you to get the datamodel :D
offsets:
```c++
inline constexpr uintptr_t visualengine = 0x5B68DC0; // changes
inline constexpr uintptr_t renderview_to_fake = 0x710; // rarely changes
inline constexpr uintptr_t fake_to_real = 0x1A8; // rarely changes
```
how to get it? Really simple.
External:
```c++
uintptr_t visual_engine = memory.read(memory.module_offset + offsets::visual_engine); // module_offset is mem::find_image() for antagonist pasters
uintptr_t fake_datamodel = memory.read(visual_engine + offsets::fake_datamodel);
uintptr_t datamodel = memory.read(fake_datamodel + offsets::datamodel); // tada
```
Internal:
```c++
#define cast(address) reinterpret_cast<uintptr_t*>(address)
#define repoint(address) *cast(address)
// ...
repoint(repoint(0x5A92FD0) + offsets::roblox::renderview_to_fake) + offsets::roblox::fake_to_real;
```
