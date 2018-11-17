---
title: "Wireshark可用的BOLT协议解析插件"
date: 2018-11-17T20:37:28+08:00
draft: false
---

https://github.com/leizhiyuan/bolt-dissector

自己平常看协议,经常看到眼花,所以写了一个解析 SOFARPC 中 BOLT 协议的 wiresharks 插件

Wireshark是排查网络问题最常用的工具，它已经内置支持了上百种通用协议，扩展性也好,对于私有协议,还是得自己写的.对 C 不熟,但是 Lua 看了一下语法,还是挺简单的.

插件是使用lua开发的，安装比较简单，以OS X平台为例：

1. 将协议解析脚本拷贝到/Applications/Wireshark.app/Contents/Resources/share/wireshark/ 目录
2. (非必须) 编辑init.lua文件，设置disable_lua = false，确保lua支持打开,默认是打开的.一般就不要操作了.
3. 在init.lua文件末尾增加,当然你也可以写全路径.
	```
		dofile("bolt.lua")
	```
4. 再次启动Wireshark，会对12200端口的数据流使用脚本解析，已经可以识别BOLT协议了。


参考文档

https://wiki.wireshark.org/LuaAPI


附上 lua脚本
目前只支持 boltv1,稍后看看改一下支持 boltv2和 tr,方便大家理解 和抓包.

