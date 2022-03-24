# optview2-cpython
Results of OptView2 analysis on CPython.
The c++ compiler used was clang10, the CPython repo was cloned on March 24st 2022.

View as html via: https://ofekshilon.github.io/optview2-cpython/

Exact steps used for htmls creation, on Ubuntu 20:

```bash
$ git clone git@github.com:python/cpython.git
$ git clone git@github.com:OfekShilon/optview2.git
$ # If you didn't setup ssh on github, replace `git@` with `https://`
$ cd cpython
$ # You might need to `sudo apt-get build-dep python3` if you're missing needed packages. More info at https://devguide.python.org/setup/#install-dependencies
$ export CC=clang
$ export CFLAGS="-fsave-optimization-record -fdiagnostics-show-hotness"
$ ./configure  --enable-optimizations --prefix=<your folder of choice>   # This enables pgo and lto
$ make
$ make install  # Puts the cpython bin into into --prefix folder, eg for profiling. Not necessary for the html creation, 
$ ../optview2/opt-viewer.py -j10 --output-dir <your htmls folder> --source-dir . .
```

Feel free to roam around and investigate.

To measure impact of code changes on the resulting CPython build, try `pyperformance`. In the script below replace `./python3` with wherever you built into (the `--prefix` above).
```bash
$ ./python3 -m pip install pyperformance pyperf pyaes dulwich chameleon django sqlalchemy sympy tornado mako
$ pyperformance run --python="./python3" -o py-vanilla.json > bench.out 2>&1
```
Perform your code changes and build, repeat the pyperformance run and dump into a different json, then:
```bash
$ pyperformance compare py-vanilla.json py-changed.json
```

