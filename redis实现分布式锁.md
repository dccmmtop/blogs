---
tags: [redis]
date: 2019-09-15 22:47
---

```ruby
require "securerandom"
module RedisLock
  # 带有超时限制特性的锁
  # params:
  # conn: redis 实例
  # lock_name: 锁名称
  # acquire_timeout: 最长获取锁的时间
  # lock_timeout: 存活时间,防止程序崩溃没有释放锁,而一直占有锁资源
  def acquire_lock_with_timeout(conn,lock_name, acquire_timeout = 10, lock_timeout = 60)
    # 64 位随机标识符
    identifier =SecureRandom.hex(64)
    lock_name = 'lock:' + lock_name
    lock_timeout = Integer(lock_timeout)
    end_time = Time.now + acquire_timeout

    #i = 0
    while Time.now < end_time
      if conn.setnx(lock_name, identifier)
        conn.expire(lock_name, lock_timeout)
        puts "已经获取到锁 #{identifier}"
        return identifier
        # 如果检测到该锁没有过期时间,给其设置过期时间, 确保所有的锁都会过期
      elsif conn.ttl(lock_name) < 0
        conn.expire(lock_name, lock_timeout)
      end
      #      puts "第#{i = i+1}次尝试获得锁"
      sleep(0.001)
    end
    return false
  end

  # 释放锁
  def release_lock(conn, lock_name, identifier)
    lock_name = "lock:" + lock_name
    begin
      conn.watch(lock_name)
      if conn.get(lock_name) == identifier
        conn.multi do
          conn.del(lock_name)
        end
      end
    rescue => e
      return false
    end
    #    puts "释放锁"
    return true
  end
end
```
