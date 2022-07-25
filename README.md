![scalene](https://github.com/plasma-umass/scalene/raw/master/docs/scalene-image.png)

# Scalene: a high-performance CPU, GPU and memory profiler for Python

by [Emery Berger](https://emeryberger.com), [Sam Stern](https://samstern.me/), and [Juan Altmayer Pizzorno](https://github.com/jaltmayerpizzorno).

[![Scalene community Slack](https://github.com/plasma-umass/scalene/raw/master/docs/images/slack-logo.png)](https://join.slack.com/t/scaleneprofil-jge3234/shared_invite/zt-110vzrdck-xJh5d4gHnp5vKXIjYD3Uwg)[Scalene community Slack](https://join.slack.com/t/scaleneprofil-jge3234/shared_invite/zt-110vzrdck-xJh5d4gHnp5vKXIjYD3Uwg)

[![PyPI Latest Release](https://img.shields.io/pypi/v/scalene.svg)](https://pypi.org/project/scalene/)[![Downloads](https://pepy.tech/badge/scalene)](https://pepy.tech/project/scalene) [![Downloads](https://pepy.tech/badge/scalene/month)](https://pepy.tech/project/scalene) ![Python versions](https://img.shields.io/pypi/pyversions/scalene.svg?style=flat-square) ![License](https://img.shields.io/github/license/plasma-umass/scalene) [![Twitter Follow](https://img.shields.io/twitter/follow/emeryberger.svg?style=social)](https://twitter.com/emeryberger)

![Ozsvald tweet](https://github.com/plasma-umass/scalene/raw/master/docs/Ozsvald-tweet.png)

(tweet from Ian Ozsvald, author of [_High Performance Python_](https://smile.amazon.com/High-Performance-Python-Performant-Programming/dp/1492055026/ref=sr_1_1?crid=texbooks))

## About Scalene

Scalene is a high-performance CPU, GPU *and* memory profiler for Python that does a number of things that other Python profilers do not and cannot do.  It runs orders of magnitude faster than other profilers while delivering far more detailed information.

### Quick Start

#### Installing Scalene:

```console
pip install -U scalene
```

#### NEW web-based GUI

Scalene has a new web-based GUI [(demo here)](http://plasma-umass.org/scalene-gui/).

Once Scalene has profiled your program, it will launch a web browser with an interactive user interface (all processing is done locally). Hover over bars to see breakdowns of CPU and memory consumption, and click on underlined column headers to sort the columns.

[![Scalene web GUI](https://raw.githubusercontent.com/plasma-umass/scalene/master/docs/scalene-gui-example.png)](https://raw.githubusercontent.com/plasma-umass/scalene/master/docs/scalene-gui-example-full.png)

#### Using Scalene:

Commonly used options:

```console
scalene your_prog.py                             # full profile (outputs to web interface)
python3 -m scalene your_prog.py                  # equivalent alternative
scalene --cli your_prog.py                       # use the command-line only (no web interface)
scalene --cpu-only your_prog.py                  # only CPU/GPU
scalene --reduced-profile your_prog.py           # only profile lines with significant usage
scalene --profile-interval 5.0 your_prog.py      # output a new profile every five seconds
scalene (Scalene options) --- your_prog.py (...) # use --- to tell Scalene to ignore options after that point
scalene --help                                   # lists all options
```

To use Scalene programmatically in your code, invoke using `scalene` as above and then:

```Python
from scalene import scalene_profiler

# Turn profiling on
scalene_profiler.start()

# Turn profiling off
scalene_profiler.stop()
```

To use Scalene to profile specific functions, just use the `@profile` decorator and run it with Scalene:

```Python
@profile
def slow_function():
    import time
    time.sleep(3)
```

## Scalene Overview

### Scalene talk (PyCon US 2021)

[This talk](https://youtu.be/5iEf-_7mM1k) presented at PyCon 2021 walks through Scalene's advantages and how to use it to debug the performance of an application (and provides some technical details on its internals). We highly recommend watching this video!

[![Scalene presentation at PyCon 2021](https://raw.githubusercontent.com/plasma-umass/scalene/master/docs/images/scalene-video-img.png)](https://youtu.be/5iEf-_7mM1k "Scalene presentation at PyCon 2021")

### Fast and Precise

- Scalene is **_fast_**. It uses sampling instead of instrumentation or relying on Python's tracing facilities. Its overhead is typically no more than 10-20% (and often less).
- Scalene performs profiling **_at the line level_** _and_ **_per function_**, pointing to the functions and the specific lines of code responsible for the execution time in your program.

### CPU profiling

- Scalene **separates out time spent in Python from time in native code** (including libraries). Most Python programmers aren't going to optimize the performance of native code (which is usually either in the Python implementation or external libraries), so this helps developers focus their optimization efforts on the code they can actually improve.
- Scalene **highlights hotspots** (code accounting for significant percentages of CPU time or memory allocation) in red, making them even easier to spot.
- Scalene also separates out **system time**, making it easy to find I/O bottlenecks.

### GPU profiling

- Scalene reports **GPU time** (currently limited to NVIDIA-based systems).

### Memory profiling

- Scalene **profiles memory usage**. In addition to tracking CPU usage, Scalene also points to the specific lines of code responsible for memory growth. It accomplishes this via an included specialized memory allocator.
- Scalene separates out the percentage of **memory consumed by Python code vs. native code**.
- Scalene produces **_per-line_ memory profiles**.
- Scalene **identifies lines with likely memory leaks**.
- Scalene **profiles _copying volume_**, making it easy to spot inadvertent copying, especially due to crossing Python/library boundaries (e.g., accidentally converting `numpy` arrays into Python arrays, and vice versa).

### Other features

- Scalene can produce **reduced profiles** (via `--reduced-profile`) that only report lines that consume more than 1% of CPU or perform at least 100 allocations.
- Scalene supports `@profile` decorators to profile only specific functions.
- When Scalene is profiling a program launched in the background (via `&`), you can **suspend and resume profiling**.

# Comparison to Other Profilers

## Performance and Features

Below is a table comparing the **performance and features** of various profilers to Scalene.

![Performance and feature comparison](https://raw.githubusercontent.com/plasma-umass/scalene/master/docs/images/profiler-comparison.png)

- **Slowdown**: the slowdown when running a benchmark from the Pyperformance suite. Green means less than 2x overhead. Scalene's overhead is just a 35% slowdown.

Scalene has all of the following features, many of which only Scalene supports:

- **Lines or functions**: does the profiler report information only for entire functions, or for every line -- Scalene does both.
- **Unmodified Code**: works on unmodified code.
- **Threads**: supports Python threads.
- **Multiprocessing**: supports use of the `multiprocessing` library -- _Scalene only_
- **Python vs. C time**: breaks out time spent in Python vs. native code (e.g., libraries) -- _Scalene only_
- **System time**: breaks out system time (e.g., sleeping or performing I/O) -- _Scalene only_
- **Profiles memory**: reports memory consumption per line / function
- **GPU**: reports time spent on an NVIDIA GPU (if present) -- _Scalene only_
- **Memory trends**: reports memory use over time per line / function -- _Scalene only_
- **Copy volume**: reports megabytes being copied per second -- _Scalene only_
- **Detects leaks**: automatically pinpoints lines responsible for likely memory leaks -- _Scalene only_

## Output

Scalene prints annotated source code for the program being profiled
(as text, JSON (`--json`), or HTML (`--html`)) and any modules it
uses in the same directory or subdirectories (you can optionally have
it `--profile-all` and only include files with at least a
`--cpu-percent-threshold` of time).  Here is a snippet from
`pystone.py`.

![Example profile](https://raw.githubusercontent.com/plasma-umass/scalene/master/docs/images/sample-profile-pystone.png)

* **Memory usage at the top**: Visualized by "sparklines", memory consumption over the runtime of the profiled code.
* **"Time Python"**: How much time was spent in Python code.
* **"native"**: How much time was spent in non-Python code (e.g., libraries written in C/C++).
* **"system"**: How much time was spent in the system (e.g., I/O).
* **"GPU"**: (not shown here) How much time spent on the GPU, if your system has an NVIDIA GPU installed.
* **"Memory Python"**: How much of the memory allocation happened on the Python side of the code, as opposed to in non-Python code (e.g., libraries written in C/C++).
* **"net"**: Positive net memory numbers indicate total memory allocation in megabytes; negative net memory numbers indicate memory reclamation.
* **"timeline / %"**: Visualized by "sparklines", memory consumption generated by this line over the program runtime, and the percentages of total memory activity this line represents.
* **"Copy (MB/s)"**: The amount of megabytes being copied per second (see "About Scalene").

## Using Scalene

The following command runs Scalene on a provided example program.

```console
scalene test/testme.py
```

<details>
 <summary>
  Click to see all Scalene's options (available by running with <code>--help</code>)
 </summary>

```console
    % scalene --help
     usage: scalene [-h] [--outfile OUTFILE] [--html] [--reduced-profile]
                    [--profile-interval PROFILE_INTERVAL] [--cpu-only]
                    [--profile-all] [--profile-only PROFILE_ONLY]
                    [--use-virtual-time]
                    [--cpu-percent-threshold CPU_PERCENT_THRESHOLD]
                    [--cpu-sampling-rate CPU_SAMPLING_RATE]
                    [--malloc-threshold MALLOC_THRESHOLD]
     
     Scalene: a high-precision CPU and memory profiler.
     https://github.com/plasma-umass/scalene
     
     command-line:
        % scalene [options] yourprogram.py
     or
        % python3 -m scalene [options] yourprogram.py
     
     in Jupyter, line mode:
        %scrun [options] statement
     
     in Jupyter, cell mode:
        %%scalene [options]
        code...
        code...
     
     optional arguments:
       -h, --help            show this help message and exit
       --outfile OUTFILE     file to hold profiler output (default: stdout)
       --html                output as HTML (default: text)
       --reduced-profile     generate a reduced profile, with non-zero lines only (default: False)
       --profile-interval PROFILE_INTERVAL
                             output profiles every so many seconds (default: inf)
       --cpu-only            only profile CPU time (default: profile CPU, memory, and copying)
       --profile-all         profile all executed code, not just the target program (default: only the target program)
       --profile-only PROFILE_ONLY
                             profile only code in filenames that contain the given strings, separated by commas (default: no restrictions)
       --use-virtual-time    measure only CPU time, not time spent in I/O or blocking (default: False)
       --cpu-percent-threshold CPU_PERCENT_THRESHOLD
                             only report profiles with at least this percent of CPU time (default: 1%)
       --cpu-sampling-rate CPU_SAMPLING_RATE
                             CPU sampling rate (default: every 0.01s)
       --malloc-threshold MALLOC_THRESHOLD
                             only report profiles with at least this many allocations (default: 100)
     
     When running Scalene in the background, you can suspend/resume profiling
     for the process ID that Scalene reports. For example:
     
        % python3 -m scalene [options] yourprogram.py &
      Scalene now profiling process 12345
        to suspend profiling: python3 -m scalene.profile --off --pid 12345
        to resume profiling:  python3 -m scalene.profile --on  --pid 12345
```
</details>

### Scalene with Jupyter

<details>
<summary>
Instructions for installing and using Scalene with Jupyter notebooks
</summary>

[This notebook](https://nbviewer.jupyter.org/github/plasma-umass/scalene/blob/master/docs/scalene-demo.ipynb) illustrates the use of Scalene in Jupyter.

Installation:

```console
!pip install scalene
%load_ext scalene
```

Line mode:

```console
%scrun [options] statement
```

Cell mode:

```console
%%scalene [options]
code...
code...
```
</details>

## Installation

<details open>
<summary>Using <code>pip</code> (Mac OS X, Linux, Windows, and WSL2)</summary>

Scalene is distributed as a `pip` package and works on Mac OS X, Linux (including Ubuntu in [Windows WSL2](https://docs.microsoft.com/en-us/windows/wsl/wsl2-index)) and (with limitations) Windows platforms. (**Note**: the Windows version isn't yet complete; it currently only supports CPU profiling.)

You can install it as follows:
```console
  % pip install -U scalene
```

or
```console
  % python3 -m pip install -U scalene
```

You may need to install some packages first.

See https://stackoverflow.com/a/19344978/4954434 for full instructions for all Linux flavors.

For Ubuntu/Debian:

```console
  % sudo apt install git python3-all-dev
```
</details>


<details>
<summary>Using <code>Homebrew</code> (Mac OS X)</summary>

As an alternative to `pip`, you can use Homebrew to install the current version of Scalene from this repository:

```console
  % brew tap plasma-umass/scalene
  % brew install --head plasma-umass/scalene/scalene
```
</details>

<details>
<summary>On ArchLinux</summary>

You can install Scalene on Arch Linux via the [AUR
package](https://aur.archlinux.org/packages/python-scalene-git/). Use your favorite AUR helper, or
manually download the `PKGBUILD` and run `makepkg -cirs` to build. Note that this will place
`libscalene.so` in `/usr/lib`; modify the below usage instructions accordingly.
</details>

# Asked Questions

**Q: Is there any way to get shorter profiles or do more targeted profiling?**

**A:** Yes! There are several options:

1. Use `--reduced-profile` to include only lines and files with memory/CPU/GPU activity.
2. Use `--profile-only` to include only filenames containing specific strings (as in, `--profile-only foo,bar,baz`).
3. Decorate functions of interest with `@profile` to have Scalene report _only_ those functions.
4. Turn profiling on and off programmatically by importing Scalene (`import scalene`) and then turning profiling on and off via `scalene_profiler.start()` and `scalene_profiler.stop()`. By default, Scalene runs with profiling on, so to delay profiling until desired, use the `--off` command-line option (`python3 -m scalene --off yourprogram.py`).

**Q: How do I run Scalene in PyCharm?**

**A:**  In PyCharm, you can run Scalene at the command line by opening the terminal at the bottom of the IDE and running a Scalene command (e.g., `python -m scalene <your program>`). Use the options `--html` and `--outfile <your output.html>` to generate an HTML file that you can then view in the IDE.

**Q: How do I use Scalene with Django?**

**A:** Pass in the `--noreload` option (see https://github.com/plasma-umass/scalene/issues/178).

**Q: How do I use Scalene with PyTorch on the Mac?**

**A:** Scalene works with PyTorch version 1.5.1 on Mac OS X. There's a bug in newer versions of PyTorch (https://github.com/pytorch/pytorch/issues/57185) that interferes with Scalene (discussion here: https://github.com/plasma-umass/scalene/issues/110), but only on Macs.

# Technical Information

For technical details on Scalene, please see the following paper: [Scalene: Scripting-Language Aware Profiling for Python](https://github.com/plasma-umass/scalene/raw/master/docs/scalene-paper.pdf) ([arXiv link](https://arxiv.org/abs/2006.03879)).

# Success Stories

If you use Scalene to successfully debug a performance problem, please [add a comment to this issue](https://github.com/plasma-umass/scalene/issues/58)!

# Acknowledgements

Logo created by [Sophia Berger](https://www.linkedin.com/in/sophia-berger/).

This material is based upon work supported by the National Science
Foundation under Grant No. 1955610. Any opinions, findings, and
conclusions or recommendations expressed in this material are those of
the author(s) and do not necessarily reflect the views of the National
Science Foundation.
