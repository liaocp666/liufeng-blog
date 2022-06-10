---
title: 使用 Lambda 对 Map 进行排序
categories: 代码笔记
tags: [Lambda]
date: 2021-03-28 13:42:08
url: map-sort-by-value
---

这真的就是一个代码笔记，使用 Lambda 对 Map 排序

<!--more-->

```java
public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("第一", 1);
        map.put("第三", 3);
        map.put("第二", 2);
        System.out.println(map);
        Map<String, Integer> linkedHashMap = map.entrySet().stream()
            	// 对 value 从小到大排序
                .sorted(Comparator.comparing(o -> o.getValue()))
            	// 收集为 LinkedHasMap
                .collect(Collectors.toMap(
                        Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e1, LinkedHashMap::new)
                );
        System.out.println(linkedHashMap);
}
```

**结果**

```Java
{第三=3, 第一=1, 第二=2}
{第一=1, 第二=2, 第三=3}
```

