#php #scripts

```bash
#! /usr/bin/env php

<?php

$file = file_get_contents('/home/romaxa/.bash_aliases', true);

if($file){
 foreach(explode("\n", $file) as $row){
  if((substr($row,0,1) == '#')  && is_string($row)){
    echo substr($row,1) . PHP_EOL;
  }
 }
}else{
 echo "нет прописаных алиасов" . PHP_EOL;
}
```
---