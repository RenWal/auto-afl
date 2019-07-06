# auto-afl
Parallel fuzzing automation tool for AFL on Linux

## Introduction
This tool automates fuzzing with the [American Fuzzy Lop](http://lcamtuf.coredump.cx/afl/) fuzzer. If you want to set up a larger multi-threaded fuzzing session using AFL and run it with little to no supervision, then auto-afl is for you.

auto-afl was created for my seminar thesis at the [Chair of Systems Security @ RUB](https://www.syssec.ruhr-uni-bochum.de/).

## Requirements
auto-afl was built for AFL version 2.52b. You need the binaries `afl-fuzz`, `afl-cmin` and `afl-ptmin` on your $PATH, as well as `screen` and `tmux`.

Please also put the script from the submodule `afl-ptmin` on your $PATH.

## Quick Start
To use auto-afl:
  1. Create any directory
  2. Copy your instrumented binary here
  
     Use `afl-gcc` or `afl-clang` to compile with instrumentation. auto-afl does NOT do this for you!
  3. Create a subdirectory and put your test cases there
  4. Make sure you have enough memory to mount a `tmpfs`. If you are low on memory, consider changing the default of 4GB file system size in `auto-afl.sh`.
  5. Run auto-afl

auto-afl will spawn a tmux session for you or use an existing one. You will then be presented with a configuration screen. Enter:
  1. A title for your session
  2. The amount of cores to use (auto-afl will never use more than one less core than in your system to keep things running smoothly)
  3. The amount of fuzzing cycles to perform (the queue will be deduplicated and minimized after each cycle)
  4. The name of the directory with your test cases
  5. The name of the target
  6. Any parameters to invoke your target with (Use `@@` as a placeholder for the input file name. If you do not, then the file content will be sent to the target's standard input)
  7. Any parameters for AFL (memory limits, etc.)

Hit enter one more time, then auto-afl will start the fuzzing run.

## Fuzzing run
auto-afl performs the follwing actions:
  1. Create a `tmpfs` ramdisk to protect your HDD/SSD from excessive writes and speed up the fuzzers
  2. Run the fuzzers for one cycle (you will see some stats for each fuzzer)
  3. Collect all queues and create a backup
  3. Minimize each test case in the queues
  4. De-duplicate the queues
  5. Minimize again
  6. Go back to step 2 as many times as you specified
  7. Collect all crashes and save them outside the ramdisk
  8. Destroy the ramdisk
