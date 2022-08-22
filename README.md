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
    print(len(arr))
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
    filename = "data/dic/{}/{}/{}.txt".format(i >> 12, (i >> 8) & 0xF, i & 0xFF)
    os.makedirs(os.path.dirname(filename), exist_ok=True)
    with open(filename, "w", encoding='utf-8') as f:
        f.write(karr[i])


```

``` html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <script>
        async function btn() {
            const encoder = new TextEncoder()
            view = encoder.encode(document.getElementById('data').value)
            const flag = view.length % 2;
            var pad = flag ? 1 : 0
            var padarr = new Uint8Array(pad)
            function concatTypedArrays(a, b) {
                var c = new (a.constructor)(a.length + b.length);
                c.set(a, 0);
                c.set(b, a.length);
                return c;
            }
            let pad_view = concatTypedArrays(view, padarr)
            let str_arr = new Array(Math.round(pad_view.length/2))
            document.getElementById('text').innerHTML = ""
            for (var i = 0; i < pad_view.length; i += 2) {
                await fetch("/data/dic/" + (pad_view[i] >> 4) + "/" + (pad_view[i] & 0xF) + "/" + pad_view[i + 1] + ".txt")
                    .then(a => a.text())
                    .then(a => str_arr[Math.round(i/2)] = a + "。");
            }
            document.getElementById('text').innerHTML = str_arr.join("<br />");
            if(flag)
            {
                document.getElementById('text').innerHTML += "<br />======="
            }
        }


    </script>
</head>

<body>

    <h2>吟诗一首</h2>
    <input type="text" id="data" name="data">
    <button id="btn">Try it</button>
    <script>
        document.getElementById("btn").onclick=async ()=>{
        await btn();
    }
    </script>
    <div id="text"></div>

</body>

</html>
```

```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <script>
        async function btn() {
            const str = document.getElementById('data').value
            const arr = str.trim().split(/\r?\n/);
            arr.forEach((element, index) => {
                arr[index] = element.slice(0, element.length - 1);
            });
            const flag = arr[arr.length - 1] === "======";
            const len = arr.length - (flag ? 1 : 0);
            let data = new Uint8Array(len * 2)
            document.getElementById('text').innerHTML = ""
            for (var i = 0; i < len; i++) {
                await fetch("/data/" + arr[i][0] + "/" + arr[i][1] + "/" + arr[i] + ".txt")
                    .then(response => {
                        if (!response.ok) {
                            return Promise.reject(response);
                        }
                        return response;
                    }
                    )
                    .then(a => a.text())
                    .then(a => 0 + a)
                    .then(a => { data[i * 2] = a >> 8; data[i * 2 + 1] = a & 0xFF;})
                    .catch(error => {
                        if (typeof error.json === "function") {
                            error.json().then(jsonError => {
                                document.getElementById('text').innerHTML += arr[i] + "<br>";
                                document.getElementById('text').innerHTML += ("Json error from API<br>");
                                document.getElementById('text').innerHTML += (jsonError);
                            }).catch(genericError => {
                                document.getElementById('text').innerHTML += arr[i] + "<br>";
                                document.getElementById('text').innerHTML += ("Generic error from API");
                                document.getElementById('text').innerHTML += (error.statusText);
                            });
                        } else {
                            document.getElementById('text').innerHTML += arr[i] + "<br>";
                            document.getElementById('text').innerHTML += ("Fetch error");
                            document.getElementById('text').innerHTML += (error);
                        }
                    });
            }
            if (flag) data = data.slice(0, len * 2 - 1);
            const decoder = new TextDecoder();
            const view = decoder.decode(data);
            document.getElementById('text').innerHTML += view;
        }
    </script>
</head>

<body>

    <h2>诗歌鉴赏</h2>
    <textarea style="display:block;width:100%" id="data" rows="20"></textarea>
    <button id="btn">Try it</button>
    <script>
        document.getElementById("btn").onclick = async () => {
            await btn();
        }
    </script>
    <div id="text"></div>

</body>

</html>
```
