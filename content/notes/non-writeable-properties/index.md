---
title: Non-writeable properties
date: "2022-02-15"
description: "Non writeable properties"
tags: ["javascript"]
---
* A non-writable property cannot be changed with an assignment operator (`=`, `*=`, `**=`, `/=`, `+=`, `-=`)
* To define a non-writeable property on an object use `Object.defineProperty()` and set `writable` to false:
  ```
  var obj = {}
  // writable is also false by default
  Object.defineProperty(obj, 'a', { value: 2, writable: false });
  ```