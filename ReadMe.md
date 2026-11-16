# System-Level Computing Projects

Systems programming projects in C++ from the Operating Systems course at the Hebrew University of Jerusalem.

## Projects

- [User-Level Thread Management Library](./User-Level-Thread-Management-Library)
  A preemptive user-level threads library: round-robin scheduling driven by `setitimer` / `SIGVTALRM`, context switching, blocking, sleeping and termination.

- [Virtual Memory Management System](./Virtual-Memory-Management-System)
  Hierarchical page tables, address translation, page faults, swapping and eviction, implemented without any dynamic memory allocation.

- [Multi-threaded MapReduce Framework](./mapreduce-framework)
  A MapReduce framework built on `pthreads`, mutexes, semaphores and atomics, running Map, Sort, Shuffle and Reduce across configurable worker threads.

Each folder has its own README with build and usage instructions.
