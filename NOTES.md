# Notes

## To resolve
- Can't attach to Reaper in CLion

## Audio deadline details
'Fast' in audio programming
waiting for worker threads has a non-deterministic execution time
we are interested in the worst-case scenario
we must miss NO audio deadlines
meet the deadline on every call to `processBlock()`

RULES
1. We cannot do anything that, in the worst case, takes longer to complete than
the buffer interval.
2. We cannot perform any operation that has an unbounded execution time.
3. We cannot perform any operation with an unknown execution time, as it may
fall into one of the above categories.

Don't do this on the audio thread:
- allocate or deallocate memory
    - new, delete, malloc(), free(), make_unique<>(), make_shared<>() etc.
    - push_back()
    - copy assignment
    - (de)allocations are system calls (= unbounded execution time)
- locking or unlocking
    - (some rare exceptions)
    - use std::atomic for float etc. but atomic may use a lock internally for larger data types
    - use static_assert(std::atomic<DataType>::is_always_lock_free) to prevent compilation
- start new threads or wait for threads
- i/o
- algorithm with unpredictable/poor worst-case execution time
- call OS functions or 3rd party library functions that are not real-time safe

##
std::view::iota()