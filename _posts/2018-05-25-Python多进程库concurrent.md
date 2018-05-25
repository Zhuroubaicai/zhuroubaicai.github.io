---
layout: post
title: Python 多进程库 concurrent 
---

```Python
from concurrent.futures import ProcessPoolExecutor

executor = ProcessPoolExecutor(max_workers=20)

futures = []
for i in range(100):
    futures.append(executor.submit(__foo, bar='bar'))

results = [future.result() for future in futures]


def __foo(bar):
    # do something
    pass
```