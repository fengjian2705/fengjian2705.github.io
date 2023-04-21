---
title: BigDecimal舍入模式
tags:
  - BigDecimal舍入模式
index_img: https://s3.bmp.ovh/imgs/2023/02/02/a1786250a2cda9fa.png
# excerpt: JDK 动态代理
categories:
- 后端
- java
---

## ROUND_UP：舍入远离 0

```
/**
 * Rounding mode to round away from zero. Always increments the digit prior to a nonzero discarded fraction.
 * Note that this rounding mode never decreases the magnitude of the calculated value.
 * 该模式，远离零，总是在非零丢弃分数之前增加数字，朝远离数轴的方向进位，正数+1，负数-1。（除保留位数后一位为0以外，其它不管正负号，直接进位）
 */
public final static int ROUND_UP =  0;
```

```
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_UP));     // 6
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_UP));     // 2
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_UP));     // 1
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_UP));    // -1
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_UP));    // -2
log.info("BigDecimal.ROUND_UP={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_UP));    // -6
```

## ROUND_DOWN：舍入靠近 0

```
/**
 * Rounding mode to round towards zero. Never increments the digit prior to a discarded fraction (i.e., truncates). 
 * Note that this rounding mode never increases the magnitude of the calculated value.
 * 该模式向零靠近。永远不要在一个被丢弃的分数前增加数字(即截断)。请注意，这种舍入模式不会增加计算值的大小。采用断言式，截断舍弃对应小数位后面的小数，不考虑任何进位。
 */
public final static int ROUND_DOWN = 1;
```
```
log.info("BigDecimal.ROUND_DOWN={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_DOWN));     // 5
log.info("BigDecimal.ROUND_DOWN={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_DOWN));     // 1
log.info("BigDecimal.ROUND_DOWN={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_DOWN));     // 1
log.info("BigDecimal.ROUND_DOWN={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_DOWN));    // -1
log.info("BigDecimal.ROUND_DOWN={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_DOWN));    // -1
log.info("BigDecimal.ROUND_DOWN={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_DOWN));    // -5
```

## ROUND_CEILING：向上（正数方向）舍入

```
/**
 * Rounding mode to round towards positive infinity. If the BigDecimal is positive, behaves as for ROUND_UP; if negative, behaves as for ROUND_DOWN. 
 * Note that this rounding mode never decreases the calculated value.
 * 该模式向正无穷四舍五入，是 ROUND_UP 和ROUND_DOWN 的组合，如果 BigDecimal 为正数，则行为与 ROUND_UP 相同；如果 BigDecimal 为负数，则行为与 ROUND_DOWN 相同。
 */
public final static int ROUND_CEILING = 2;
```

```
log.info("BigDecimal.ROUND_CEILING={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_CEILING));     // 6
log.info("BigDecimal.ROUND_CEILING={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_CEILING));     // 2
log.info("BigDecimal.ROUND_CEILING={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_CEILING));     // 1
log.info("BigDecimal.ROUND_CEILING={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_CEILING));    // -1
log.info("BigDecimal.ROUND_CEILING={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_CEILING));    // -1
log.info("BigDecimal.ROUND_CEILING={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_CEILING));    // -5

```

## ROUND_FLOOR：向下（负数方向）舍入

```
/**
 * Rounding mode to round towards negative infinity. If the BigDecimal is positive, behave as for ROUND_DOWN; if negative, behave as for ROUND_UP. 
 * Note that this rounding mode never increases the calculated value.
 * 该模式向负无穷四舍五入，也是 ROUND_UP 和 ROUND_DOWN 的组合，但是和ROUND_CEILING 是相反的。如果 BigDecimal 为正数，则行为与 ROUND_DOWN 相同；如果为负数，则行为与 ROUND_UP 相同。
 */
public final static int ROUND_FLOOR = 3;

```
```
log.info("BigDecimal.ROUND_FLOOR={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_FLOOR));     // 5
log.info("BigDecimal.ROUND_FLOOR={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_FLOOR));     // 1
log.info("BigDecimal.ROUND_FLOOR={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_FLOOR));     // 1
log.info("BigDecimal.ROUND_FLOOR={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_FLOOR));    // -1
log.info("BigDecimal.ROUND_FLOOR={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_FLOOR));    // -2
log.info("BigDecimal.ROUND_FLOOR={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_FLOOR));    // -6
```

## ROUND_HALF_UP：四舍五入

```
/**
 * Rounding mode to round towards "nearest neighbor" unless both neighbors are equidistant, in which case round up. 
 * Behaves as for ROUND_UP if the discarded fraction is ≥ 0.5; otherwise, behaves as for ROUND_DOWN. 
 * Note that this is the rounding mode that most of us were taught in grade school.
 * 该模式向“最近的邻居”四舍五入。如果丢弃的分数是≥ 0.5，则行为与ROUND_UP相同;否则，行为与ROUND_DOWN相同。请注意，这是我们大多数人在小学时学过的舍入模式。
 */
public final static int ROUND_HALF_UP = 4;

```
```shell
log.info("BigDecimal.ROUND_HALF_UP={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_HALF_UP));     // 6
log.info("BigDecimal.ROUND_HALF_UP={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_HALF_UP));     // 2
log.info("BigDecimal.ROUND_HALF_UP={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_HALF_UP));     // 1
log.info("BigDecimal.ROUND_HALF_UP={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_HALF_UP));    // -1
log.info("BigDecimal.ROUND_HALF_UP={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_HALF_UP));    // -2
log.info("BigDecimal.ROUND_HALF_UP={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_HALF_UP));    // -6
```

