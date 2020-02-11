---
layout: post
title: "限制系统支持字符"
description: ""
category: 
tags: []
---
{% include JB/setup %}
## 场景

在设计一个系统时，需要考虑系统支持（接受和输出）的语言类型及其对应的字符系统类型，拉丁字母类，中日韩类等特殊编码系统，或支持几乎所有的写系统（writing system）。

在系统接收字段限制和验证上也需要清楚罗列可接受的安全字符类型（白名单）或不接受的字符类型（黑名单）。题外话，黑名单很难将所有可能存在安全隐患的字符包含在内，而不影响正常的业务需求。

在以上场景下需要了解计算机系统可识别的字符及其分类，以及对应的编码方式。 

## 字符的分类

#### 按使用方式

可分为图形字符（graphic characters），格式字符（format characters)和控制字符（control characters）。图形字符分为脚本（script），表情符号（emoji），脚本包含字母和其他可写的代表文字信息的符号。

#### 按字符的长度

可分为全角字符和半角字符。汉字字符和规定了全角的英文字符及国标GB2312-80中的图形符号和特殊字符都是全角字符。一般的系统命令是不用全角字符的，只是在作文字处理时才会使用全角字符。那些包含既包含全角又包含半角的老的编码对应Unicode block的U+FF00–FFEF，其中有长度为两个半角字符的数字，英文字母和一些符号，参考：https://en.wikipedia.org/wiki/Halfwidth_and_Fullwidth_Forms_(Unicode_block)。 

#### 可按国家或语言分类有： 

- GB2312为中国的标准字符集，用于简体中文。 

- CLK：包含中文，日文和韩文的字符集。

- Unicode：包含几乎所有语言的字符。

#### 按Unicode的block分：

- 最开始的block（U+0000至U+007F）是Basic latin和C0 Controls，是Unicode中唯一一个UTF-8编码为一个字节的block。包含所有的英文字母（也是拉丁文），常见的标点字符和控制字符，与ASCII中定义的字符一一对应。

- 之前提到的全角半角格式字符也是Unicode中的一个block，叫做Halfwidth and Fullwidth Forms block。

- CJK block：中文，日文，韩文所包含字符。

- 拉丁文有多个补充字符集，Latin-1 supplement block中含有如拉丁文字符，版权标志等字符。Latin Extended-A，Latin Extended-B（包含拼音）等。 

- 希腊和埃及，蒙古，非洲，美国，Unicode符号，箭头，数学符号，圈起来的数字字母（enclosed alphanumerics），还有各种混杂的符号如表情，播放符号，音乐符号等。

## 编码系统举例

一个编码系统对应一个字符集，一个字符集可能可被多个编码系统编码。

