# Iterate over files and search for a pattern

*Script*

```
#!/bin/bash

for filename in ./*.xml; do
    echo Checking $filename
    aux1=`grep -oE ' Name=\"(.*)\"' < $filename | cut -f 2 -d '"'`
    cp $filename $aux1.xml
done

echo Done!
```

*Remarks*

 - `grep -oE` searches ONLY for a pattern
 - `grep -oE < $fileName` is more efficient than `cat $filename | grep -oE`
 - See [this StackOverflow question](https://stackoverflow.com/questions/48945548/bash-script-for-searching-for-a-string-in-a-xml-file-and-rename-the-file-with-th/48946391#48946391)