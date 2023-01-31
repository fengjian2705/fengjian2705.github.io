---
title: BigDecimal舍入模式
tags:
  - BigDecimal舍入模式
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/proxy/proxy01.jpg
# excerpt: JDK 动态代理
categories:
- 后端
- java
---

## ROUND_UP：舍入远离 0

```shell
/**
 * Rounding mode to round away from zero. Always increments the digit prior to a nonzero discarded fraction.
 * Note that this rounding mode never decreases the magnitude of the calculated value.
 * 该模式，远离零，总是在非零丢弃分数之前增加数字，朝远离数轴的方向进位，正数+1，负数-1。（除保留位数后一位为0以外，其它不管正负号，直接进位）
 */
public final static int ROUND_UP =  0;
```

```javascript
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_UP));     // 6
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_UP));     // 2
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_UP));     // 1
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_UP));    // -1
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_UP));    // -2
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_UP));    // -6
```

## ROUND_DOWN：舍入靠近 0

```shell
/**
 * Rounding mode to round towards zero. Never increments the digit prior to a discarded fraction (i.e., truncates). 
 * Note that this rounding mode never increases the magnitude of the calculated value.
 * 该模式向零靠近。永远不要在一个被丢弃的分数前增加数字(即截断)。请注意，这种舍入模式不会增加计算值的大小。采用断言式，截断舍弃对应小数位后面的小数，不考虑任何进位。
 */
public final static int ROUND_DOWN = 1;
```

