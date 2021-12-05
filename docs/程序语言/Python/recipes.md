如何保证image经过padding后宽和高可以被32或16整除

```
pad = 31  #	被
new_height = (height | pad) + 1
new_width = (width | pad) + 1
```

