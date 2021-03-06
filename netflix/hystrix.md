##### 设计原则

* 阻止任何单一的第三方依赖使用掉整个容器的全部用户线程
* 快速失败而不是在队列中积压请求
* 提供fallback错误回调机制
* 任何第三方依赖之间相互隔离
* 通过近实时的监控和报警及时发现系统中的问题

##### 实现原理

* 隔离(线程池隔离/信号量隔离)
* 优雅的降级机制(超时/资源不足)
* 融断，当失败率达到阀值自动触发，快速失败
* 缓存
* 支持实时监控 

##### 批量执行

* HystrixCollapser 

##### CircuitBreaker

* boolean attemptExecution() 

  > forceOpen 强制开启，所有请求都执行降级逻辑 
  > forceClose 强制关闭，所有请求都执行正常逻辑
  > circuitOpened 熔断开关，默认为-1，请求执行正常逻辑，如果发生熔断，该值会成为0，请求执行阶级逻辑
  > HALF_OPEN 熔断半开，即熔断后，会每隔一段时间进行试探 

##### 插件 

* HystrixConcurrencyStrategy
* HystrixEventNotifier
* HystrixMetricsPublisher
* HystrixPropetiesStrategy
* HystrixCommandExecutionHook
* HystrixDynamicProperties

##### HystrixCircuitBreaker

* HystrixCommandProperties.circuitBreakerRequestVolumeThreshold QPS达到一定阈值
* HystrixCommandProperties.circuitBreakerErrorThresholdPercentage 错误率达到一定阈值
* 熔断器将从『闭路』转换成『开路』
* 若此时是『开路』状态熔断器将短路后续所有经过该熔断器的请求,这些请求直接走『失败回退逻辑』
* 经过一定时间（即『休眠窗口』通过 `HystrixCommandProperties.circuitBreakerSleepWindowInMilliseconds()` 配置）后续第一个请求将会被允许通过熔断器（此时熔断器处于『半开』状态）, 若该请求失败，熔断器将又进入『开路』状态, 且在休眠窗口内保持此状态; 若该请求成功, 熔断器将进入『闭路』状态

##### 常规属性

* hystrix.command.default.execution.isolation.strategy  隔离策略,默认是Thread,可选Thread｜Semaphore

* hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds 命令执行超时时间,默认1000ms

* hystrix.command.default.execution.timeout.enabled 执行是否启用超时,默认启用true

* hystrix.command.default.execution.isolation.thread.interruptOnTimeout 发生超时是是否中断,默认true

* hystrix.command.default.execution.isolation.semaphore.maxConcurrentRequests 最大并发请求数

  > 默认10，该参数当使用ExecutionIsolationStrategy.SEMAPHORE策略时才有效。如果达到最大并发请求数，请求会被拒绝。理论上选择semaphore size的原则和选择thread size一致，但选用semaphore时每次执行的单元要比较小且执行速度快（ms级别），否则的话应该用thread。

* hystrix.command.default.fallback.isolation.semaphore.maxConcurrentRequests 如果并发数达到该设置值，请求会被拒绝和抛出异常并且fallback不会被调用。默认10

* hystrix.command.default.fallback.enabled 当执行失败或者请求被拒绝，是否会尝试调用hystrixCommand.getFallback() 。默认true

##### Circuit Breaker 相关的属性

* hystrix.command.default.circuitBreaker.enabled 用来跟踪circuit的健康性,如果未达标则让request短路。默认true

* hystrix.command.default.circuitBreaker.requestVolumeThreshold 

  > 一个rolling window内最小的请求数。如果设为20，那么当一个rolling window的时间内（比如说1个rolling window是10秒）收到19个请求，即使19个请求都失败，也不会触发circuit break。默认20

* hystrix.command.default.circuitBreaker.sleepWindowInMilliseconds 触发短路的时间值

  > 当该值设为5000时，则当触发circuit break后的5000毫秒内都会拒绝request，也就是5000毫秒后才会关闭circuit。默认5000

* hystrix.command.default.circuitBreaker.errorThresholdPercentage错误比率阀值，如果错误率>=该值，circuit会被打开，并短路所有请求触发fallback。默认50

* hystrix.command.default.circuitBreaker.forceOpen 强制打开熔断器，如果打开这个开关，那么拒绝所有request，默认false

* hystrix.command.default.circuitBreaker.forceClosed 强制关闭熔断器 如果这个开关打开，circuit将一直关闭且忽略circuitBreaker.errorThresholdPercentage

``` java
public static void main(String[] args) {
  System.out.println(test("java"));
}
private static String test(String name) {
  HystrixUtil.HystrixReqConfig hc = HystrixUtil.HystrixReqConfig
    .withGroupKey("testgroup")
    .withTimeout(3)
    .withUnit(TimeUnit.SECONDS)
    .withPassNum(64);
  String result = HystrixUtil
    .getExcuteResult(new HystrixCallableService<String>(){
    	@Override
      public String execute() {
        return "success" + name;
      }
      @Override 
      public String fallback() {
        return "fallback" + name;
      }
  },hc);
  return result;
}
```

