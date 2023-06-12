## ubuntu生成coredumo文件

### 首先用ulimit命令看下配置
```bash
ulimit -a
```

```
    -t: cpu time (seconds)              unlimited
    -f: file size (blocks)              unlimited
    -d: data seg size (kbytes)          unlimited
    -s: stack size (kbytes)             8192
    -c: core file size (blocks)         0
    -m: resident set size (kbytes)      unlimited
    -u: processes                       62761
    -n: file descriptors                1024
    -l: locked-in-memory size (kbytes)  65536
    -v: address space (kbytes)          unlimited
    -x: file locks                      unlimited
    -i: pending signals                 62761
    -q: bytes in POSIX msg queues       819200
    -e: max nice                        0
    -r: max rt priority                 0
    -N 15:                              unlimited
```
- 可以看到“-c: core file size (blocks) 0”, 当前限制coredump文件大小是0，不会生成coredump文件

### 配置coredump文件
```bash
ulimit -c unlimited
```
```bash
ulimit -a

-t: cpu time (seconds)              unlimited
-f: file size (blocks)              unlimited
-d: data seg size (kbytes)          unlimited
-s: stack size (kbytes)             8192
-c: core file size (blocks)         unlimited
-m: resident set size (kbytes)      unlimited
-u: processes                       62761
-n: file descriptors                1024
-l: locked-in-memory size (kbytes)  65536
-v: address space (kbytes)          unlimited
-x: file locks                      unlimited
-i: pending signals                 62761
-q: bytes in POSIX msg queues       819200
-e: max nice                        0
-r: max rt priority                 0
-N 15:                              unlimited
```

- coredump文件大小变为无限大(取决机器的配置)
- 虽然配置了coredump文件大小，但是很多时候会发现coredump文件没生成
- 生成coredump最简单的方法是使用kill命令给进程发送ABRT信号
```bash
kill -s ABRT 'process_id'
```

### 大部份时候会发现当前目录下没有生成coredump文件，此时可能是core_pattern配置的问题
```bash
cat /proc/sys/kernel/core_pattern

|/usr/share/apport/apport -p%p -s%s -c%c -d%d -P%P -u%u -g%g -- %E
```
- 默认配置有点难理解
- 重新配置下core_pattern
```bash
// 获取root权限
sudo -s
// 配置core_pattern
echo core > /proc/sys/kernel/core_pattern
```

### 再次kill进程就会生成coredumo文件
```bash
kill -s ABRT 'process_id'
ls
core
```
- core_pattern支持一些特殊配置，可以参考 [https://man7.org/linux/man-pages/man5/core.5.html]