## ROUND_HALF_DOWN：五舍六入

```shell
/**
 * Rounding mode to round towards "nearest neighbor" unless both neighbors are equidistant, in which case round down. 
 * Behaves as for ROUND_UP if the discarded fraction is > 0.5; otherwise, behaves as for ROUND_DOWN.
 * 该模式向“最近的邻居”四舍五入。如果丢弃的分数是> 0.5，则行为与ROUND_UP相同；否则，行为与ROUND_DOWN相同，也就是五舍六入
 */
public final static int ROUND_HALF_DOWN = 5;

```
```shell
log.info("BigDecimal.ROUND_HALF_DOWN={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_HALF_DOWN));     // 5
log.info("BigDecimal.ROUND_HALF_DOWN={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_HALF_DOWN));     // 2
log.info("BigDecimal.ROUND_HALF_DOWN={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_HALF_DOWN));     // 1
log.info("BigDecimal.ROUND_HALF_DOWN={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_HALF_DOWN));    // -1
log.info("BigDecimal.ROUND_HALF_DOWN={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_HALF_DOWN));    // -2
log.info("BigDecimal.ROUND_HALF_DOWN={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_HALF_DOWN));    // -5

```

## ROUND_HALF_EVEN：奇（四舍五入）偶（五舍六入）舍入

```shell
/**
 * Rounding mode to round towards the "nearest neighbor" unless both neighbors are equidistant, in which case, round towards the even neighbor. 
 * Behaves as for ROUND_HALF_UP if the digit to the left of the discarded fraction is odd; behaves as for ROUND_HALF_DOWN if it's even. 
 * Note that this is the rounding mode that minimizes cumulative error when applied repeatedly over a sequence of calculations.
 * 该模式是 ROUND_HALF_UP 和 ROUND_HALF_DOWN 的组合，但是比较特殊的是，如果被丢弃分数左边的数字是奇数，则行为与ROUND_HALF_UP（四舍五入）相同;如果是偶数，则表现为ROUND_HALF_DOWN（五舍六入）。请注意，在对一系列计算重复应用时，这种舍入模式可以最小化累积误差。
 */
public final static int ROUND_HALF_EVEN = 6;

```
```shell
log.info("BigDecimal.ROUND_HALF_EVEN={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_HALF_EVEN));     // 6
log.info("BigDecimal.ROUND_HALF_EVEN={}", new BigDecimal("6.5").setScale(0, BigDecimal.ROUND_HALF_EVEN));     // 5
log.info("BigDecimal.ROUND_HALF_EVEN={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_HALF_EVEN));     // 2
log.info("BigDecimal.ROUND_HALF_EVEN={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_HALF_EVEN));     // 1
log.info("BigDecimal.ROUND_HALF_EVEN={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_HALF_EVEN));    // -1
log.info("BigDecimal.ROUND_HALF_EVEN={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_HALF_EVEN));    // -2
log.info("BigDecimal.ROUND_HALF_EVEN={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_HALF_EVEN));    // -6
log.info("BigDecimal.ROUND_HALF_EVEN={}", new BigDecimal("-6.5").setScale(0, BigDecimal.ROUND_HALF_EVEN));    // -5

```

## ROUND_UNNECESSARY：非必要舍入（固定格式）

```shell
/**
 * Rounding mode to assert that the requested operation has an exact result, hence no rounding is necessary. 
 * If this rounding mode is specified on an operation that yields an inexact result, an ArithmeticException is thrown.
 * 该模式认为传入的数据一定满足设置的小数模式，因此不需要舍入。如果在产生不精确结果的操作上指定了此舍入模式，则会抛出ArithmeticException。
 */ 
public final static int ROUND_UNNECESSARY = 7;

```
```shell
log.info("BigDecimal.ROUND_UNNECESSARY={}", new BigDecimal("5.5").setScale(0, BigDecimal.ROUND_UNNECESSARY));     // throw ArithmeticException
log.info("BigDecimal.ROUND_UNNECESSARY={}", new BigDecimal("1.6").setScale(0, BigDecimal.ROUND_UNNECESSARY));     // throw ArithmeticException
log.info("BigDecimal.ROUND_UNNECESSARY={}", new BigDecimal("1.0").setScale(0, BigDecimal.ROUND_UNNECESSARY));     // 1
log.info("BigDecimal.ROUND_UNNECESSARY={}", new BigDecimal("-1.0").setScale(0, BigDecimal.ROUND_UNNECESSARY));    // -1
log.info("BigDecimal.ROUND_UNNECESSARY={}", new BigDecimal("-1.6").setScale(0, BigDecimal.ROUND_UNNECESSARY));    // throw ArithmeticException
log.info("BigDecimal.ROUND_UNNECESSARY={}", new BigDecimal("-5.5").setScale(0, BigDecimal.ROUND_UNNECESSARY));    // throw ArithmeticException

```
