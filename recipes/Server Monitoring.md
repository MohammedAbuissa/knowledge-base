Lately, I've been monitoring a backend using htop so I came across helpful aid tools and learning using htop better.

## htop Color coding
htop color codes the status bars for processor and memory, what do they mean?
![[Pasted image 20231031194218.png]]

Here's the color meaning according to a [StackOverFlow answer](https://serverfault.com/a/180724/328649)
![[Pasted image 20231031194820.png]]

## Linux Cache

I thought I might benefit from checking what the OS caches so I can get better grasp on the memory usage

### BingAI answer to how to detect cache usage & utilization

-> "I want to list the process or the information that the linux os caches in memory becausee htop shows big yellow(cache) portion in mem status bar , how can I achieve that?"

You can use the `linux-ftools` suite of tools to analyze the buffers and cache. It includes the following tools: `fincore`, `fadvise`, and `fallocate`. [The tool that will show you the list of files in the buffers & cache is `fincore`](https://stackoverflow.com/questions/210809/how-can-you-tell-whether-a-file-is-being-cached-in-memory-in-linux)[1](https://stackoverflow.com/questions/210809/how-can-you-tell-whether-a-file-is-being-cached-in-memory-in-linux). Here is an example of how to use it:

```bash
$ fincore --pages=false --summarize --only-cached *
```

[This command will print stats for files that are actually in cache](https://stackoverflow.com/questions/210809/how-can-you-tell-whether-a-file-is-being-cached-in-memory-in-linux)[1](https://stackoverflow.com/questions/210809/how-can-you-tell-whether-a-file-is-being-cached-in-memory-in-linux).

Another tool you can use is `slabtop`. [This command will sort the output and show you the cache size](https://askubuntu.com/questions/920609/determining-the-source-of-memory-cache-usage)[2](https://askubuntu.com/questions/920609/determining-the-source-of-memory-cache-usage):

```bash
$ sudo slabtop -s c
```

[You can also check which processes are using swap with this command](https://askubuntu.com/questions/920609/determining-the-source-of-memory-cache-usage)[2](https://askubuntu.com/questions/920609/determining-the-source-of-memory-cache-usage):

```bash
$ cd /proc
$ find -maxdepth 2 -iname status -exec grep -i -e name -e swap {} \\; -exec echo \"---\" \\;
```

[This command will give you a list of all process cache sizes](https://askubuntu.com/questions/920609/determining-the-source-of-memory-cache-usage)[2](https://askubuntu.com/questions/920609/determining-the-source-of-memory-cache-usage).

[Please note that these tools may not be available in most Linux distributions by default](https://stackoverflow.com/questions/210809/how-can-you-tell-whether-a-file-is-being-cached-in-memory-in-linux)[1](https://stackoverflow.com/questions/210809/how-can-you-tell-whether-a-file-is-being-cached-in-memory-in-linux)[2](https://askubuntu.com/questions/920609/determining-the-source-of-memory-cache-usage).


-> "What is a cache slab?"

In Linux, a slab is a cache of objects of the same type. [It’s a primary unit in cache, which is acquired or relinquished at once](https://stackoverflow.com/questions/14450133/slab-memory-management)[1](https://stackoverflow.com/questions/14450133/slab-memory-management). [The slab allocation algorithm uses caches to store kernel objects](https://www.geeksforgeeks.org/operating-system-allocating-kernel-memory-buddy-system-slab-system/)[2](https://www.geeksforgeeks.org/operating-system-allocating-kernel-memory-buddy-system-slab-system/).

[The slab allocator is an abstraction layer to make the allocation of numerous objects of the same type easier](https://stackoverflow.com/questions/14450133/slab-memory-management)[1](https://stackoverflow.com/questions/14450133/slab-memory-management). [It introduces the notion of slab through a data structure to prevent memory fragmentation issues that could happen if the memory allocation was made in a simple and intuitive manner](https://stackoverflow.com/questions/14450133/slab-memory-management)[1](https://stackoverflow.com/questions/14450133/slab-memory-management).

[The `kmem_cache` object holds 3 lists of its slabs, gathered in 3 flavors](https://stackoverflow.com/questions/14450133/slab-memory-management)[1](https://stackoverflow.com/questions/14450133/slab-memory-management):

- Empty slabs: these slabs do not contain an in-use object.
- Partial slabs: these slabs contain objects currently used but there is still memory area that can hold new objects.
- Full slabs: these slabs contain objects being used and cannot host new objects.

When requesting an object through the slab allocator, it will try to get the required memory area within a partial slab. [If it cannot, it will get it from an empty slab](https://stackoverflow.com/questions/14450133/slab-memory-management)[1](https://stackoverflow.com/questions/14450133/slab-memory-management).

[The advantages of using slabs include reducing internal fragmentation caused by the buddy system, better hardware cache usage by aligning objects to start at different offsets in different slabs so that interference between cache lines can be reduced](https://stackoverflow.com/questions/14450133/slab-memory-management)[1](https://stackoverflow.com/questions/14450133/slab-memory-management). [This also causes a reduction in external fragmentation](https://stackoverflow.com/questions/14450133/slab-memory-management)[1](https://stackoverflow.com/questions/14450133/slab-memory-management).