UTF-8（8-[bit](https://en.wikipedia.org/wiki/Bit) [Unicode Transformation Format](https://en.wikipedia.org/wiki/Unicode_transformation_format))，一个字符对应8个bit，称为一个字节（byte），一个全角字符即占位两个字节，一个CJK（Chinese, Japanese, Korean）字符占三个字节。是处理Unicode的编码规则。

EUC-CN(Extended Unix Code for Chinese)是处理GB2312字符组的多字节编码系统（multibyte character encoding system）。相对UTF-8在存储上更高效，一个CJK字符用EUC-CN编码只占两个字节。同在EUC下的还有EUC-JP, EUC-KR, EUC-TW（用于台湾繁体字，不常用，常用的是Big5）。

HZ是另一个处理GB2312字符组的编码系统。

Big5: 用于台湾，香港和澳门的繁体字符编码标准。

## 为什么编码

编码是将人类可读或不可读字符翻译为机器可读字符的过程。一个混合了未编码和编码后的字符串在经过转换后机器都可识别，如"{}|~\u201C”。编码也是一个类似将手写体转为标准体的过程，避免信息异议。一个肉眼看似一样的字符可能是不同的字符，如多个空格和一个tab，一个全角英文字母和一个半角英文字母加一个空格。也是 

## ASCII码和UTF-8的关系

ASCII是信息交换的美国标准码，是一个电子通信中字符编码的标准。ASCII码代表计算机，电信设备（telecommunications equipment）和其他设备。大部分现代的字符编码原理都基于ASCII，虽然他们支持更多其他的字符。https://en.wikipedia.org/wiki/ASCII 

UTF-8向下兼容[ASCII](https://en.wikipedia.org/wiki/ASCII). Unicode中的前128个字符和ASCII一一对应，编码使用一个字节，并和ASCII对应的字节完全相等，因此一个合法的ASCII文字也是合法的UTF-8编码的Unicode。

ASCII包含的字符为Basic latin或控制字符。

## 最后

需要根据自己系统支持的语言以及对应可能用到的文字符号，表情，和控制符入手，找出可用的字符集作为白名单来限制用户或黑客的输入，即可。

## 参考文章

**Encoding**: **Character encoding** is used to represent a repertoire of [characters](https://en.wikipedia.org/wiki/Character_(computing)) by some kind of [encoding](https://en.wikipedia.org/wiki/Code) system. Depending on the [abstraction level](https://en.wikipedia.org/wiki/Abstraction_level) and context, corresponding [code points](https://en.wikipedia.org/wiki/Code_point) and the resulting code space may be regarded as [bit patterns](https://en.wikipedia.org/wiki/Bitstream), [octets](https://en.wikipedia.org/wiki/Octet_(computing)), natural [numbers](https://en.wikipedia.org/wiki/Number), electrical pulses, etc. Ref:  https://en.wikipedia.org/wiki/Character_encoding 

**Writing system**: A **writing system** is a method of visually representing verbal [communication](https://en.wikipedia.org/wiki/Communication). While both [writing](https://en.wikipedia.org/wiki/Writing) and [speech](https://en.wikipedia.org/wiki/Spoken_language) are useful in conveying [messages](https://en.wikipedia.org/wiki/Message), writing differs in also being a reliable form of [information](https://en.wikipedia.org/wiki/Information) storage and [transfer](https://en.wikipedia.org/wiki/Information_transfer). Writing systems require shared understanding between writers and [readers](https://en.wikipedia.org/wiki/Reading_(process)) of the meaning behind the sets of [characters](https://en.wikipedia.org/wiki/Character_(symbol)) that make up a script. Including Logographic systems(One character represent one word, eg. Traditional Chinese Characters), Syllabic systems: syllabary(to make up words); Segmental system:alphabets; Featural systems; Ambiguous systems. https://en.wikipedia.org/wiki/Writing_system 

**Unicode**: A computing industry standard for the consistent [encoding](https://en.wikipedia.org/wiki/Character_encoding), representation, and handling of [text](https://en.wikipedia.org/wiki/Character_(computing)) expressed in most of the world's [writing systems](https://en.wikipedia.org/wiki/Writing_system). The standard is maintained by the [Unicode Consortium](https://en.wikipedia.org/wiki/Unicode_Consortium). and as of May 2019 the most recent version, *Unicode 12.1*, contains a repertoire of 137,994 [characters](https://en.wikipedia.org/wiki/Character_(computing)) (consisting of 137,766 graphic characters, 163 format characters and 65 control characters) covering 150 modern and historic [scripts](https://en.wikipedia.org/wiki/Script_(Unicode)), as well as multiple symbol sets and [emoji](https://en.wikipedia.org/wiki/Emoji). https://en.wikipedia.org/wiki/Unicode, https://en.wikipedia.org/wiki/List_of_Unicode_characters 

**UTF-8**: UTF-8 (8-[bit](https://en.wikipedia.org/wiki/Bit) [Unicode Transformation Format](https://en.wikipedia.org/wiki/Unicode_transformation_format)) is a [variable width](https://en.wikipedia.org/wiki/Variable-width_encoding) [character encoding](https://en.wikipedia.org/wiki/Character_encoding) capable of encoding all 1,112,064[[nb 1\]](https://en.wikipedia.org/wiki/UTF-8#cite_note-1) valid [code points](https://en.wikipedia.org/wiki/Code_point) in [Unicode](https://en.wikipedia.org/wiki/Unicode) using one to four 8-bit [bytes](https://en.wikipedia.org/wiki/Byte). The encoding is defined by the Unicode Standard, and was originally designed by [Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson) and [Rob Pike](https://en.wikipedia.org/wiki/Rob_Pike). 

**Halfwidth and fullwidth forms**: https://en.wikipedia.org/wiki/Halfwidth_and_fullwidth_forms 

**GB2312**: https://en.wikipedia.org/wiki/GB_2312 
