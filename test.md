Document number: TBD (Dnnnn=yy-nnnn)

Date: 2017-08-21

Reply-to: Michael Kilburn <crusader.mike at gmail dot com>


# 1. Introduction
Memory for exception object is allocated in an 'unspecified way' (18.1/4). This makes it impossible for application to handle such allocation failures. If implementation uses dynamic storage for exception this means impossibility to write code that reliably handles *out-of-memory* condition.

This proposal aims to fix it by specifying behavior in such case.

# 2. Motivation
Code that uses C-style error handling can be written to handle out-of-memory (OOM) reliably. It can't be done if code uses exceptions. This is a problem if you are writing a program designed to survive OOMs.

# 3. Impact On the Standard
Proposal introduces a change to exception handling mechanism by specifying behavior under no-memory condition.

# 4. Problem
Consider this snippet in context of typical stack-based implementation:
```c
int foo() { return -1; }
...
int res = foo();
if (res)
    return res;
```

and this one:

```c++
void foo() { throw -1; }
...
foo();
```
First snippet can have only two outcomes:
- stack overflow if there is no space on stack for another frame
- error object (-1) is successfully returned to caller

Second snippet can have three outcomes:
- stack overflow
- exception object (-1) is successfully constructed and thrown
- **unspecified** if memory allocation for exception object fails, no way to handle something unspecified in the code

With GCC (which allocates exceptions in heap and calls *std::terminate()* on failure) this means you can't write code that reliably survives OOM. It is understandable why GCC chose *std::terminate()* (over throwing *std::bad_alloc*, for example) -- no one expects `throw -1` to throw something else instead.

Note that fundamentally these two snippets do the same thing -- construct error object and pass it to caller. The important difference that in first one object memory is already preallocated by caller and in second one -- it gets allocated by callee when it is time to throw.

# 5. Solution
In short:
* an exception *XX* (eXceptional eXception) that is guaranteed to be thrown without fail
* change 18.1/4 to mandate that `throw ABC` will throw *XX* if runtime can't allocate memory for *ABC*
* (optional) clarify 21.8.6/8 -- mention that *XX* can be thrown if memory for *std::bad_exception* can't be allocated

## 5.1 New exception
*std::bad_alloc* can be reused for *XX*, but it may be useful to distinguish between running out of dynamic storage and running out of exception storage. To facilitate that *std::bad_alloc* can be extended with additional member variable that specifies storage we ran out of.

(Regardless of choice made wrt std::bad_alloc) *XX* support should be built in such way that throwing it does not consume storage used for normal exception mechanisms. Effectively we need to pass a simple 'no space in exception storage' flag to exception handling mechanism. For example it could be done by passing NULL instead of exception object pointer (under the hood).

Implementation of *std::current_exception()* might be tricky as NULL value is already used in 'no exception being handled' case.

## 5.2 Thoughts on implementations
Implementations using heap for exceptions (like GCC) should not have any problems.

Implementations that use call stack as exception storage (like MSVC) could be considered problematic since C++ traditionally doesn't provide means of handling out-of-stack condition (probably because this will make impossible to create *no-fail* functions). My belief that it shouldn't be a game changer since at time of allocation implementation should know how much stack has been left and should be able to check it.

Implementation can also use a separate stack for exceptions -- to be created on first throw (in current thread) with every allocation checked against remaining free space.

# 6. Proposed Text
TBD

