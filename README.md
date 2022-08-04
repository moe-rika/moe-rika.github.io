# QuanTangshi
全唐诗

# code
``` python
import json

kmap = {}
karr = []
n = 0
with open("qts.txt", "r", encoding='utf-8') as f:
    arr = f.read().split('。')
    for i in arr:
        ii = i.strip()
        if ii not in kmap:
            kmap[ii] = n
            karr.append(ii)
            n += 1
        if n >= 65536:
            break

with open("qts2.json", "w", encoding='utf-8') as f:
    f.write(json.dumps(kmap, ensure_ascii=False))

with open("qts3.json", "w", encoding='utf-8') as f:
    f.write(json.dumps(karr, ensure_ascii=False))
```
