# QuanTangshi
全唐诗

# code
``` python
import os
import re

kmap = {}
karr = []
n = 0
with open("qts.txt", "r", encoding='utf-8') as f:
    arr = f.read().split('。')
    for i in arr:
        ii = re.sub(r'[<>:"/\\|?*]', '', i)
        ii = ii.strip().replace("\n", "").replace("\r\n", "").replace(" ", "")
        if ii not in kmap and ii != "" and ord(ii[0]) > 128 and len(ii) < 50 and ii[0] != '、'\
                and ii[0] != '、' and ii[0] != '：' and ii[0] != '□':
            kmap[ii] = n
            karr.append(ii)
            n += 1
        if n >= 65536:
            break

for key, val in kmap.items():
    filename = "data/{}/{}/{}.txt".format(key[0], key[1], key)
    os.makedirs(os.path.dirname(filename), exist_ok=True)
    with open(filename, "w") as f:
        f.write(str(val))

for i in range(len(karr)):
    filename = "data/dic/{}/{}/{}.txt".format(i & 0xF000, i & 0xF00, i & 0xFF)
    os.makedirs(os.path.dirname(filename), exist_ok=True)
    with open(filename, "w", encoding='utf-8') as f:
        f.write(karr[i])

```
