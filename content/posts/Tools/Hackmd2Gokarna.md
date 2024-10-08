---
lang: zh-TW
title: "Hackmd2Gokarna"
tags: [ "Tools", "Python" ]
authors: [ "XinShou" ]
type: post
showTableOfContents: false
date: 2024-08-01
---

## 我懶得再重寫一次筆記，就直接轉吧!!

> 將 Hackmd 中的筆記內容轉為 Gokarna 相容的顯示樣式

{{< spoiler "範例轉換來源 Example.md" >}}

`````markdown {linenos=inline}
---
lang: zh-TW
title: "FHSH X AIS3 Club 挑戰賽 Writeup"
tags: [ "Cyber Security", "Competition", "CTF Writeup" ]
authors: [ "鄭", "salt", "XinShou" ]
type: post
showTableOfContents: true
date: 2024-06-08
---

<!-- Hackmd Only -->

# FHSH X AIS3 Club 挑戰賽 Writeup

:::spoiler Categories
[TOC]
:::
<!-- /Hackmd Only -->

> Team – [ntihs](https://ctfd.fhh4ck3rs.taipei/teams/16)  
> Member – cheng, salt, wasami, xinshou

---

:::spoiler Scoreboard／Challenge
![Scoreboard](https://hackmd.io/_uploads/S19f1-_rR.png)
![Challenges](https://hackmd.io/_uploads/BJJWyZuSA.png)
:::

---

## MISC

### Welcome

> :::spoiler 原圖
> ![Challenge](https://hackmd.io/_uploads/HJe1-wlH0.png =500x)
> :::
> 這是 Sanity Check 喔！  
> 直接複製貼上卽可！  
> 找到flag 就是CTF 的精髓啦～  
> `FhCTF{W3cOmE_ch4113nger_e8a898e5be97e58aabe585a5e8999be693ace7a4bee59c98}`  
> 簡單的啦！  
> 三類組的毛病了..都想看實體心臟...
>
> Author: CXPh03n1x

#### 題解

無

:::info
Flag: `FhCTF{W3c0mE_ch4ll3nger_e8a898e5be97e58aa0e585a5e8999be693ace7a4bee59c98}`
:::

<br /> <br /> <br /> 

---

### Survey

> :::spoiler 原圖
> ![Challenge](https://hackmd.io/_uploads/BJN8heOrC.png "500px")
> :::
> 就是要來調查一下啦～  
> 請大家要回答喔～  
> 這題值5分  
> https://forms.gle/NzDoAgBEu2gbgN6NA
>
> Author: CXPh03n1x

#### 題解

表單填完就有了，或是偷吃步

```bash
$ curl https://docs.google.com/forms/d/e/1FAIpQLSc12s3nmeR4uoVqQrgHYCxOyO71viqTQKn9WVAdzjogM3Xhig/viewform 
| grep -E 'FhCTF{\S+}'
```

![image](https://hackmd.io/_uploads/HyCQhgdSR.png "700px")

:::info
Flag: `FhCTF{G00d_G4m3}`
:::

<br /> <br /> <br />

---

## FORENSICS

### Hex Dumb Dumb

> :::spoiler 原圖
> ![Challenge](https://hackmd.io/_uploads/BkBGHeVS0.png "500px")
> :::
> 我說，用 UTF-8 已經過時了！現在都用 6865786g6d616c  
> 但是總覺得..你會看不透XD
>
> Author: CXPh03n1x

#### 題解

使用`xxd`會發現怪怪的，FhC...，把首行出現的字母組合起來便是Flag
那個`＃`是底線 ．ｗ．

:::spoiler content

```
00040dd0: 0000 4646 4646 4646 0000 0000 0000 0000  ..FFFFFF........
00040de0: 0000 2929 2929 2929 0000 0000 0000 0000  ..))))))........
00040df0: 0000 0000 0066 0066 0000 0000 0000 0000  .....f.f........
# ...
00040f80: 0000 0000 0000 0029 0000 0000 0000 0000  .......)........
00040f90: 0000 0000 0000 0074 0000 0000 0000 0000  .......t........
00040fa0: 0000 5454 5454 5474 0000 0000 0000 0000  ..TTTTTt........
```

:::

:::info
Flag: `FhCTF{H3xdump_n33d_m0R3_S3CUR3}`
:::

<br /> <br /> <br />

---

### Do you know packet?

> :::spoiler 原圖
> ![Challenge](https://hackmd.io/_uploads/rk27IgNHC.png "500px")
> :::
> I receive a PCAP file. But...., what it means?
>
> Author: Scott

#### 題解

1. 看到很多USB的封包，猜是要來解並分析內容，先過濾需要被分析的封包
   `((usb.transfer_type == 0x01) && (frame.len == 35)) && !(usb.capdata == 00:00:00:00:00:00:00:00)`
   ![image](https://hackmd.io/_uploads/BJ1wEwESC.png)

2. 將 HID Data 導出並使用腳本分析

:::spoiler code

```python=
BOOT_KEYBOARD_MAP = {
    0x00: (None, None),  # Reserved (no event indicated)
    0x01: ('', ''),  # ErrorRollOver
    # ...
    0x73: ('[F24]', '[F24]'),  # F24
}


