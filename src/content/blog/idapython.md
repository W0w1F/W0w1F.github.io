---
title: 'Example Post Nine'
description: 'Final sample post using example hero images.'
pubDate: 'Aug 03 2026'
category: 'Showcase'
tags: ['template']
---

以下是 **IDA 8.x / IDAPython 3.x（Python 3）** 中最常用的函数与对应模块整理，适用于 IDA 8.3 版本，涵盖：

- 地址读取与写入
- 函数/段处理
- 注释添加
- 名称重命名
- 导入符号使用

------

## ✅ 一、地址相关读写（来自 `ida_bytes`）

| 功能       | 函数                               | 示例                                          |
| ---------- | ---------------------------------- | --------------------------------------------- |
| 读字节     | `get_byte(ea)`                     | `b = ida_bytes.get_byte(0x401000)`            |
| 写字节     | `patch_byte(ea, val)`              | `ida_bytes.patch_byte(0x401000, 0x90)`        |
| 读字       | `get_word(ea)`                     | `w = ida_bytes.get_word(0x401000)`            |
| 写字       | `patch_word(ea, val)`              | `ida_bytes.patch_word(0x401000, 0x1234)`      |
| 读双字     | `get_dword(ea)`                    | `d = ida_bytes.get_dword(0x401000)`           |
| 写双字     | `patch_dword(ea, val)`             | `ida_bytes.patch_dword(0x401000, 0xDEADBEEF)` |
| 设置未定义 | `del_items(ea, flags)`             | `ida_bytes.del_items(0x401000, 0)`            |
| 读字节序列 | `ida_bytes.get_bytes(start, size)` | 返回bytes                                     |

------

## ✅ 二、段操作（来自 `ida_segment`）

| 功能           | 函数                  | 示例                                     |
| -------------- | --------------------- | ---------------------------------------- |
| 获取段起始地址 | `get_segm_start(ea)`  | `start = ida_segment.get_segm_start(ea)` |
| 获取段结束地址 | `get_segm_end(ea)`    | `end = ida_segment.get_segm_end(ea)`     |
| 获取段名       | `get_segm_name(sega)` | `name = ida_segment.get_segm_name(ea)`   |

------

## ✅ 三、函数处理（来自 `ida_funcs`）

| 功能           | 函数                   | 示例                                         |
| -------------- | ---------------------- | -------------------------------------------- |
| 创建函数       | `add_func(start, end)` | `ida_funcs.add_func(0x401000, 0x401100)`     |
| 删除函数       | `del_func(start)`      | `ida_funcs.del_func(0x401000)`               |
| 判断是否为函数 | `is_func(Flags(ea))`   | `ida_funcs.is_func(ida_bytes.get_flags(ea))` |

------

## ✅ 四、符号名操作（来自 `ida_name`）

| 功能     | 函数                   | 示例                                     |
| -------- | ---------------------- | ---------------------------------------- |
| 设置名字 | `set_name(ea, "name")` | `ida_name.set_name(0x401000, "my_func")` |
| 获取名字 | `get_name(ea)`         | `ida_name.get_name(0x401000)`            |

------

## ✅ 五、注释操作（来自 `ida_bytes`）

| 功能     | 函数                                       | 示例                                              |
| -------- | ------------------------------------------ | ------------------------------------------------- |
| 设置注释 | `set_cmt(ea, "comment", repeatable=False)` | `ida_bytes.set_cmt(0x401000, "decrypt start", 0)` |
| 获取注释 | `get_cmt(ea, repeatable)`                  | `ida_bytes.get_cmt(0x401000, 0)`                  |

------

## ✅ 六、当前地址、跳转、日志（来自 `idc`, `ida_kernwin`）

| 功能           | 函数                                          | 示例                                         |
| -------------- | --------------------------------------------- | -------------------------------------------- |
| 寄存器值       | `idc.get_reg_value('reg')`                    | `eax = idc.get_reg_value('eax')`             |
| 内存值         | `idc.get_wide_dword(ea)`// 带wide都是unsigned | `op =idc.get_wide_dword(ea)`                 |
|                |                                               |                                              |
|                |                                               |                                              |
| 当前地址       | `here()`                                      | `ea = idc.here()`                            |
| 输出消息       | `print(...)` 或 `ida_kernwin.msg()`           | `ida_kernwin.msg("info: %x\n", ea)`          |
| 跳转           | `jumpto(ea)`                                  | `idc.jumpto(0x401000)`                       |
| 获取指令助记符 | `idc.print_insn_mnem(ea)`                     |                                              |
| 获取指令操作数 | `idc.get_operand_value(ea, n)`                | 0为第一个操作数，返回数或偏移值              |
| 判断操作数类型 | `idc.get_operand_type(ea, n)`                 | 0为第一个操作数，返回值为`idc.o_imm`为立即数 |