```lua
---
--- Created by bystander.
--- DateTime: 2018/11/16 8:57 AM
---

-- declare the protocol
bolt_proto = Proto("bolt", "SOFA BOLT Protocol")

-- declare the value strings
local vs_proto = {
    [1] = "BOLT",
    [13] = "TR",
}

local vs_type = {
    [1] = "Request",
    [2] = "Request Oneway",
    [0] = "Response",
}

local vs_cmdcode = {
    [0] = "HeartBeat",
    [1] = "Request",
    [2] = "Response",
}

local vs_version2 = {
    [1] = "v1",
    [2] = "v2",
}

local vs_codec = {
    [1] = "HESSIAN_CODEC",
    [2] = "JAVA_CODEC",
    [11] = "PROTOBUF_CODEC",
}

local vs_responsestatus = {
    [00] = "Success",
    [01] = "Error",
    [02] = "Server Exception",
    [03] = "Unknown Exception",
    [04] = "Server Thread Pool Busy",
    [05] = "Error Communication",
    [06] = "No Processor",
    [07] = "Timeout Exception",
    [08] = "Client Send Error",
    [09] = "Codec Exception",
    [10] = "Connection Closed",
    [11] = "Server Serialize Exception",
    [12] = "Server DeSerialize Exception",
}

-- declare the fields
local f_proto = ProtoField.uint8("bolt.proto", "Protocode", base.Dec, vs_proto)
local f_type = ProtoField.uint8("bolt.type", "Type", base.Dec, vs_type)
local f_cmdcode = ProtoField.uint16("bolt.cmdcode", "Cmdcode", base.Dec, vs_cmdcode)
local f_version2 = ProtoField.uint8("bolt.version", "Version", base.Dec, vs_version2)
local f_req_id = ProtoField.uint32("bolt.reqid", "RequestID", base.DEC)
local f_codec = ProtoField.uint8("bolt.codec", "Codec", base.DEC, vs_codec)
local f_timeout = ProtoField.uint32("bolt.timeout", "Timeout", base.DEC)
local f_response_status = ProtoField.uint32("bolt.response.status", "Response Status", base.DEC, vs_responsestatus)
local f_classlen = ProtoField.uint16("bolt.classlen", "Classlen", base.DEC)
local f_headerlen = ProtoField.uint16("bolt.headerlen", "Headerlen", base.DEC)
local f_contentlen = ProtoField.uint32("bolt.contentlen", "Contentlen", base.DEC)
local f_class = ProtoField.string("bolt.class", "Class")
local f_header = ProtoField.bytes("bolt.header", "Header")
local f_content = ProtoField.bytes("bolt.content", "Content")

bolt_proto.fields = { f_proto, f_type, f_cmdcode, f_version2, f_req_id, f_codec, f_timeout, f_response_status,
                      f_classlen, f_headerlen, f_contentlen, f_class, f_header, f_content
}

function get_pdu_length(buffer)
    local offset = 0
    local proto = buffer(offset, 1):uint()
    offset = offset + 1
    -- BOLT
    if proto == 1 then
        local dataType = buffer(offset, 1):uint()

        if dataType == 1 or dataType == 2 then
            if buffer:len() < 22 then
                return 22
            end
            -- 继续
            local classLen = buffer(14, 2):uint64()
            local headerLen = buffer(16, 2):uint64()
            local contentLen = buffer(18, 4):uint64()
            return 22 + classLen + headerLen + contentLen
        end

        --response
        if dataType == 0 then
            if buffer:len() < 20 then
                return 20
            end
            -- 继续
            local classLen = buffer(12, 2):uint64()
            local headerLen = buffer(14, 2):uint64()
            local contentLen = buffer(16, 4):uint64()
            return 20 + classLen + headerLen + contentLen
        end
    end
end

-- create the dissection function
function bolt_proto.dissector(buffer, pinfo, tree)

    -- check the protocol
    local check_proto = buffer(0, 1):uint()
    if check_proto ~= 1 then
        return
    end

    -- Set the protocol column
    pinfo.cols['protocol'] = "BOLT"

    -- Reassembling packets into one PDU
    local pdu_len = get_pdu_length(buffer)
    if pdu_len > buffer:len() then
        pinfo.desegment_len = pdu_len - buffer:len()
        pinfo.desegment_offset = 0
        return
    end

    -- create the BOLT protocol tree item
    local t_bolt = tree:add(bolt_proto, buffer())
    local offset = 0

    local protocol = buffer(offset, 1):uint()
    offset = offset + 1
    t_bolt:add(f_proto, protocol)

    -- TB REMOTING
    if protocol == 13 then
        -- NOT easy
    end
    -- BOLT REMOTING
    if protocol == 1 then
        local dataType = buffer(offset, 1):uint()
        t_bolt:add(f_type, dataType)
        offset = offset + 1
        -- Set the info column to the name of the function
        local info = vs_proto[protocol] .. ":" .. vs_type[dataType]
        pinfo.cols['info'] = info

        -- request
        if dataType == 1 or dataType == 2 then
            t_bolt:add(f_cmdcode, buffer(offset, 2))
            offset = offset + 2
            t_bolt:add(f_version2, buffer(offset, 1))
            offset = offset + 1
            t_bolt:add(f_req_id, buffer(offset, 4))
            offset = offset + 4
            t_bolt:add(f_codec, buffer(offset, 1))
            offset = offset + 1
            t_bolt:add(f_timeout, buffer(offset, 4))
            offset = offset + 4
            t_bolt:add(f_classlen, buffer(offset, 2))
            local classLen = buffer(offset, 2):uint64():tonumber()
            offset = offset + 2
            t_bolt:add(f_headerlen, buffer(offset, 2))
            local headerLen = buffer(offset, 2):uint64():tonumber()
            offset = offset + 2
            t_bolt:add(f_contentlen, buffer(offset, 4))
            local contentLen = buffer(offset, 4):uint64():tonumber()
            offset = offset + 4

            t_bolt:add(f_class, buffer(offset, classLen))
            offset = offset + classLen
            t_bolt:add(f_header, buffer(offset, headerLen))
            offset = offset + headerLen
            t_bolt:add(f_content, buffer(offset, contentLen))
            offset = offset + contentLen
        end
        -- response
        if dataType == 0 then
            t_bolt:add(f_cmdcode, buffer(offset, 2))
            offset = offset + 2
            t_bolt:add(f_version2, buffer(offset, 1))
            offset = offset + 1
            t_bolt:add(f_req_id, buffer(offset, 4))
            offset = offset + 4
            t_bolt:add(f_codec, buffer(offset, 1))
            offset = offset + 1
            t_bolt:add(f_response_status, buffer(offset, 2))
            offset = offset + 2
            t_bolt:add(f_classlen, buffer(offset, 2))
            local classLen = buffer(offset, 2):uint64():tonumber()
            offset = offset + 2
            t_bolt:add(f_headerlen, buffer(offset, 2))
            local headerLen = buffer(offset, 2):uint64():tonumber()
            offset = offset + 2
            t_bolt:add(f_contentlen, buffer(offset, 4))
            local contentLen = buffer(offset, 4):uint64():tonumber()
            offset = offset + 4
            t_bolt:add(f_class, buffer(offset, classLen))
            offset = offset + classLen
            t_bolt:add(f_header, buffer(offset, headerLen))
            offset = offset + headerLen
            t_bolt:add(f_content, buffer(offset, contentLen))
            offset = offset + contentLen
        end
    end
end

-- we use 12200
tcp_table = DissectorTable.get("tcp.port")
-- register the protocol to port 12200
tcp_table:add(12200, bolt_proto)

```