def parse_boot_keyboard_report(data: bytearray):
    # 數據解析
    # ...

if __name__ == '__main__':
    lines = "001af8001a00f8ff,0024f9002400f9ff,0028fa002800faff,0030f9003000f9ff,0034fb003400fbff,0032fa003200faff,002efa002e00faff,002cfb002c00fbff,0023fc002300fcff,001dfb001d00fbff,0016fc001600fcff,000ffe000f00feff,0008fd000800fdff,0004fe000400feff,0000ff000000ffff,0000ff000000ffff,00ff0000ffff0000,0000ff000000ffff,0000ff000000ffff,00ffff00ffffffff,0000ff000000ffff,00ff0000ffff0000,0100000000000000,0000000000000000,0200000000000000,0200090000000000,0200000000000000,0000000000000000,00000b0000000000,0000000000000000,0200000000000000,0200060000000000,0200000000000000,0200170000000000,0200000000000000,0200090000000000,0200000000000000,02002f0000000000,0200000000000000,0000000000000000,0000040000000000,0000000000000000,0200000000000000,02002d0000000000,0200000000000000,0000000000000000,0200000000000000,0200160000000000,0200000000000000,0000000000000000,00001e0000000000,0000000000000000,0000100000000000,0000000000000000,0000130000000000,0000000000000000,00001e0000000000,0000000000000000,0000200000000000,0000000000000000,0200000000000000,02002d0000000000,0200000000000000,0200180000000000,0200000000000000,0200160000000000,0200000000000000,0200050000000000,0200000000000000,0000000000000000,0200000000000000,02002d0000000000,0200000000000000,0200060000000000,0200000000000000,0000000000000000,0200000000000000,02001f0000000000,0200000000000000,0000000000000000,0000130000000000,0000000000000000,0000240000000000,0000000000000000,0000180000000000,0000000000000000,0200000000000000,0200150000000000,0200000000000000,0000000000000000,0000080000000000,0000000000000000,0200000000000000,0200300000000000,0200000000000000,0000000000000000,00fafe00fafffeff,00effa00effffaff,00eefa00eefffaff,00eefa00eefffaff,00ecfa00ecfffaff,00edfc00edfffcff,00effc00effffcff,00f3fc00f3fffcff,00f2fa00f2fffaff,00f3fa00f3fffaff,00f1f900f1fff9ff,00f4fa00f4fffaff,00f3f900f3fff9ff,00f3fb00f3fffbff,00f4fb00f4fffbff,00f6fc00f6fffcff,00f9ff00f9ffffff,00fb0000fbff0000,00fb0000fbff0000,00fb0000fbff0000,00f60000f6ff0000,00f20000f2ff0000,00f10000f1ff0000,00edfe00edfffeff,00ecfd00ecfffdff,00ebfc00ebfffcff,00ecfc00ecfffcff,00edfb00edfffbff,00effc00effffcff,00f0fa00f0fffaff,00f2f900f2fff9ff,00f3fa00f3fffaff,00f7fc00f7fffcff,00f7fb00f7fffbff,00f9fc00f9fffcff,00fbfe00fbfffeff,00fcfe00fcfffeff,00ff0000ffff0000,00fd0000fdff0000,00fe0000feff0000,00fa0300faff0300,00f40400f4ff0400,00f20600f2ff0600,00f10800f1ff0800,00f30900f3ff0900,00f80600f8ff0600,00fa0800faff0800,00fd0100fdff0100,0000010000000100,00fdff00fdffffff,00fffe00fffffeff,00ff0000ffff0000,0002000002000000,0005000005000000,0006ff000600ffff,0006fe000600feff,000cfe000c00feff,000afc000a00fcff,000ffb000f00fbff,0011f9001100f9ff,0015f7001500f7ff,0014fa001400faff,0016f9001600f9ff,0017fb001700fbff,0015fb001500fbff,000ffc000f00fcff,0007fb000700fbff,0003f9000300f9ff,0000fb000000fbff,00fbf900fbfff9ff,00f5f700f5fff7ff,00eff900effff9ff,00ecf800ecfff8ff,00e6f800e6fff8ff,00e0f900e0fff9ff,00d6f800d6fff8ff,00d3f700d3fff7ff,00cff700cffff7ff,00cdf700cdfff7ff,00caf700cafff7ff,00c9f900c9fff9ff,00cdf700cdfff7ff,00d1f800d1fff8ff,00ddf900ddfff9ff,00e7fa00e7fffaff,00f1fa00f1fffaff,00fafa00fafffaff,00fefe00fefffeff,0000ff000000ffff,0000ff000000ffff,0001000001000000,0001000001000000,0001010001000100,0003040003000400,0004050004000500,0004050004000500,0005040005000400,0003030003000300,0004030004000300,0004030004000300,0005030005000300,0006020006000200,0008030008000300,0005010005000100,0005020005000200,0006010006000100,0007020007000200,0009010009000100,0008030008000300,0009020009000200,0007020007000200,0007030007000300,0009020009000200,0007030007000300,000c03000c000300,000a03000a000300,000c04000c000400,000d04000d000400,000a04000a000400,0007020007000200,0005010005000100,0005010005000100,0007020007000200,0005000005000000,0007010007000100,000a01000a000100,000c00000c000000,000d01000d000100,0011010011000100,0010010010000100,000e01000e000100,000c00000c000000,000a00000a000000,0001000001000000,0000020000000200,00ff0000ffff0000,00ff0000ffff0000,0000010000000100,0000010000000100,0001000001000000,0001000001000000,0001000001000000,0002010002000100,0002010002000100,0003020003000200,0002010002000100,0001010001000100,0001010001000100,0001000001000000,0001010001000100,0001000001000000,0001000001000000,0001000001000000,0001000001000000,0001010001000100,0002010002000100,0003010003000100,0002010002000100,0003020003000200,0002010002000100,0002010002000100,0001000001000000,0001000001000000,0001000001000000,0001000001000000,0001000001000000,0000020000000200,0001020001000200,0000020000000200,0001020001000200,0000030000000300,0001020001000200,0000020000000200,0000010000000100,0000030000000300,0000020000000200,0000010000000100,0000010000000100,0001000001000000,0001000001000000,0001010001000100,0100000000000000,0000000000000000".split(',')
    # ...
    print(f'Raw output:\n{text.__repr__()}')
    print(f'Text output:\n{text}')


