
# MapReduce Multi-threaded Framework

This repository contains a full implementation of a **multi-threaded MapReduce framework in C++**, built using `pthreads`, `mutexes`, `semaphores`, and `atomics`, as part of the HUJI Operating Systems course (67808).

The framework enables parallel data processing using the classic **Map → Sort → Shuffle → Reduce** model and is optimized for **performance, thread safety, and reusability**.

---

## Project Objectives

- Build a complete MapReduce **framework** (not just a client)
- Enable **multi-threaded processing** of large datasets
- Track **job state and progress** in real-time using atomic variables
- Maintain full **thread safety** and avoid race conditions
- Handle complex synchronization without using C++11 threads

---

## Technical Highlights

### Core Features
- Configurable multithreading (`multiThreadLevel`)
- Full lifecycle management: start, monitor, wait, and cleanup
- Support for any client-defined `map()` and `reduce()` logic
- Barrier synchronization between phases
- Shuffle logic implemented in a single thread for correctness
- Thread-local intermediate storage to eliminate output collisions

### Thread Safety and Performance Design

- **Thread Coordination**: All synchronization done using low-level POSIX primitives (`pthread_mutex`, `sem_t`)
- **Atomic State Tracking**: 64-bit atomic variable stores both current phase and percentage completion
- **Output Safety**: `emit3()` writes to `outputVec` under mutex protection
- **Concurrency**: Multiple jobs can run concurrently in separate threads
- **Shuffle Optimization**: Sorted vectors allow efficient max-key-based merging during shuffle

---

## Architecture Overview

The framework is divided into:
- **Client logic**: Provides `map()` and `reduce()` functions via `MapReduceClient`
- **Framework logic**: Handles threads, job state, and task coordination

### Execution Flow:

1. **Map Phase**
   - Threads pull input elements using atomic index
   - Calls `map()` → emits `(k2, v2)` via `emit2()`

2. **Sort Phase**
   - Each thread locally sorts its intermediate vector

3. **Barrier Synchronization**
   - All threads wait before proceeding

4. **Shuffle Phase** (Thread 0 only)
   - Collects sorted `(k2, v2)` across threads and groups them by key

5. **Reduce Phase**
   - All threads pull key-groups and call `reduce()`, which emits `(k3, v3)` via `emit3()`

---

## File Structure

```
MapReduceFramework.cpp         # Your full framework implementation
MapReduceFramework.h           # Provided interface (do not modify)
MapReduceClient.h              # Provided interface (do not modify)
Makefile                       # Builds the framework as a static library
README.md                      # You're reading it
```

---

## How to Use the Framework

1. Implement a client class with `map()` and `reduce()` (see `MapReduceClient.h`)
2. Prepare an input vector of type `InputVec = std::vector<std::pair<K1*, V1*>>`
3. Call the framework with:
   ```cpp
   JobHandle job = startMapReduceJob(client, inputVec, outputVec, numThreads);
   ```
4. Monitor job progress with:
   ```cpp
   JobState state;
   getJobState(job, &state);
   ```
5. Wait for job completion:
   ```cpp
   waitForJob(job);
   ```
6. Release resources:
   ```cpp
   closeJobHandle(job);
   ```

---

## Testing & Verification

Framework was tested using:
- Provided TA6 client (character frequency count)
- Custom clients with:
  - Large datasets
  - Duplicate keys
  - High thread count
  - Edge cases (empty input, early thread exits)

### Key goals verified:
- No memory leaks or crashes
- Correct sorting/shuffling of keys
- Fully deterministic output regardless of thread count
- Race condition-free output handling
- Reduced runtime with increasing threads (scaling verified)

---

## Notes & Constraints

- Uses **only** `pthread_`, `sem_`, and `std::atomic`
- **C++11+ threading is forbidden** (`std::thread`, `std::mutex`, etc.)
- `startMapReduceJob()` is **thread-safe**
- `emit2()` and `emit3()` are **called from within client logic** but affect shared framework data
- **No output is printed** and no `main()` function exists in the framework
- All memory and synchronization objects are safely released using `closeJobHandle()`

---

## Build Instructions

Compile using:
```bash
make
```

This produces:
```
libMapReduceFramework.a
```

Ensure your submitted version contains:
- `MapReduceFramework.cpp`
- `Makefile`
- `README.md`

You **do not** need to submit the `.h` files provided with the exercise.

---

## Contact

For any questions or feedback:

- Email: yairthfc@gmail.com  
- LinkedIn: [linkedin.com/in/yairmahfud](https://www.linkedin.com/in/yairmahfud)

---

## License

This project was created as part of the 2025 Operating Systems course at the Hebrew University of Jerusalem.  
Use is permitted for **educational and non-commercial** purposes only.