------

## ✅ 七、辅助模块说明（按功能分类）

| 模块          | 功能                               |
| ------------- | ---------------------------------- |
| `ida_bytes`   | 所有的字节读写、补丁操作           |
| `ida_funcs`   | 函数创建、删除、判断               |
| `ida_segment` | 段操作（如获取段名/起止地址）      |
| `ida_name`    | 地址命名与重命名                   |
| `ida_kernwin` | UI 日志、交互消息                  |
| `idc`         | 高层封装（兼容性好，简单任务可用） |

------

## ✅ 八、辅助

idaapi

* `idaapi.process_ui_action("msglist:Clear")` 清空output窗口信息

------



## ✅ 示例：解密 `.data` 段并重命名函数

```python
import ida_segment, ida_bytes, ida_name

start = ida_segment.get_segm_by_name(".data").start_ea
end = ida_segment.get_segm_by_name(".data").end_ea

for ea in range(start, end):
    val = ida_bytes.get_byte(ea)
    ida_bytes.patch_byte(ea, val ^ 0xC)

ida_name.set_name(start, "decrypted_data")
print("解密完成")
```

------

将所有从.h中parse的结构体全部从Load Types进入Structures

```python
import idaapi

idati = idaapi.cvar.idati
qty = idaapi.get_ordinal_qty(idati)
print(f"[+] Found {qty} local types")

for ordinal in range(1, qty):
    name = idaapi.get_numbered_type_name(idati, ordinal)
    if not name:
        continue

    result = idaapi.get_numbered_type(idati, ordinal)
    if not result:
        continue

    # IDA 8.3: get_numbered_type 返回 (type, fields, fieldcmts, sda)
    if isinstance(result, tuple):
        # 只取前 3 个参数供 deserialize 使用
        type_ptr, fields, fieldcmts = result[:3]
    else:
        continue

    tinfo = idaapi.tinfo_t()
    if tinfo.deserialize(idati, type_ptr, fields, fieldcmts):
        if tinfo.is_struct():
            print(f"[+] Creating struct: {name}")
            # ✅ 传入 til, ordinal, name
            idaapi.import_type(idati, ordinal, name)

print("[+] Done importing all struct types to 'Structures' window.")

```



读取某段区间的字符串

```python
import idautils, idc, idaapi
func = idaapi.get_func(here())
START = func.start_ea
END = func.end_ea
str_num = 0
ea = START
while ea < END:
    if idc.print_insn_mnem(ea) == "lea":
        op = idc.get_operand_value(ea, 1)
        if idc.get_segm_name(op) in (".rdata", ".data", ".rodata"):
            s = idc.get_strlit_contents(op, -1, idc.STRTYPE_C)
            if s:
                print(f"{hex(ea)} => {s.decode('utf-8', errors='ignore')}")
                str_num += 1
    ea = idc.next_head(ea)
print(str_num)
print('done')

```



自动找到`__Pyx_CreateStringTabAndInitStrings`函数改名，创建`__pyx_mstate_global`结构体

