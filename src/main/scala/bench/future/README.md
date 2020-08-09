## Simple Future vs Fiber performance test

In this test we compare a `CPU-bound` factorial calculation task for sequential and parallel mode on several thread pools. We calculate factorials for each number in range `[0..10]` or `[0..20]` and add results to get the final sum.

As a baseline, we use Scala `Future` to calculate factorials for each element. 
For `ZIO`, we use two modes:
- sequential
- parallel

In sequential mode, all computation is done sequentially on the default thread pool.
In parallel mode, computation is split across `16 fibers` all run on the same thread pool.

### Setup

* Hardware
- Intel(R) Core(TM) i5-2410M CPU @ 2.30GHz
- DDR3 1333MHz
- SSD

* Software
- Linux lap 5.4.0-42-generic #46~18.04.1-Ubuntu 
- OpenJDK build 11.0.9-testing+0-builds.shipilev.net-openjdk-shenandoah-jdk11-b289-20200806
- Scala 2.13.3
- ZIO 1.0.0

### Results

#### Scala Future (baseline)

```bash
[info] Benchmark                                             (num)   Mode  Cnt       Score   Error  Units

// Work Stealing Thread Pool

[info] FutureFiberSimpleBenchmark.futureBench                   10  thrpt       146486.706          ops/s
[info] FutureFiberSimpleBenchmark.futureBench                   20  thrpt       118264.307          ops/s
```

#### ZIO Sequential (1 fiber)

```bash

// Default Thread Pool

[info] FutureFiberSimpleBenchmark.fiberCalcSeqBench             10  thrpt       445424.724          ops/s
[info] FutureFiberSimpleBenchmark.fiberCalcSeqBench             20  thrpt       283268.525          ops/s

// Blocking Thread Pool

[info] FutureFiberSimpleBenchmark.fiberCalcSeqBlockBench        10  thrpt        55316.678          ops/s
[info] FutureFiberSimpleBenchmark.fiberCalcSeqBlockBench        20  thrpt        49578.342          ops/s

// Cached Thread Pool

[info] FutureFiberSimpleBenchmark.fiberCalcSeqCachedTPBench     10  thrpt        56269.522          ops/s
[info] FutureFiberSimpleBenchmark.fiberCalcSeqCachedTPBench     20  thrpt        51140.325          ops/s

// Work Stealing Thread Pool

[info] FutureFiberSimpleBenchmark.fiberCalcSeqWSTPBench         10  thrpt        60301.492          ops/s
[info] FutureFiberSimpleBenchmark.fiberCalcSeqWSTPBench         20  thrpt        55901.526          ops/s
```

#### ZIO Parallel (16 fibers)

```bash

// Default Thread Pool

[info] FutureFiberSimpleBenchmark.fiberCalcParBench             10  thrpt         2914.234          ops/s
[info] FutureFiberSimpleBenchmark.fiberCalcParBench             20  thrpt         2561.360          ops/s

// Blocking Thread Pool

[info] FutureFiberSimpleBenchmark.fiberCalcParBlockBench        10  thrpt         1261.759          ops/s
[info] FutureFiberSimpleBenchmark.fiberCalcParBlockBench        20  thrpt         1101.768          ops/s

// Cached Thread Pool

[info] FutureFiberSimpleBenchmark.fiberCalcParCachedTPBench     10  thrpt         1364.834          ops/s
[info] FutureFiberSimpleBenchmark.fiberCalcParCachedTPBench     20  thrpt          954.166          ops/s

// Work Stealing Thread Pool

[info] FutureFiberSimpleBenchmark.fiberCalcParWSPPBench         10  thrpt         4060.323          ops/s
[info] FutureFiberSimpleBenchmark.fiberCalcParWSPPBench         20  thrpt         3324.929          ops/s
```

### Results

The best performance for this simple `CPU-bound` task is obtained for `ZIO` **sequential computation** in one fiber on th default thread pool.

Using blocking thread pool cuts performance `5x...8x` times.

Using other thread pools (`Work Stealing` and `Caching`) won't improve performance much for this test


### Timestamp: Aug 10 2020