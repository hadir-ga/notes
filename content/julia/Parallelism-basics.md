---
title: "Parallelism-basics"
author: "Hadir Garcia-Castro"
date: 22018-11-12T16:24:01.176
type: technical-note
draft: false
---
# Basic concepts in parallelism with Julia

## Julia coroutines
- Commonly known as symetric coroutines, lightweight threads, cooperative multitasking, or one-shot continuations.

- Julia executes scheduled tasks, can stop a task, executes a new one and then, after finish it, resumes the former one.

- First-in first-out queue concept.

### Chanels


```julia
const jobs = Channel{Int}(32);
jobs
```


```julia
const results = Channel{Tuple}(32);

function do_work()
   for job_id in jobs
       exec_time = rand()
       sleep(exec_time)                # simulates elapsed time doing actual work
                                       # typically performed externally.
       put!(results, (job_id, exec_time))
   end
end;

function make_jobs(n)
   for i in 1:n
       put!(jobs, i)
   end
end;

n = 12;

@async make_jobs(n); # feed the jobs channel with "n" jobs

for i in 1:4 # start 4 tasks to process requests in parallel
   @async do_work()
end

@elapsed while n > 0 # print out results
   job_id, exec_time = take!(results)
   println("$job_id finished in $(round(exec_time; digits=2)) seconds")
   global n = n - 1
end
```

## Multi-threading

## Multi-core or distributed processing

# References
- [Julia Documentation](https://docs.julialang.org/en/v1/manual/parallel-computing/index.html). Accesed on Nov 12th, 2018