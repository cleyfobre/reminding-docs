#garbage #gc #garbagecollector #garbagecollect #memory #java 

Yes, you’re absolutely correct! Addressing high heap memory usage and frequent major GCs involves a combination of tuning GC settings and identifying any inefficient memory usage patterns in your code. Here’s a breakdown of each approach:

> [!note] 치트키
> `jstat -gc <pid> 1000` 로 1초마다 heap메모리 상태를 확인할 수 있다.
> pid는 `ps aux --sort=-%mem | head` 로 메모리 많이 차지하는 순으로 확인하자.

#### Heap 메모리 확인할 때 컬럼 보는 법

| Column | Full Name                       | Description                                                          |
| ------ | ------------------------------- | -------------------------------------------------------------------- |
| S0C    | Survivor Space 0 Capacity       | Total capacity of the first survivor space in the young generation.  |
| S1C    | Survivor Space 1 Capacity       | Total capacity of the second survivor space in the young generation. |
| S0U    | Survivor Space 0 Used           | Currently used space in the first survivor space.                    |
| S1U    | Survivor Space 1 Used           | Currently used space in the second survivor space.                   |
| EC     | Eden Capacity                   | Total capacity of the Eden space in the young generation.            |
| EU     | Eden Used                       | Currently used space in the Eden space.                              |
| OC     | Old Generation Capacity         | Total capacity of the old generation.                                |
| OU     | Old Generation Used             | Currently used space in the old generation.                          |
| MC     | Metaspace Capacity              | Total capacity of the Metaspace (non-heap area).                     |
| MU     | Metaspace Used                  | Currently used space in the Metaspace.                               |
| CCSC   | Compressed Class Space Capacity | Total capacity of the Compressed Class Space (part of Metaspace).    |
| CCSU   | Compressed Class Space Used     | Currently used space in the Compressed Class Space.                  |
| YGC    | Young GC Count                  | Number of young (minor) garbage collections that have occurred.      |
| YGCT   | Young GC Time                   | Total time spent in young (minor) garbage collections.               |
| FGC    | Full GC Count                   | Number of full garbage collections that have occurred.               |
| CGC    | Concurrent GC Count             | Number of concurrent GC cycles                                       |
| CGCT   | Concurrent GC Time              | Total time spent in concurrent GC cycles.                            |
| FGCT   | Full GC Time                    | Total time spent in full garbage collections.                        |
| GCT    | Total GC Time                   | Total time spent in all garbage collections.                         |

```
# 11.1
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT    CGC    CGCT     GCT   
 0.0   3072.0  0.0   3072.0 389120.0 140288.0  230400.0   163566.9  133756.0 130435.2 16844.0 15677.4  65622  237.187   0      0.000  20      0.411  237.598
 0.0   3072.0  0.0   3072.0 389120.0 142336.0  230400.0   163566.9  133756.0 130435.2 16844.0 15677.4  65622  237.187   0      0.000  20      0.411  237.598

# 11.4
 0.0   1024.0  0.0   1024.0 89088.0  78848.0   176128.0   149727.0  134268.0 130914.8 16844.0 15680.9 100610  374.994   1      0.300  20      0.411  375.705
```

#### CGC 일어나더니 Eden용량, Old용량이 바뀜

```
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT    CGC    CGCT     GCT   
 0.0   1024.0  0.0   1024.0 36864.0  20480.0   679936.0   644055.1  134096.0 130738.9 16892.0 15678.7 113074  396.494   0      0.000  20      0.476  396.969
 0.0   1024.0  0.0   1024.0 36864.0   7168.0   679936.0   644347.9  134096.0 130738.9 16892.0 15678.7 113117  396.617   0      0.000  20      0.476  397.092
 0.0   2048.0  0.0   2048.0 449536.0 87040.0   266240.0   113310.8  134096.0 130713.1 16892.0 15672.7 113128  396.658   0      0.000  22      0.532  397.190
 0.0   2048.0  0.0   2048.0 449536.0 236544.0  266240.0   113414.7  134096.0 130713.1 16892.0 15672.7 113131  396.672   0      0.000  22      0.532  397.205
```



These full names should make it easier to understand each memory pool and its usage when interpreting `jstat` or GC log outputs.
### 1. **Configure GC Options to Run Major GC Before the Old Generation Gets Full**

   Tuning the GC options can prevent the old generation from filling up, which in turn helps avoid sudden spikes in memory usage and Full GC pauses. Here are a few ways to do this:

   - **Adjust Heap Occupancy Thresholds**:
     - For the **G1 GC**, set `-XX:InitiatingHeapOccupancyPercent=<threshold>` to a lower value to trigger major GC earlier, such as:
       ```bash
       java -XX:+UseG1GC -XX:InitiatingHeapOccupancyPercent=40 -jar your-app.jar
       ```
     - Lowering this threshold (e.g., from 45% to 40%) can trigger concurrent cycles sooner, reducing the chances of an old generation "fill-up."

   - **Consider a Different GC Algorithm**:
     - The **G1 GC** is designed to handle large heaps with predictable pause times, making it suitable for applications with varying workloads.
     - **CMS (Concurrent Mark-Sweep)** can also minimize Full GC by managing the old generation concurrently, but it’s more complex to configure.
     - **ZGC (Z Garbage Collector)** or **Shenandoah GC** are great for low-latency requirements and aim to avoid Full GC. These may be beneficial depending on your JVM version and application needs.

   - **Adjust Minor GC Settings**: 
     - Tuning the young generation (Eden and Survivor spaces) with `-Xmn` (setting the young generation size) or `-XX:NewRatio` can help control how much space is allocated to the young generation, which indirectly affects old generation usage.

### 2. **Identify and Fix Code That Causes Excessive Garbage**

   Finding and addressing memory leaks or inefficient code can significantly reduce the need for frequent GCs and reduce memory pressure on the old generation. Here’s how you can approach this:

   - **Profile Memory Usage**:
     - Use tools like **VisualVM** or **jmap** to take a heap dump and analyze which objects are using the most memory.
     - **Eclipse MAT (Memory Analyzer Tool)** can help you identify potential memory leaks by showing retained heap sizes and instances that prevent garbage collection.

   - **Look for Common Memory Issues**:
     - **Long-Lived Collections**: Collections that keep growing without being cleaned up (e.g., `List` or `Map` storing references indefinitely).
     - **Static References**: Static variables that hold references to large objects or collections.
     - **Unclosed Resources**: Resources like file streams, database connections, or network connections not being closed properly.
     - **Caching**: Excessive or unbounded caching can cause memory issues. Ensure caches have size limits and are periodically cleaned.

   - **Optimize Object Allocation Patterns**:
     - **Reuse Objects**: For frequently created objects, consider object pooling.
     - **Minimize Large Temporary Objects**: Try to avoid creating large temporary objects that are only used briefly, as they can quickly promote to the old generation if they survive a minor GC.

Combining GC tuning and memory usage optimization will yield the best results, as it not only helps manage memory more predictably but also makes the application more efficient overall.