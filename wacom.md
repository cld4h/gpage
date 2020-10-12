---
title: Wacom
date: 2020-10-12
tags:
---

# Wacom 手写板配置

## 安装驱动

```
pacman -S xf86-input-wacom
```

## 配置

### 列出设备

```
xsetwacom list
```

会产生如下输出


```
Wacom One by Wacom S Pen stylus 	id: 13	type: STYLUS    
Wacom One by Wacom S Pen eraser 	id: 16	type: ERASER    
```

### 旋转设备

```
xsetwacom set "Wacom One by Wacom S Pen stylus" Rotate half
```

### 按钮映射

设置靠近笔尖的button为pan

```
xsetwacom set "Wacom One by Wacom S Pen stylus" Button 2 pan
```

改变pan的阈值

```
xsetwacom set "Wacom One by Wacom S Pen stylus" "PanScrollThreshold" 200
```
