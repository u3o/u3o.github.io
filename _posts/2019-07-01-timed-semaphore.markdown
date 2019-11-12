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

{% highlight java %}
package com.company.component;

import java.util.concurrent.Callable;
import java.util.concurrent.Future;
import java.util.concurrent.LinkedBlockingDeque;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class RateLimitExecutor extends ThreadPoolExecutor {
    private final CustomTimedSemaphore semaphore;

    public RateLimitExecutor(int poolSize, int maxRate, int period) {
        super(poolSize, poolSize, 0, TimeUnit.SECONDS, new LinkedBlockingDeque<>());
        this.semaphore = new CustomTimedSemaphore(maxRate, period);
    }

    @Override
    public <T> Future<T> submit(Callable<T> task) {
        semaphore.acquire();
        return super.submit(task);
    }

    public int getPermits() {
        return semaphore.availablePermits();
    }
}

{% endhighlight %}