>>>
"""
Raw output:
"w7\n]'= 6zsleaFhCTF{a_S1mp13_USB_C@p7uRe}aacceeffcceebcciglnrqstrldaababbababaaabcebbcdfefddfdigaiaajaagadbbdbdgijnmkig"
Text output:
w7
]'= 6zsleaFhCTF{a_S1mp13_USB_C@p7uRe}aacceeffcceebcciglnrqstrldaababbababaaabcebbcdfefddfdigaiaajaagadbbdbdgijnmkig
"""
```

:::

:::info
Flag: `FhCTF{a_S1mp13_USB_C@p7uRe}`
:::

<!-- Hackmd Only -->

---

> [name=salt]\
> [name=鄭] \
> [name=XinShou]\
> [name=wasami]

---

<style>
    .markdown-body p {
        font-size: 18px;
    }
</style>

<!-- /Hackmd Only -->
`````

{{< /spoiler >}}

### 腳本

```py {linenos=inline}
#!/usr/bin/env python3
# 將 Hackmd 中的筆記內容轉為 Gokarna 相容的顯示樣式
# Usage: ./Hackmd2blog.py ./target.md ./output.md
# Author: XinShou

import sys
import re


class Hackmd2blog:
    file_path: str
    content: str = None

    def __init__(self, file_path):
        self.file_path = file_path
        self.read_file()
        self.remove_hackmd_only_sections()
        self.remove_placeholder()
        self.convert_flag_info_spoiler()
        self.convert_spoiler()
        self.convert_code_block()
        self.convert_image_size_format()
        self.content = self.content.strip()

    # def parseTitle(self):
    #     extracted_text = re.search(r'^---(.*?)---', self.content, re.DOTALL)
    #     if not extracted_text:
    #         raise RuntimeError
    #
    #     extracted_text = extracted_text.group(1).strip()
    #     return extracted_text
    def read_file(self):
        with open(self.file_path, 'r') as f:
            self.content = f.read().strip()

    def remove_hackmd_only_sections(self):
        """
        Removes sections marked explicitly for Hackmd Only from the provided text.
        """
        self.content = re.sub(r'<!-- Hackmd Only -->.*?<!-- /Hackmd Only -->', '', self.content, flags=re.DOTALL)

    def convert_spoiler(self):
        """
        Converts Hackmd spoiler syntax to Hugo spoiler shortcode syntax.
        """
        converted_text = re.sub(r':::spoiler (.*)\n', r'{{< spoiler "\1" >}}\n', self.content)
        converted_text = re.sub(r':::\n', r'{{< /spoiler >}}\n', converted_text)
        self.content = converted_text

    def convert_flag_info_spoiler(self):
        """
        Converts Hackmd info block to Markdown blockquote.
        """
        self.content = re.sub(r':::info\n(.*)\n:::', r'> \1', self.content)

    def convert_code_block(self):
        """
        Converts standard Markdown code blocks to code blocks with inline line numbers.
        """
        self.content = re.sub(r'```([a-z]+)=', r'```\1 {linenos=inline}', self.content)

    def remove_placeholder(self):
        self.content = self.content.replace('<br />', '')

    def convert_image_size_format(self):
        """
        Converts Markdown image links from size format '=500x' to ' "500px"'.
        """
        self.content = re.sub(r'\(https://hackmd.io/_uploads/([^ ]+) =(\d+)x\)',
                              r'(https://hackmd.io/_uploads/\1 "\2px")', self.content)


if __name__ == '__main__':
    if len(sys.argv) != 3:
        print("Usage: ./hackmd2blog target.md output.md")
        sys.exit(1)

    input_file = sys.argv[1]
    output_file = sys.argv[2]

    h2b = Hackmd2blog(input_file)
    with open(output_file, 'w') as f:
        f.write(h2b.content)
```