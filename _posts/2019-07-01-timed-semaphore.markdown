---
layout: post
title:  "Implement custom timed semaphore"
date:   2019-07-01 10:03:16 +0200
categories:
---
 

{% highlight java %}
package com.company.component;
 
import java.time.LocalDateTime;
import java.time.temporal.ChronoUnit;
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;
 
import lombok.extern.slf4j.Slf4j;
 
@Slf4j
public class CustomTimedSemaphore extends Semaphore {
    private volatile int sleepPeriod;
    private volatile int maxRate;
    private volatile LocalDateTime startTime;
    private volatile int totalTime;
 
    public CustomTimedSemaphore(int permits, int period) {
        super(permits);
        maxRate = permits;
        sleepPeriod = period;
    }
 
    @Override
    public synchronized void acquire() {
        try {
            log.debug("number of available permits in semaphore: {}", this.availablePermits());
            checkTime();
            super.acquire();
            checkPermits();
        } catch (InterruptedException e) {
            this.release();
        }
    }
 
    private void checkTime() {
        if (this.availablePermits() == maxRate) {
            startTime = LocalDateTime.now();
            log.debug("init start time: {}", startTime);
        } else {
            totalTime = (int) ChronoUnit.SECONDS.between(startTime, LocalDateTime.now());
            if (totalTime > sleepPeriod) {
                log.debug("totalTime: {}", totalTime);
                startTime = LocalDateTime.now();
                this.release(maxRate - this.availablePermits());
            }
        }
    }
 
    private void checkPermits() throws InterruptedException {
        if (this.availablePermits() == 0) {
            log.debug("semaphore starts sleeping");
            TimeUnit.SECONDS.sleep(sleepPeriod - LocalDateTime.now().getSecond());
            log.debug("semaphore ends sleeping");
            this.release(maxRate);
        }
    }
 
    synchronized int getActualPermits() {
        checkTime();
        return availablePermits();
    }
}
{% endhighlight %}

Check out the [Postgres docs][postgres-docs] and [Spring docs][spring-docs] for more info.

[postgres-docs]: https://www.postgresql.org/docs/9.5/sql-insert.html
[spring-docs]:   https://docs.spring.io/spring/docs/3.0.0.M4/reference/html/ch12s04.html
