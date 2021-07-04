# Let's Make Exceptions Great Again (M.E.G.A.)
## Introduction
## What is wrong with C++ exceptions?
1. exceptions are expensive and (costs) are unpredictable
  - unknown price (space/performance) and behaviour
    - heap allocated
  - stack unwinding is implemented using [dwarf interpreter](https://youtu.be/_Ivd3qzgT7U?t=1242)
  - stack unwinding [can allocate memory](https://youtu.be/_Ivd3qzgT7U?t=2480)
3. exceptions can prevent compiler from [using certain optimizations](https://stackoverflow.com/questions/26079903/noexcept-stack-unwinding-and-performance)
4. unreliable
5. proposals to change exceptions (Herb's "Deterministic exceptions", Outcome pattern)
