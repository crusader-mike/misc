# Let's Make Exceptions Great Again (M.E.G.A.)
## Introduction
## What is wrong with C++ exceptions?
1. exceptions are expensive
  - unknown price (space/performance) and behaviour
    - heap allocated
  - ironically, stack unwinding in C++ (a compiled language) is executed by [dwarf interpreter](https://youtu.be/_Ivd3qzgT7U?t=1242)
  - also, stack unwinding [can allocate memory](https://youtu.be/_Ivd3qzgT7U?t=2480)
2. exception costs are unpredictable
3. exceptions prevent compiler from [using certain optimizations](https://stackoverflow.com/questions/26079903/noexcept-stack-unwinding-and-performance)
4. unreliable
5. proposals to change exceptions (Herb's "Deterministic exceptions", Outcome pattern)