```python
import ida_typeinf
import idautils,idc

def find_str_xrefs_simple(search_str):
    ea = 0
    # 搜索字符串
    while True:
        ea = ida_search.find_text(ea, 0, 0, search_str, ida_search.SEARCH_DOWN)
        print(f"\n找到字符串地址: {hex(ea)}")
        # 获取交叉引用
        for xref in idautils.XrefsTo(ea, 0):
            print(f"被引用地址: {hex(xref.frm)}")
            return xref.frm
        ea +=1

def rename_function_at_address(address, new_name):
    # 获取函数起始地址
    func = ida_funcs.get_func(address)
    if not func:
        print(f"地址 {hex(address)} 不在任何函数中")
        return False

    # 获取当前函数名称
    current_name = idc.get_func_name(func.start_ea)
    print(f"当前函数名称: {current_name}")

    # 尝试重命名函数
    if idc.set_name(func.start_ea, new_name):
        print(f"函数 {current_name} 已重命名为 {new_name}")
        return True
    else:
        print(f"无法重命名函数 {current_name} 为 {new_name}")
        return False


def get_function_name_at_address(address):
    # 获取函数起始地址
    func = ida_funcs.get_func(address)
    if not func:
        return None

    # 获取函数名称
    func_name = idc.get_func_name(func.start_ea)
    return func_name

def get_function_address_by_name(func_name):
    # 获取函数的起始地址
    func_ea = idc.get_name_ea_simple(func_name)
    if func_ea == idc.BADADDR:
        print(f"未找到函数: {func_name}")
        return None
    else:
        print(f"函数 {func_name} 的地址是: {hex(func_ea)}")
        return func_ea

# 使用示例

def add_struct_from_c(struct_str):
    # 解析C代码
    til = ida_typeinf.get_idati()  # 获取当前类型库

    # 尝试解析并添加类型
    result = ida_typeinf.parse_decls(til, struct_str, None, 0)
    print("成功添加结构体定义")
    return result


def print_strings_in_function(func_addr):
    string_list = []
    # 获取函数对象
    func = ida_funcs.get_func(func_addr)
    if not func:
        print("未找到指定地址的函数")
        return

    ea = func.start_ea
    flag = False
    while ea < func.end_ea:
        if idc.print_insn_mnem(ea) == "lea":
            op = idc.get_operand_value(ea, 1)
            if idc.get_segm_name(op) in (".rdata", ".data", ".rodata"):
                s = idc.get_strlit_contents(op, -1, idc.STRTYPE_C)
                if s == b'?':
                    flag = True
                    ea = idc.next_head(ea)
                    continue
                if s and flag:
                    print(f"{hex(ea)} => {s.decode('utf-8', errors='ignore')}")
                    s = 'str_'+ s.decode('utf-8')
                    if '.' in s:
                        s = s.replace('.','_')
                    string_list.append(s)
        ea = idc.next_head(ea)
    return string_list


# 使用示例
if __name__ == '__main__':
    # 结构体定义
    struct_str = """
    struct __pyx_mstate_global {
    PyObject *__pyx_d;
    PyObject *__pyx_b;
    PyObject *__pyx_cython_runtime;
    PyObject *__pyx_empty_tuple;
    PyObject *__pyx_empty_bytes;
    PyObject *__pyx_empty_unicode;
    PyTypeObject *__pyx_CyFunctionType;
    """

    __pyx_mstate = """
    struct __pyx_mstate{
        struct __pyx_mstate_global *_;
    }
    """
    search_str = "__main__"
    # 使用示例
    address = find_str_xrefs_simple(search_str)
    function_name = get_function_name_at_address(address)
    # 使用示例
    address = get_function_address_by_name(function_name)  # 替换为你要重命名的函数地址
    new_function_name = "__Pyx_CreateStringTabAndInitStrings"  # 替换为新的函数名称
    rename_function_at_address(address, new_function_name)
    print("以找到对应关键函数并命名")

    string_list = print_strings_in_function(address)
    for i in string_list:
        struct_str += "PyObject* _{};\n".format(i)
    struct_str += "};"

    print(struct_str)

    # 添加结构体
    add_struct_from_c(struct_str)
    add_struct_from_c(__pyx_mstate)
print('done')
```

从`__Pyx_InitConstants`的`PyLong_FromString`和`PyLong_FromLong`提取数字与对应在`__pyx_mstate_global`的下标（注意0不会被提取，一般是第一个数）

```python
import idc
import idaapi
func = idaapi.get_func(here())
START = func.start_ea
END = func.end_ea
ea = START
while ea < END:
    if idc.print_insn_mnem(ea) == "mov":
        op = idc.get_operand_value(ea, 1)
        if idc.get_operand_type(ea, 1) == idc.o_imm:
            print(op, end='->')
        op2 = idc.get_operand_value(ea, 0)
        if idc.get_operand_type(ea, 0) == idc.o_displ:
            print(op2 >> 3, end=',  ')
    if idc.print_insn_mnem(ea) == "lea":
        op = idc.get_operand_value(ea, 1)
        if idc.get_segm_name(op) in (".rdata", ".data", ".rodata"):
            s = idc.get_strlit_contents(op, -1, idc.STRTYPE_C)
            if s:
                print(f"{s.decode('utf-8', errors='ignore')}" + "->", end='')
    ea = idc.next_head(ea)
print('done')
```





vm条件断点

```python
import idc
op1 = idc.get_reg_value('edx')
op2 = idc.get_reg_value('cl')
print(f'shr {hex(op1)} , {hex(op2)} = {hex((op1 >> op2) & 0xffffffff)}')

import idc
op1 = idc.get_reg_value('eax')
rbp = idc.get_reg_value('rbp')
tmp = rbp + 0xc0 - 0x9c
op2 = idc.get_wide_dword(tmp)
print(f'sub {hex(op1)} , {hex(tmp)} = {hex(op1 - op2)}')
```

