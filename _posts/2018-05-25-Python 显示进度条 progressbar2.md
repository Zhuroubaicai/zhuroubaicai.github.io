---
layout: post
title: Python 显示进度条 progressbar2 
---
### 1. 安装

```bash
pip install progressbar2
```

### 2. 使用

```Python
from progressbar import ProgressBar

# 1000 steps
bar = ProgressBar(max_value=1000))

k = 0
for i in range(1000):
    # do something

    # update bar
    k += 1
    bar.update(k)

bar.finish()

```