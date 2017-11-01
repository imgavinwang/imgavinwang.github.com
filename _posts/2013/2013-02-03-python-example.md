---
layout: post
title: python example
date: 2013-02-13 18:28:58
categories:
- python
tags:
---

module example:

vtmain.py
```python
from engine import Engine

def main():
	mainEngine = Engine()

if __name__ == '__main__':
    main()
```

engine.py
```python
class Engine(object):
	def __init__(self):
		print "init Engine!"
```

