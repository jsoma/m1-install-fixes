# Install scipy on an M1 Mac

This page might solve installation issues involving:

* scipy
* numpy
* plotnine

## What the problem is

When installing the Python package [scipy](https://scipy.org/) on an M1 laptop, you might run into a problem! Usually when you install a Python package, you download a "wheel," which is a version that's pre-made for a computer just like yours.

Currently scipy doesn't provide wheels for versions of scipy before 1.80.0 (which isn't released yet), so you computer has to build it from scratch! Along with this, scipy depends on some particular add-ons to [numpy](https://numpy.org/), another Python package. Numpy installs fine on M1 macs, but there's an extra piece called BLAS that [does advanced linear algebra stuff](https://numpy.org/devdocs/user/building.html#accelerated-blas-lapack-libraries), which isn't installed by default.

So life looks like this:

1. You install numpy (and probably pandas), works fine.
2. You install scipy, it won't install. Complains about numpy.
3. You're confused because numpy _is already installed_. But it's the version without BLAS!

## How to fix it

First you'll need to **uninstall numpy**. The version you have doesn't have BLAS, so we don't want it any more.

```bash
pip uninstall -y numpy
```

If you don't do the `-y` part it might warn you that other packages depend on numpy. That's fine – we're going to reinstall it in a second. If it asks whether you *really* want to uninstall it, press `y` to confirm you want it uninstalled.

Now we're going to use [Homebrew](https://brew.sh/) to install OpenBLAS, a BLAS library. Then we'll install numpy again, telling it where to find BLAS, and also install scipy while we're at it.

```bash
brew install openblas
OPENBLAS="$(brew --prefix openblas)" pip install numpy scipy
```

To confirm that it worked, you can ask python what version of scipy is installed.

```bash
python -c 'import scipy; print(scipy.__version__)'
```

If it gives you a number, it worked. If it prints `ModuleNotFoundError: No module named 'scipy'`, it didn't work. [Ask for more help here.](https://github.com/jsoma/m1-install-fixes/issues/new)

## The error message

Your error message might look something like this:

```
(requires-python:>=3.7,<3.11). Command errored out with exit status 1: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /Users/username/.pyenv/versions/3.9.7/lib/python3.9/site-packages/pip/_vendor/pep517/in_process/_in_process.py prepare_metadata_for_build_wheel /var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/tmp3cwkeq2r Check the logs for full command output.
......
ERROR: Command errored out with exit status 1:
   command: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-standalone-pip-kl2ixo88/__env_pip__.zip/pip install --ignore-installed --no-user --prefix /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-build-env-4228eozy/overlay --no-warn-script-location --no-binary :none: --only-binary :none: -i https://pypi.org/simple -- 'wheel<0.37.0' 'setuptools<58.0.0' 'Cython>=0.29.18,<3.0' 'pybind11>=2.4.3,<2.7.0' pythran==0.9.11 'numpy==1.19.2; python_version=='"'"'3.7'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.19.2; python_version=='"'"'3.8'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.16.5; python_version=='"'"'3.7'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.17.3; python_version=='"'"'3.8'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.19.3; python_version=='"'"'3.9'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.20.0; python_version=='"'"'3.7'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version=='"'"'3.8'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version=='"'"'3.9'"'"' and platform_python_implementation=='"'"'PyPy'"'"''
       cwd: None
  Complete output (943 lines):
  Ignoring numpy: markers 'python_version == "3.7" and platform_machine == "aarch64"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.8" and platform_machine == "aarch64"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.7" and platform_machine != "aarch64" and platform_python_implementation != "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.8" and platform_machine != "aarch64" and platform_python_implementation != "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.7" and platform_python_implementation == "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.8" and platform_python_implementation == "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.9" and platform_python_implementation == "PyPy"' don't match your environment
  Collecting wheel<0.37.0
    Using cached wheel-0.36.2-py2.py3-none-any.whl (35 kB)
  Collecting setuptools<58.0.0
    Using cached setuptools-57.5.0-py3-none-any.whl (819 kB)
  Collecting Cython<3.0,>=0.29.18
    Using cached Cython-0.29.26-py2.py3-none-any.whl (983 kB)
  Collecting pybind11<2.7.0,>=2.4.3
    Using cached pybind11-2.6.2-py2.py3-none-any.whl (191 kB)
  Collecting pythran==0.9.11
    Using cached pythran-0.9.11-py3-none-any.whl (4.2 MB)
  Collecting numpy==1.19.3
    Using cached numpy-1.19.3.zip (7.3 MB)
    Installing build dependencies: started
    Installing build dependencies: finished with status 'done'
    Getting requirements to build wheel: started
    Getting requirements to build wheel: finished with status 'done'
      Preparing wheel metadata: started
      Preparing wheel metadata: finished with status 'done'
  Collecting six
    Using cached six-1.16.0-py2.py3-none-any.whl (11 kB)
  Collecting beniget~=0.3.0
    Using cached beniget-0.3.0-py3-none-any.whl (9.3 kB)
  Collecting networkx>=2
    Using cached networkx-2.6.3-py3-none-any.whl (1.9 MB)
  Collecting gast~=0.4.0
    Using cached gast-0.4.0-py3-none-any.whl (9.8 kB)
  Collecting ply>=3.4
    Using cached ply-3.11-py2.py3-none-any.whl (49 kB)
  Collecting decorator
    Using cached decorator-5.1.1-py3-none-any.whl (9.1 kB)
  Building wheels for collected packages: numpy
    Building wheel for numpy (PEP 517): started
    Building wheel for numpy (PEP 517): finished with status 'error'
    ERROR: Command errored out with exit status 1:
     command: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/tmpqf1p9tew_in_process.py build_wheel /var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/tmp6dhojaeo
         cwd: /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c1j3j3l0/numpy_f454c40d91704dd58998cc365c4318e2
    Complete output (893 lines):
    Running from numpy source directory.
    numpy/random/_bounded_integers.pxd.in has not changed
    numpy/random/_philox.pyx has not changed
    numpy/random/_bounded_integers.pyx.in has not changed
    numpy/random/_sfc64.pyx has not changed
    numpy/random/_mt19937.pyx has not changed
    numpy/random/bit_generator.pyx has not changed
    Processing numpy/random/_bounded_integers.pyx
    numpy/random/mtrand.pyx has not changed
    numpy/random/_generator.pyx has not changed
    numpy/random/_pcg64.pyx has not changed
    numpy/random/_common.pyx has not changed
    Cythonizing sources
    blas_opt_info:
    blas_mkl_info:
    customize UnixCCompiler
      libraries mkl_rt not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    blis_info:
      libraries blis not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_info:
      libraries openblas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_blas_threads_info:
    Setting PTATLAS=ATLAS
      libraries tatlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_blas_info:
      libraries satlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_blas_threads_info:
    Setting PTATLAS=ATLAS
      libraries ptf77blas,ptcblas,atlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_blas_info:
      libraries f77blas,cblas,atlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    accelerate_info:
      libraries accelerate not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
    Library accelerate was not found. Ignoring
      libraries veclib not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
    Library veclib was not found. Ignoring
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
    non-existing path in 'numpy/distutils': 'site.cfg'
    lapack_opt_info:
    lapack_mkl_info:
      libraries mkl_rt not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_lapack_info:
      libraries openblas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_clapack_info:
      libraries openblas,lapack not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    flame_info:
      libraries flame not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_threads_info:
    Setting PTATLAS=ATLAS
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries tatlas,tatlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries tatlas,tatlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries tatlas,tatlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_3_10_threads_info'>
      NOT AVAILABLE
  
    atlas_3_10_info:
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries satlas,satlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries satlas,satlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries satlas,satlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_3_10_info'>
      NOT AVAILABLE
  
    atlas_threads_info:
    Setting PTATLAS=ATLAS
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries ptf77blas,ptcblas,atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries ptf77blas,ptcblas,atlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries ptf77blas,ptcblas,atlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_threads_info'>
      NOT AVAILABLE
  
    atlas_info:
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries f77blas,cblas,atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries f77blas,cblas,atlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries f77blas,cblas,atlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_info'>
      NOT AVAILABLE
  
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
    /Users/username/.pyenv/versions/3.9.7/lib/python3.9/distutils/dist.py:274: UserWarning: Unknown distribution option: 'define_macros'
      warnings.warn(msg)
    running bdist_wheel
    running build
    running config_cc
    unifing config_cc, config, build_clib, build_ext, build commands --compiler options
    running config_fc
    unifing config_fc, config, build_clib, build_ext, build commands --fcompiler options
    running build_src
    build_src
    building py_modules sources
    building library "npymath" sources
    Could not locate executable gfortran
    Could not locate executable f95
    Could not locate executable f90
    Could not locate executable f77
    Could not locate executable xlf90
    Could not locate executable xlf
    Could not locate executable ifort
    Could not locate executable ifc
    Could not locate executable g77
    Could not locate executable g95
    Could not locate executable pgfortran
    don't know how to compile Fortran code on platform 'posix'
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath' to include_dirs.
    None - nothing done with h_files = ['build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/npy_math_internal.h']
    building library "npysort" sources
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common' to include_dirs.
    None - nothing done with h_files = ['build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_sort.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_partition.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_binsearch.h']
    building library "npyrandom" sources
    building extension "numpy.core._multiarray_tests" sources
    building extension "numpy.core._multiarray_umath" sources
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath' to include_dirs.
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath' to include_dirs.
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common' to include_dirs.
    numpy.core - nothing done with h_files = ['build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/funcs.inc', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/simd.inc', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/loops.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/matmul.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/clip.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/npy_math_internal.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/templ_common.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy/config.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy/_numpyconfig.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy/__multiarray_api.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy/__ufunc_api.h']
    building extension "numpy.core._umath_tests" sources
    building extension "numpy.core._rational_tests" sources
    building extension "numpy.core._struct_ufunc_tests" sources
    building extension "numpy.core._operand_flag_tests" sources
    building extension "numpy.fft._pocketfft_internal" sources
    building extension "numpy.linalg.lapack_lite" sources
    building extension "numpy.linalg._umath_linalg" sources
    building extension "numpy.random._mt19937" sources
    building extension "numpy.random._philox" sources
    building extension "numpy.random._pcg64" sources
    building extension "numpy.random._sfc64" sources
    building extension "numpy.random._common" sources
    building extension "numpy.random.bit_generator" sources
    building extension "numpy.random._generator" sources
    building extension "numpy.random._bounded_integers" sources
    building extension "numpy.random.mtrand" sources
    building data_files sources
    build_src: building npy-pkg config files
    running build_py
    creating build/lib.macosx-11.4-arm64-3.9
    creating build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/conftest.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/version.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/_globals.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/dual.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/_distributor_init.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/ctypeslib.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/matlib.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/_pytesttester.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying build/src.macosx-11.4-arm64-3.9/numpy/__config__.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    creating build/lib.macosx-11.4-arm64-3.9/numpy/compat
    copying numpy/compat/py3k.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat
    copying numpy/compat/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat
    copying numpy/compat/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat
    copying numpy/compat/_inspect.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat
    creating build/lib.macosx-11.4-arm64-3.9/numpy/compat/tests
    copying numpy/compat/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat/tests
    copying numpy/compat/tests/test_compat.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/umath.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/fromnumeric.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_dtype.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_add_newdocs.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_methods.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_internal.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_string_helpers.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/multiarray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_asarray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/records.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/setup_common.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/memmap.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/overrides.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/getlimits.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_dtype_ctypes.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/defchararray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/shape_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/machar.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/numeric.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/function_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/einsumfunc.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/umath_tests.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_ufunc_config.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_exceptions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/numerictypes.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_type_aliases.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/cversions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/arrayprint.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/code_generators/generate_numpy_api.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    creating build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_numerictypes.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalar_methods.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalarmath.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_item_selection.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_machar.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_unicode.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_arrayprint.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalarbuffer.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_indexerrors.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_print.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_half.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_mem_overlap.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_shape_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_deprecations.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_errstate.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_records.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalarinherit.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_indexing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_umath.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_numeric.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_function_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_datetime.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test__exceptions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_extint128.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_umath_complex.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/_locales.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_defchararray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_conversion_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalarprint.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_abc.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_ufunc.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_dtype.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_umath_accuracy.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_getlimits.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_einsum.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_api.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_longdouble.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_overrides.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalar_ctors.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_multiarray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_memmap.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_nditer.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_cpu_features.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_protocols.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/unixccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/numpy_distribution.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/conv_template.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/cpuinfo.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/ccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/msvc9compiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/npy_pkg_config.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/misc_util.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/log.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/line_endings.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/lib2def.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/pathccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/system_info.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/core.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/exec_command.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/from_template.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/mingw32ccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/extension.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/msvccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/intelccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/_shell_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying build/src.macosx-11.4-arm64-3.9/numpy/distutils/__config__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    creating build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/config_compiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_ext.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/config.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/install_headers.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_py.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_src.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/sdist.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_scripts.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/bdist_rpm.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/install_clib.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_clib.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/autodist.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/egg_info.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/install.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/develop.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/install_data.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    creating build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/gnu.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/compaq.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/intel.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/none.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/nag.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/pg.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/ibm.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/sun.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/nv.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/lahey.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/g95.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/mips.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/hpux.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/environment.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/pathf95.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/absoft.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/vast.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    creating build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_system_info.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_mingw32ccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_from_template.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_fcompiler_intel.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_misc_util.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_fcompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_shell_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_exec_command.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_npy_pkg_config.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_fcompiler_nagfor.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_fcompiler_gnu.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/misc.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/internals.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/creation.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/dispatch.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/constants.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/ufuncs.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/broadcasting.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/basics.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/subclassing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/indexing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/byteswapping.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/structured_arrays.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/glossary.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    creating build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/cfuncs.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/common_rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/crackfortran.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/cb_rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/f2py2e.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/func2subr.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/__version__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/diagnose.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/capi_maps.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/f90mod_rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/f2py_testing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/use_rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/auxfuncs.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/__main__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    creating build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_mixed.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_logical.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_assumed_shape.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_common.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_kind.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_array_from_pyobj.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_real.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/util.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_size.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_callback.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_string.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_quoted_character.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_parameter.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_semicolon_split.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_compile_function.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_block_docstring.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_integer.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_character.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_complex.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_crackfortran.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/fft
    copying numpy/fft/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft
    copying numpy/fft/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft
    copying numpy/fft/helper.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft
    copying numpy/fft/_pocketfft.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft
    creating build/lib.macosx-11.4-arm64-3.9/numpy/fft/tests
    copying numpy/fft/tests/test_pocketfft.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft/tests
    copying numpy/fft/tests/test_helper.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft/tests
    copying numpy/fft/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/_iotools.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/mixins.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/nanfunctions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/recfunctions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/histograms.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/scimath.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/_version.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/user_array.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/format.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/twodim_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/financial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/index_tricks.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/npyio.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/shape_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/stride_tricks.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/arrayterator.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/function_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/arraysetops.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/arraypad.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/type_check.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/polynomial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/_datasource.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/ufunclike.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    creating build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_type_check.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_twodim_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_polynomial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test__iotools.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_shape_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_ufunclike.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_index_tricks.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_arrayterator.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test__version.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_io.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_arraysetops.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_function_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_arraypad.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_mixins.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_packbits.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test__datasource.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_stride_tricks.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_financial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_recfunctions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_nanfunctions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_format.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_histograms.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/linalg
    copying numpy/linalg/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg
    copying numpy/linalg/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg
    copying numpy/linalg/linalg.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg
    creating build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/test_linalg.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/test_deprecations.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/test_build.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/extras.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/testutils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/core.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/bench.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/timer_comparison.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/mrecords.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    creating build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_old_ma.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_core.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_deprecations.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_subclassing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_extras.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_mrecords.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib
    copying numpy/matrixlib/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib
    copying numpy/matrixlib/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib
    copying numpy/matrixlib/defmatrix.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib
    creating build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_matrix_linalg.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_defmatrix.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_interaction.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_numeric.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_masked_matrix.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_multiarray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/laguerre.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/_polybase.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/polyutils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/hermite_e.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/chebyshev.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/polynomial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/legendre.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/hermite.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    creating build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_chebyshev.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_hermite_e.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_polynomial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_laguerre.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_legendre.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_printing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_hermite.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_classes.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_polyutils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/random
    copying numpy/random/_pickle.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random
    copying numpy/random/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random
    copying numpy/random/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random
    creating build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_generator_mt19937.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_randomstate.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_direct.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_extending.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_smoke.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_randomstate_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_seed_sequence.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_generator_mt19937_regressions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_random.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/testing
    copying numpy/testing/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing
    copying numpy/testing/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing
    copying numpy/testing/utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing
    copying numpy/testing/print_coercion_tables.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing
    creating build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/nosetester.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/noseclasses.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/parameterized.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/decorators.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    creating build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    copying numpy/testing/tests/test_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    copying numpy/testing/tests/test_decorators.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    copying numpy/testing/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    copying numpy/testing/tests/test_doctesting.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_warnings.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_matlib.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_ctypeslib.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_numpy_version.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_reloading.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_public_api.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_scripts.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    running build_clib
    customize UnixCCompiler
    customize UnixCCompiler using new_build_clib
    building 'npymath' library
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9
    creating build/temp.macosx-11.4-arm64-3.9/numpy
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src/npymath
    creating build/temp.macosx-11.4-arm64-3.9/build
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath
    compile options: '-Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    clang: numpy/core/src/npymath/npy_math.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/ieee754.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/npy_math_complex.c
    clang: numpy/core/src/npymath/halffloat.c
    In file included from numpy/core/src/npymath/npy_math.c:9:
    numpy/core/src/npymath/npy_math_internal.h.src:490:21: warning: incompatible pointer types passing 'npy_longdouble *' (aka 'double *') to parameter of type 'long double *' [-Wincompatible-pointer-types]
        return modfl(x, iptr);
                        ^~~~
    /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/math.h:394:52: note: passing argument to parameter here
    extern long double modfl(long double, long double *);
                                                       ^
    1 warning generated.
    ar: adding 4 object files to build/temp.macosx-11.4-arm64-3.9/libnpymath.a
    ranlib:@ build/temp.macosx-11.4-arm64-3.9/libnpymath.a
    building 'npysort' library
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort
    compile options: '-Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/quicksort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/mergesort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/timsort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/radixsort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/heapsort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/selection.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/binsearch.c
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    22 warnings generated.
    ar: adding 7 object files to build/temp.macosx-11.4-arm64-3.9/libnpysort.a
    ranlib:@ build/temp.macosx-11.4-arm64-3.9/libnpysort.a
    building 'npyrandom' library
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9/numpy/random
    creating build/temp.macosx-11.4-arm64-3.9/numpy/random/src
    creating build/temp.macosx-11.4-arm64-3.9/numpy/random/src/distributions
    compile options: '-Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    clang: numpy/random/src/distributions/logfactorial.c
    clang: numpy/random/src/distributions/distributions.c
    clang: numpy/random/src/distributions/random_mvhg_count.c
    clang: numpy/random/src/distributions/random_hypergeometric.c
    clang: numpy/random/src/distributions/random_mvhg_marginals.c
    ar: adding 5 object files to build/temp.macosx-11.4-arm64-3.9/libnpyrandom.a
    ranlib:@ build/temp.macosx-11.4-arm64-3.9/libnpyrandom.a
    running build_ext
    customize UnixCCompiler
    customize UnixCCompiler using new_build_ext
    building 'numpy.core._multiarray_tests' extension
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src/common
    compile options: '-DNPY_INTERNAL_BUILD=1 -DHAVE_NPY_CONFIG_H=1 -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE=1 -D_LARGEFILE64_SOURCE=1 -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/_multiarray_tests.c
    clang: numpy/core/src/common/mem_overlap.c
    In file included from numpy/core/src/multiarray/_multiarray_tests.c.src:7:
    In file included from numpy/core/include/numpy/npy_math.h:596:
    numpy/core/src/npymath/npy_math_internal.h.src:490:21: warning: incompatible pointer types passing 'npy_longdouble *' (aka 'double *') to parameter of type 'long double *' [-Wincompatible-pointer-types]
        return modfl(x, iptr);
                        ^~~~
    /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/math.h:394:52: note: passing argument to parameter here
    extern long double modfl(long double, long double *);
                                                       ^
    numpy/core/src/multiarray/_multiarray_tests.c.src:1895:61: warning: format specifies type 'long double' but the argument has type 'npy_longdouble' (aka 'double') [-Wformat]
            PyOS_snprintf(str, sizeof(str), "%.*Lg", precision, x);
                                             ~~~~~              ^
                                             %.*g
    2 warnings generated.
    clang -bundle -undefined dynamic_lookup -L/opt/homebrew/opt/readline/lib -L/opt/homebrew/opt/readline/lib -L/Users/username/.pyenv/versions/3.9.7/lib -L/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/lib -L/opt/homebrew/opt/readline/lib -L/opt/homebrew/opt/readline/lib -L/Users/username/.pyenv/versions/3.9.7/lib -L/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/lib build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/_multiarray_tests.o build/temp.macosx-11.4-arm64-3.9/numpy/core/src/common/mem_overlap.o -Lbuild/temp.macosx-11.4-arm64-3.9 -lnpymath -o build/lib.macosx-11.4-arm64-3.9/numpy/core/_multiarray_tests.cpython-39-darwin.so
    building 'numpy.core._multiarray_umath' extension
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src/multiarray
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src/umath
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/common
    creating build/temp.macosx-11.4-arm64-3.9/private
    creating build/temp.macosx-11.4-arm64-3.9/private/var
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c1j3j3l0
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c1j3j3l0/numpy_f454c40d91704dd58998cc365c4318e2
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c1j3j3l0/numpy_f454c40d91704dd58998cc365c4318e2/numpy
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c1j3j3l0/numpy_f454c40d91704dd58998cc365c4318e2/numpy/_build_utils
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c1j3j3l0/numpy_f454c40d91704dd58998cc365c4318e2/numpy/_build_utils/src
    compile options: '-DNPY_INTERNAL_BUILD=1 -DHAVE_NPY_CONFIG_H=1 -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE=1 -D_LARGEFILE64_SOURCE=1 -DNO_ATLAS_INFO=3 -DHAVE_CBLAS -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/umath -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    extra options: '-faltivec -I/System/Library/Frameworks/vecLib.framework/Headers'
    clang: numpy/core/src/multiarray/alloc.c
    clang: numpy/core/src/multiarray/array_assign_scalar.c
    clang: numpy/core/src/multiarray/buffer.c
    clang: numpy/core/src/multiarray/common.c
    clang: numpy/core/src/multiarray/conversion_utils.c
    clang: numpy/core/src/multiarray/datetime_strings.c
    clang: numpy/core/src/multiarray/descriptor.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/einsum.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/multiarray/hashdescr.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/multiarray/multiarraymodule.c
    clang: numpy/core/src/multiarray/nditer_constr.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/lowlevel_strided_loops.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/multiarray/scalarapi.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/multiarray/refcount.c
    clang: numpy/core/src/multiarray/temp_elide.c
    clang: numpy/core/src/multiarray/vdot.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/loops.c
    clang: numpy/core/src/umath/ufunc_object.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/umath/ufunc_type_resolution.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/common/array_assign.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/ieee754.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/common/ucsnarrow.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c1j3j3l0/numpy_f454c40d91704dd58998cc365c4318e2/numpy/_build_utils/src/apple_sgemv_fix.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_cpu_features.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    error: Command "clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -DNPY_INTERNAL_BUILD=1 -DHAVE_NPY_CONFIG_H=1 -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE=1 -D_LARGEFILE64_SOURCE=1 -DNO_ATLAS_INFO=3 -DHAVE_CBLAS -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/umath -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c numpy/core/src/multiarray/common.c -o build/temp.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/common.o -MMD -MF build/temp.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/common.o.d -faltivec -I/System/Library/Frameworks/vecLib.framework/Headers" failed with exit status 1
    ----------------------------------------
    ERROR: Failed building wheel for numpy
  Failed to build numpy
  ERROR: Could not build wheels for numpy which use PEP 517 and cannot be installed directly
  WARNING: You are using pip version 21.2.3; however, version 21.3.1 is available.
  You should consider upgrading via the '/Users/username/.pyenv/versions/3.9.7/bin/python3.9 -m pip install --upgrade pip' command.
  ----------------------------------------
WARNING: Discarding https://files.pythonhosted.org/packages/47/33/a24aec22b7be7fdb10ec117a95e1e4099890d8bbc6646902f443fc7719d1/scipy-1.7.1.tar.gz#sha256=6b47d5fa7ea651054362561a28b1ccc8da9368a39514c1bbf6c0977a1c376764 (from https://pypi.org/simple/scipy/) (requires-python:>=3.7,<3.10). Command errored out with exit status 1: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-standalone-pip-kl2ixo88/__env_pip__.zip/pip install --ignore-installed --no-user --prefix /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-build-env-4228eozy/overlay --no-warn-script-location --no-binary :none: --only-binary :none: -i https://pypi.org/simple -- 'wheel<0.37.0' 'setuptools<58.0.0' 'Cython>=0.29.18,<3.0' 'pybind11>=2.4.3,<2.7.0' pythran==0.9.11 'numpy==1.19.2; python_version=='"'"'3.7'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.19.2; python_version=='"'"'3.8'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.16.5; python_version=='"'"'3.7'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.17.3; python_version=='"'"'3.8'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.19.3; python_version=='"'"'3.9'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.20.0; python_version=='"'"'3.7'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version=='"'"'3.8'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version=='"'"'3.9'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' Check the logs for full command output.
  Using cached scipy-1.7.0.tar.gz (36.1 MB)
  Installing build dependencies ... error
  ERROR: Command errored out with exit status 1:
   command: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-standalone-pip-rk2v957g/__env_pip__.zip/pip install --ignore-installed --no-user --prefix /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-build-env-xhjoou88/overlay --no-warn-script-location --no-binary :none: --only-binary :none: -i https://pypi.org/simple -- 'wheel<0.37.0' 'setuptools<58.0.0' 'Cython>=0.29.18,<3.0' 'pybind11>=2.4.3,<2.7.0' pythran==0.9.11 'numpy==1.19.2; python_version=='"'"'3.7'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.19.2; python_version=='"'"'3.8'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.16.5; python_version=='"'"'3.7'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.17.3; python_version=='"'"'3.8'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.19.3; python_version=='"'"'3.9'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.20.0; python_version=='"'"'3.7'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version=='"'"'3.8'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version=='"'"'3.9'"'"' and platform_python_implementation=='"'"'PyPy'"'"''
       cwd: None
  Complete output (943 lines):
  Ignoring numpy: markers 'python_version == "3.7" and platform_machine == "aarch64"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.8" and platform_machine == "aarch64"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.7" and platform_machine != "aarch64" and platform_python_implementation != "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.8" and platform_machine != "aarch64" and platform_python_implementation != "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.7" and platform_python_implementation == "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.8" and platform_python_implementation == "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.9" and platform_python_implementation == "PyPy"' don't match your environment
  Collecting wheel<0.37.0
    Using cached wheel-0.36.2-py2.py3-none-any.whl (35 kB)
  Collecting setuptools<58.0.0
    Using cached setuptools-57.5.0-py3-none-any.whl (819 kB)
  Collecting Cython<3.0,>=0.29.18
    Using cached Cython-0.29.26-py2.py3-none-any.whl (983 kB)
  Collecting pybind11<2.7.0,>=2.4.3
    Using cached pybind11-2.6.2-py2.py3-none-any.whl (191 kB)
  Collecting pythran==0.9.11
    Using cached pythran-0.9.11-py3-none-any.whl (4.2 MB)
  Collecting numpy==1.19.3
    Using cached numpy-1.19.3.zip (7.3 MB)
    Installing build dependencies: started
    Installing build dependencies: finished with status 'done'
    Getting requirements to build wheel: started
    Getting requirements to build wheel: finished with status 'done'
      Preparing wheel metadata: started
      Preparing wheel metadata: finished with status 'done'
  Collecting decorator
    Using cached decorator-5.1.1-py3-none-any.whl (9.1 kB)
  Collecting beniget~=0.3.0
    Using cached beniget-0.3.0-py3-none-any.whl (9.3 kB)
  Collecting gast~=0.4.0
    Using cached gast-0.4.0-py3-none-any.whl (9.8 kB)
  Collecting six
    Using cached six-1.16.0-py2.py3-none-any.whl (11 kB)
  Collecting ply>=3.4
    Using cached ply-3.11-py2.py3-none-any.whl (49 kB)
  Collecting networkx>=2
    Using cached networkx-2.6.3-py3-none-any.whl (1.9 MB)
  Building wheels for collected packages: numpy
    Building wheel for numpy (PEP 517): started
    Building wheel for numpy (PEP 517): finished with status 'error'
    ERROR: Command errored out with exit status 1:
     command: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/tmp_m6n4wpq_in_process.py build_wheel /var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/tmpqyopsf5b
         cwd: /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c8ym9nev/numpy_19e07fc3bb404ccdbeee000fa1acbc92
    Complete output (893 lines):
    Running from numpy source directory.
    numpy/random/_bounded_integers.pxd.in has not changed
    numpy/random/_philox.pyx has not changed
    numpy/random/_bounded_integers.pyx.in has not changed
    numpy/random/_sfc64.pyx has not changed
    numpy/random/_mt19937.pyx has not changed
    numpy/random/bit_generator.pyx has not changed
    Processing numpy/random/_bounded_integers.pyx
    numpy/random/mtrand.pyx has not changed
    numpy/random/_generator.pyx has not changed
    numpy/random/_pcg64.pyx has not changed
    numpy/random/_common.pyx has not changed
    Cythonizing sources
    blas_opt_info:
    blas_mkl_info:
    customize UnixCCompiler
      libraries mkl_rt not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    blis_info:
      libraries blis not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_info:
      libraries openblas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_blas_threads_info:
    Setting PTATLAS=ATLAS
      libraries tatlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_blas_info:
      libraries satlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_blas_threads_info:
    Setting PTATLAS=ATLAS
      libraries ptf77blas,ptcblas,atlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_blas_info:
      libraries f77blas,cblas,atlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    accelerate_info:
      libraries accelerate not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
    Library accelerate was not found. Ignoring
      libraries veclib not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
    Library veclib was not found. Ignoring
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
    non-existing path in 'numpy/distutils': 'site.cfg'
    lapack_opt_info:
    lapack_mkl_info:
      libraries mkl_rt not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_lapack_info:
      libraries openblas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_clapack_info:
      libraries openblas,lapack not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    flame_info:
      libraries flame not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_threads_info:
    Setting PTATLAS=ATLAS
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries tatlas,tatlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries tatlas,tatlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries tatlas,tatlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_3_10_threads_info'>
      NOT AVAILABLE
  
    atlas_3_10_info:
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries satlas,satlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries satlas,satlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries satlas,satlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_3_10_info'>
      NOT AVAILABLE
  
    atlas_threads_info:
    Setting PTATLAS=ATLAS
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries ptf77blas,ptcblas,atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries ptf77blas,ptcblas,atlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries ptf77blas,ptcblas,atlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_threads_info'>
      NOT AVAILABLE
  
    atlas_info:
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries f77blas,cblas,atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries f77blas,cblas,atlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries f77blas,cblas,atlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_info'>
      NOT AVAILABLE
  
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
    /Users/username/.pyenv/versions/3.9.7/lib/python3.9/distutils/dist.py:274: UserWarning: Unknown distribution option: 'define_macros'
      warnings.warn(msg)
    running bdist_wheel
    running build
    running config_cc
    unifing config_cc, config, build_clib, build_ext, build commands --compiler options
    running config_fc
    unifing config_fc, config, build_clib, build_ext, build commands --fcompiler options
    running build_src
    build_src
    building py_modules sources
    building library "npymath" sources
    Could not locate executable gfortran
    Could not locate executable f95
    Could not locate executable f90
    Could not locate executable f77
    Could not locate executable xlf90
    Could not locate executable xlf
    Could not locate executable ifort
    Could not locate executable ifc
    Could not locate executable g77
    Could not locate executable g95
    Could not locate executable pgfortran
    don't know how to compile Fortran code on platform 'posix'
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath' to include_dirs.
    None - nothing done with h_files = ['build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/npy_math_internal.h']
    building library "npysort" sources
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common' to include_dirs.
    None - nothing done with h_files = ['build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_sort.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_partition.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_binsearch.h']
    building library "npyrandom" sources
    building extension "numpy.core._multiarray_tests" sources
    building extension "numpy.core._multiarray_umath" sources
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath' to include_dirs.
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath' to include_dirs.
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common' to include_dirs.
    numpy.core - nothing done with h_files = ['build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/funcs.inc', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/simd.inc', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/loops.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/matmul.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/clip.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/npy_math_internal.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/templ_common.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy/config.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy/_numpyconfig.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy/__multiarray_api.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy/__ufunc_api.h']
    building extension "numpy.core._umath_tests" sources
    building extension "numpy.core._rational_tests" sources
    building extension "numpy.core._struct_ufunc_tests" sources
    building extension "numpy.core._operand_flag_tests" sources
    building extension "numpy.fft._pocketfft_internal" sources
    building extension "numpy.linalg.lapack_lite" sources
    building extension "numpy.linalg._umath_linalg" sources
    building extension "numpy.random._mt19937" sources
    building extension "numpy.random._philox" sources
    building extension "numpy.random._pcg64" sources
    building extension "numpy.random._sfc64" sources
    building extension "numpy.random._common" sources
    building extension "numpy.random.bit_generator" sources
    building extension "numpy.random._generator" sources
    building extension "numpy.random._bounded_integers" sources
    building extension "numpy.random.mtrand" sources
    building data_files sources
    build_src: building npy-pkg config files
    running build_py
    creating build/lib.macosx-11.4-arm64-3.9
    creating build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/conftest.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/version.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/_globals.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/dual.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/_distributor_init.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/ctypeslib.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/matlib.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying numpy/_pytesttester.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    copying build/src.macosx-11.4-arm64-3.9/numpy/__config__.py -> build/lib.macosx-11.4-arm64-3.9/numpy
    creating build/lib.macosx-11.4-arm64-3.9/numpy/compat
    copying numpy/compat/py3k.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat
    copying numpy/compat/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat
    copying numpy/compat/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat
    copying numpy/compat/_inspect.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat
    creating build/lib.macosx-11.4-arm64-3.9/numpy/compat/tests
    copying numpy/compat/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat/tests
    copying numpy/compat/tests/test_compat.py -> build/lib.macosx-11.4-arm64-3.9/numpy/compat/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/umath.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/fromnumeric.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_dtype.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_add_newdocs.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_methods.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_internal.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_string_helpers.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/multiarray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_asarray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/records.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/setup_common.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/memmap.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/overrides.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/getlimits.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_dtype_ctypes.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/defchararray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/shape_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/machar.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/numeric.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/function_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/einsumfunc.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/umath_tests.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_ufunc_config.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_exceptions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/numerictypes.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/_type_aliases.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/cversions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/arrayprint.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    copying numpy/core/code_generators/generate_numpy_api.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core
    creating build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_numerictypes.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalar_methods.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalarmath.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_item_selection.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_machar.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_unicode.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_arrayprint.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalarbuffer.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_indexerrors.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_print.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_half.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_mem_overlap.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_shape_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_deprecations.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_errstate.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_records.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalarinherit.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_indexing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_umath.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_numeric.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_function_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_datetime.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test__exceptions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_extint128.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_umath_complex.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/_locales.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_defchararray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_conversion_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalarprint.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_abc.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_ufunc.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_dtype.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_umath_accuracy.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_getlimits.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_einsum.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_api.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_longdouble.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_overrides.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_scalar_ctors.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_multiarray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_memmap.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_nditer.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_cpu_features.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_protocols.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    copying numpy/core/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/core/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/unixccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/numpy_distribution.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/conv_template.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/cpuinfo.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/ccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/msvc9compiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/npy_pkg_config.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/misc_util.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/log.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/line_endings.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/lib2def.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/pathccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/system_info.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/core.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/exec_command.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/from_template.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/mingw32ccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/extension.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/msvccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/intelccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying numpy/distutils/_shell_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    copying build/src.macosx-11.4-arm64-3.9/numpy/distutils/__config__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils
    creating build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/config_compiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_ext.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/config.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/install_headers.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_py.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_src.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/sdist.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_scripts.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/bdist_rpm.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/install_clib.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/build_clib.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/autodist.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/egg_info.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/install.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/develop.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    copying numpy/distutils/command/install_data.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/command
    creating build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/gnu.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/compaq.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/intel.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/none.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/nag.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/pg.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/ibm.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/sun.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/nv.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/lahey.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/g95.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/mips.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/hpux.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/environment.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/pathf95.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/absoft.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    copying numpy/distutils/fcompiler/vast.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/fcompiler
    creating build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_system_info.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_mingw32ccompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_from_template.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_fcompiler_intel.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_misc_util.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_fcompiler.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_shell_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_exec_command.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_npy_pkg_config.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_fcompiler_nagfor.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    copying numpy/distutils/tests/test_fcompiler_gnu.py -> build/lib.macosx-11.4-arm64-3.9/numpy/distutils/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/misc.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/internals.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/creation.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/dispatch.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/constants.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/ufuncs.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/broadcasting.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/basics.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/subclassing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/indexing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/byteswapping.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/structured_arrays.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    copying numpy/doc/glossary.py -> build/lib.macosx-11.4-arm64-3.9/numpy/doc
    creating build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/cfuncs.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/common_rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/crackfortran.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/cb_rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/f2py2e.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/func2subr.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/__version__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/diagnose.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/capi_maps.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/f90mod_rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/f2py_testing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/use_rules.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/auxfuncs.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    copying numpy/f2py/__main__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py
    creating build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_mixed.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_logical.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_assumed_shape.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_common.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_kind.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_array_from_pyobj.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_real.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/util.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_size.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_callback.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_string.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_quoted_character.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_parameter.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_semicolon_split.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_compile_function.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_block_docstring.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_integer.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_character.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_return_complex.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_crackfortran.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    copying numpy/f2py/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/f2py/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/fft
    copying numpy/fft/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft
    copying numpy/fft/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft
    copying numpy/fft/helper.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft
    copying numpy/fft/_pocketfft.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft
    creating build/lib.macosx-11.4-arm64-3.9/numpy/fft/tests
    copying numpy/fft/tests/test_pocketfft.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft/tests
    copying numpy/fft/tests/test_helper.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft/tests
    copying numpy/fft/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/fft/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/_iotools.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/mixins.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/nanfunctions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/recfunctions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/histograms.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/scimath.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/_version.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/user_array.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/format.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/twodim_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/financial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/index_tricks.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/npyio.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/shape_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/stride_tricks.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/arrayterator.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/function_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/arraysetops.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/arraypad.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/type_check.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/polynomial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/_datasource.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    copying numpy/lib/ufunclike.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib
    creating build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_type_check.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_twodim_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_polynomial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test__iotools.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_shape_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_ufunclike.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_index_tricks.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_arrayterator.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test__version.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_io.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_arraysetops.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_function_base.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_arraypad.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_mixins.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_packbits.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test__datasource.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_stride_tricks.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_financial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_recfunctions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_nanfunctions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_format.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_histograms.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    copying numpy/lib/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/lib/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/linalg
    copying numpy/linalg/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg
    copying numpy/linalg/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg
    copying numpy/linalg/linalg.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg
    creating build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/test_linalg.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/test_deprecations.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/test_build.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    copying numpy/linalg/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/linalg/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/extras.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/testutils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/core.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/bench.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/timer_comparison.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    copying numpy/ma/mrecords.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma
    creating build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_old_ma.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_core.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_deprecations.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_subclassing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_extras.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_mrecords.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    copying numpy/ma/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/ma/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib
    copying numpy/matrixlib/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib
    copying numpy/matrixlib/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib
    copying numpy/matrixlib/defmatrix.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib
    creating build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_matrix_linalg.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_defmatrix.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_interaction.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_numeric.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_masked_matrix.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_multiarray.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    copying numpy/matrixlib/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/matrixlib/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/laguerre.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/_polybase.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/polyutils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/hermite_e.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/chebyshev.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/polynomial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/legendre.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    copying numpy/polynomial/hermite.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial
    creating build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_chebyshev.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_hermite_e.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_polynomial.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_laguerre.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_legendre.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_printing.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_hermite.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_classes.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    copying numpy/polynomial/tests/test_polyutils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/polynomial/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/random
    copying numpy/random/_pickle.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random
    copying numpy/random/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random
    copying numpy/random/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random
    creating build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_generator_mt19937.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_randomstate.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_direct.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_extending.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_smoke.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_randomstate_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_seed_sequence.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_generator_mt19937_regressions.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_random.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    copying numpy/random/tests/test_regression.py -> build/lib.macosx-11.4-arm64-3.9/numpy/random/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/testing
    copying numpy/testing/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing
    copying numpy/testing/setup.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing
    copying numpy/testing/utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing
    copying numpy/testing/print_coercion_tables.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing
    creating build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/nosetester.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/noseclasses.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/parameterized.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    copying numpy/testing/_private/decorators.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/_private
    creating build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    copying numpy/testing/tests/test_utils.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    copying numpy/testing/tests/test_decorators.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    copying numpy/testing/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    copying numpy/testing/tests/test_doctesting.py -> build/lib.macosx-11.4-arm64-3.9/numpy/testing/tests
    creating build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_warnings.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_matlib.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_ctypeslib.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_numpy_version.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/__init__.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_reloading.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_public_api.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    copying numpy/tests/test_scripts.py -> build/lib.macosx-11.4-arm64-3.9/numpy/tests
    running build_clib
    customize UnixCCompiler
    customize UnixCCompiler using new_build_clib
    building 'npymath' library
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9
    creating build/temp.macosx-11.4-arm64-3.9/numpy
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src/npymath
    creating build/temp.macosx-11.4-arm64-3.9/build
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath
    compile options: '-Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    clang: numpy/core/src/npymath/npy_math.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/ieee754.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/npy_math_complex.c
    clang: numpy/core/src/npymath/halffloat.c
    In file included from numpy/core/src/npymath/npy_math.c:9:
    numpy/core/src/npymath/npy_math_internal.h.src:490:21: warning: incompatible pointer types passing 'npy_longdouble *' (aka 'double *') to parameter of type 'long double *' [-Wincompatible-pointer-types]
        return modfl(x, iptr);
                        ^~~~
    /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/math.h:394:52: note: passing argument to parameter here
    extern long double modfl(long double, long double *);
                                                       ^
    1 warning generated.
    ar: adding 4 object files to build/temp.macosx-11.4-arm64-3.9/libnpymath.a
    ranlib:@ build/temp.macosx-11.4-arm64-3.9/libnpymath.a
    building 'npysort' library
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort
    compile options: '-Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/quicksort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/mergesort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/timsort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/heapsort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/radixsort.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/selection.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npysort/binsearch.c
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    numpy/core/src/npysort/selection.c.src:328:9: warning: code will never be executed [-Wunreachable-code]
            npy_intp k;
            ^~~~~~~~~~~
    numpy/core/src/npysort/selection.c.src:326:14: note: silence by adding parentheses to mark code as explicitly dead
        else if (0 && kth == num - 1) {
                 ^
                 /* DISABLES CODE */ ( )
    22 warnings generated.
    ar: adding 7 object files to build/temp.macosx-11.4-arm64-3.9/libnpysort.a
    ranlib:@ build/temp.macosx-11.4-arm64-3.9/libnpysort.a
    building 'npyrandom' library
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9/numpy/random
    creating build/temp.macosx-11.4-arm64-3.9/numpy/random/src
    creating build/temp.macosx-11.4-arm64-3.9/numpy/random/src/distributions
    compile options: '-Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    clang: numpy/random/src/distributions/distributions.c
    clang: numpy/random/src/distributions/logfactorial.c
    clang: numpy/random/src/distributions/random_hypergeometric.c
    clang: numpy/random/src/distributions/random_mvhg_count.c
    clang: numpy/random/src/distributions/random_mvhg_marginals.c
    ar: adding 5 object files to build/temp.macosx-11.4-arm64-3.9/libnpyrandom.a
    ranlib:@ build/temp.macosx-11.4-arm64-3.9/libnpyrandom.a
    running build_ext
    customize UnixCCompiler
    customize UnixCCompiler using new_build_ext
    building 'numpy.core._multiarray_tests' extension
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src/common
    compile options: '-DNPY_INTERNAL_BUILD=1 -DHAVE_NPY_CONFIG_H=1 -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE=1 -D_LARGEFILE64_SOURCE=1 -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/_multiarray_tests.c
    clang: numpy/core/src/common/mem_overlap.c
    In file included from numpy/core/src/multiarray/_multiarray_tests.c.src:7:
    In file included from numpy/core/include/numpy/npy_math.h:596:
    numpy/core/src/npymath/npy_math_internal.h.src:490:21: warning: incompatible pointer types passing 'npy_longdouble *' (aka 'double *') to parameter of type 'long double *' [-Wincompatible-pointer-types]
        return modfl(x, iptr);
                        ^~~~
    /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/math.h:394:52: note: passing argument to parameter here
    extern long double modfl(long double, long double *);
                                                       ^
    numpy/core/src/multiarray/_multiarray_tests.c.src:1895:61: warning: format specifies type 'long double' but the argument has type 'npy_longdouble' (aka 'double') [-Wformat]
            PyOS_snprintf(str, sizeof(str), "%.*Lg", precision, x);
                                             ~~~~~              ^
                                             %.*g
    2 warnings generated.
    clang -bundle -undefined dynamic_lookup -L/opt/homebrew/opt/readline/lib -L/opt/homebrew/opt/readline/lib -L/Users/username/.pyenv/versions/3.9.7/lib -L/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/lib -L/opt/homebrew/opt/readline/lib -L/opt/homebrew/opt/readline/lib -L/Users/username/.pyenv/versions/3.9.7/lib -L/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/lib build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/_multiarray_tests.o build/temp.macosx-11.4-arm64-3.9/numpy/core/src/common/mem_overlap.o -Lbuild/temp.macosx-11.4-arm64-3.9 -lnpymath -o build/lib.macosx-11.4-arm64-3.9/numpy/core/_multiarray_tests.cpython-39-darwin.so
    building 'numpy.core._multiarray_umath' extension
    compiling C sources
    C compiler: clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include
  
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src/multiarray
    creating build/temp.macosx-11.4-arm64-3.9/numpy/core/src/umath
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath
    creating build/temp.macosx-11.4-arm64-3.9/build/src.macosx-11.4-arm64-3.9/numpy/core/src/common
    creating build/temp.macosx-11.4-arm64-3.9/private
    creating build/temp.macosx-11.4-arm64-3.9/private/var
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c8ym9nev
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c8ym9nev/numpy_19e07fc3bb404ccdbeee000fa1acbc92
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c8ym9nev/numpy_19e07fc3bb404ccdbeee000fa1acbc92/numpy
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c8ym9nev/numpy_19e07fc3bb404ccdbeee000fa1acbc92/numpy/_build_utils
    creating build/temp.macosx-11.4-arm64-3.9/private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c8ym9nev/numpy_19e07fc3bb404ccdbeee000fa1acbc92/numpy/_build_utils/src
    compile options: '-DNPY_INTERNAL_BUILD=1 -DHAVE_NPY_CONFIG_H=1 -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE=1 -D_LARGEFILE64_SOURCE=1 -DNO_ATLAS_INFO=3 -DHAVE_CBLAS -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/umath -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c'
    extra options: '-faltivec -I/System/Library/Frameworks/vecLib.framework/Headers'
    clang: numpy/core/src/multiarray/array_assign_scalar.c
    clang: numpy/core/src/multiarray/alloc.c
    clang: numpy/core/src/multiarray/buffer.c
    clang: numpy/core/src/multiarray/common.c
    clang: numpy/core/src/multiarray/conversion_utils.c
    clang: numpy/core/src/multiarray/datetime_strings.c
    clang: numpy/core/src/multiarray/descriptor.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/einsum.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/multiarray/hashdescr.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/lowlevel_strided_loops.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/multiarray/multiarraymodule.c
    clang: numpy/core/src/multiarray/nditer_constr.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/multiarray/refcount.c
    clang: numpy/core/src/multiarray/temp_elide.c
    clang: numpy/core/src/multiarray/scalarapi.c
    clang: numpy/core/src/multiarray/vdot.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath/loops.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/umath/ufunc_object.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/umath/ufunc_type_resolution.c
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/ieee754.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/common/array_assign.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: numpy/core/src/common/ucsnarrow.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_cpu_features.c
    clang: /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-c8ym9nev/numpy_19e07fc3bb404ccdbeee000fa1acbc92/numpy/_build_utils/src/apple_sgemv_fix.c
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    clang: error: clang: error: the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    the clang compiler does not support 'faltivec', please use -maltivec and include altivec.h explicitly
    error: Command "clang -Wno-unused-result -Wsign-compare -Wunreachable-code -DNDEBUG -g -fwrapv -O3 -Wall -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -I/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include -DNPY_INTERNAL_BUILD=1 -DHAVE_NPY_CONFIG_H=1 -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE=1 -D_LARGEFILE64_SOURCE=1 -DNO_ATLAS_INFO=3 -DHAVE_CBLAS -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/umath -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Inumpy/core/include -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/include/numpy -Inumpy/core/src/common -Inumpy/core/src -Inumpy/core -Inumpy/core/src/npymath -Inumpy/core/src/multiarray -Inumpy/core/src/umath -Inumpy/core/src/npysort -I/Users/username/.pyenv/versions/3.9.7/include/python3.9 -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/common -Ibuild/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath -c numpy/core/src/multiarray/alloc.c -o build/temp.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/alloc.o -MMD -MF build/temp.macosx-11.4-arm64-3.9/numpy/core/src/multiarray/alloc.o.d -faltivec -I/System/Library/Frameworks/vecLib.framework/Headers" failed with exit status 1
    ----------------------------------------
    ERROR: Failed building wheel for numpy
  Failed to build numpy
  ERROR: Could not build wheels for numpy which use PEP 517 and cannot be installed directly
  WARNING: You are using pip version 21.2.3; however, version 21.3.1 is available.
  You should consider upgrading via the '/Users/username/.pyenv/versions/3.9.7/bin/python3.9 -m pip install --upgrade pip' command.
  ----------------------------------------
WARNING: Discarding https://files.pythonhosted.org/packages/bb/bb/944f559d554df6c9adf037aa9fc982a9706ee0e96c0d5beac701cb158900/scipy-1.7.0.tar.gz#sha256=998c5e6ea649489302de2c0bc026ed34284f531df89d2bdc8df3a0d44d165739 (from https://pypi.org/simple/scipy/) (requires-python:>=3.7,<3.10). Command errored out with exit status 1: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-standalone-pip-rk2v957g/__env_pip__.zip/pip install --ignore-installed --no-user --prefix /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-build-env-xhjoou88/overlay --no-warn-script-location --no-binary :none: --only-binary :none: -i https://pypi.org/simple -- 'wheel<0.37.0' 'setuptools<58.0.0' 'Cython>=0.29.18,<3.0' 'pybind11>=2.4.3,<2.7.0' pythran==0.9.11 'numpy==1.19.2; python_version=='"'"'3.7'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.19.2; python_version=='"'"'3.8'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.16.5; python_version=='"'"'3.7'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.17.3; python_version=='"'"'3.8'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.19.3; python_version=='"'"'3.9'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.20.0; python_version=='"'"'3.7'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version=='"'"'3.8'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version=='"'"'3.9'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' Check the logs for full command output.
  Using cached scipy-1.6.3.tar.gz (27.2 MB)
  Installing build dependencies ... error
  ERROR: Command errored out with exit status 1:
   command: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-standalone-pip-nt81b8wc/__env_pip__.zip/pip install --ignore-installed --no-user --prefix /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-build-env-3u67kur2/overlay --no-warn-script-location --no-binary :none: --only-binary :none: -i https://pypi.org/simple -- 'wheel<0.37.0' 'setuptools<=51.0.0' 'Cython>=0.29.18,<3.0' 'pybind11>=2.4.3,<2.7.0' 'numpy==1.19.2; python_version=='"'"'3.6'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.19.2; python_version=='"'"'3.7'"'"' and platform_machine=='"'"'aarch64'"'"'' 'numpy==1.16.5; python_version=='"'"'3.6'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.16.5; python_version=='"'"'3.7'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.17.3; python_version=='"'"'3.8'"'"' and platform_machine!='"'"'aarch64'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.19.3; python_version=='"'"'3.9'"'"' and platform_python_implementation != '"'"'PyPy'"'"'' 'numpy==1.19.0; python_version=='"'"'3.6'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy==1.20.0; python_version=='"'"'3.7'"'"' and platform_python_implementation=='"'"'PyPy'"'"'' 'numpy; python_version>='"'"'3.10'"'"'' 'numpy; python_version>='"'"'3.8'"'"' and platform_python_implementation=='"'"'PyPy'"'"''
       cwd: None
  Complete output (931 lines):
  Ignoring numpy: markers 'python_version == "3.6" and platform_machine == "aarch64"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.7" and platform_machine == "aarch64"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.6" and platform_machine != "aarch64" and platform_python_implementation != "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.7" and platform_machine != "aarch64" and platform_python_implementation != "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.8" and platform_machine != "aarch64" and platform_python_implementation != "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.6" and platform_python_implementation == "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version == "3.7" and platform_python_implementation == "PyPy"' don't match your environment
  Ignoring numpy: markers 'python_version >= "3.10"' don't match your environment
  Ignoring numpy: markers 'python_version >= "3.8" and platform_python_implementation == "PyPy"' don't match your environment
  Collecting wheel<0.37.0
    Using cached wheel-0.36.2-py2.py3-none-any.whl (35 kB)
  Collecting setuptools<=51.0.0
    Using cached setuptools-51.0.0-py3-none-any.whl (785 kB)
  Collecting Cython<3.0,>=0.29.18
    Using cached Cython-0.29.26-py2.py3-none-any.whl (983 kB)
  Collecting pybind11<2.7.0,>=2.4.3
    Using cached pybind11-2.6.2-py2.py3-none-any.whl (191 kB)
  Collecting numpy==1.19.3
    Using cached numpy-1.19.3.zip (7.3 MB)
    Installing build dependencies: started
    Installing build dependencies: finished with status 'done'
    Getting requirements to build wheel: started
    Getting requirements to build wheel: finished with status 'done'
      Preparing wheel metadata: started
      Preparing wheel metadata: finished with status 'done'
  Building wheels for collected packages: numpy
    Building wheel for numpy (PEP 517): started
    Building wheel for numpy (PEP 517): finished with status 'error'
    ERROR: Command errored out with exit status 1:
     command: /Users/username/.pyenv/versions/3.9.7/bin/python3.9 /var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/tmpqmmmpley_in_process.py build_wheel /var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/tmpzyvir1fh
         cwd: /private/var/folders/c4/ch89cvbs21d4cvs_ch64f2rh0000gn/T/pip-install-fv40djwl/numpy_064d5fb711cc498f81764b40f75306b5
    Complete output (893 lines):
    Running from numpy source directory.
    numpy/random/_bounded_integers.pxd.in has not changed
    numpy/random/_philox.pyx has not changed
    numpy/random/_bounded_integers.pyx.in has not changed
    numpy/random/_sfc64.pyx has not changed
    numpy/random/_mt19937.pyx has not changed
    numpy/random/bit_generator.pyx has not changed
    Processing numpy/random/_bounded_integers.pyx
    numpy/random/mtrand.pyx has not changed
    numpy/random/_generator.pyx has not changed
    numpy/random/_pcg64.pyx has not changed
    numpy/random/_common.pyx has not changed
    Cythonizing sources
    blas_opt_info:
    blas_mkl_info:
    customize UnixCCompiler
      libraries mkl_rt not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    blis_info:
      libraries blis not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_info:
      libraries openblas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_blas_threads_info:
    Setting PTATLAS=ATLAS
      libraries tatlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_blas_info:
      libraries satlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_blas_threads_info:
    Setting PTATLAS=ATLAS
      libraries ptf77blas,ptcblas,atlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_blas_info:
      libraries f77blas,cblas,atlas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    accelerate_info:
      libraries accelerate not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
    Library accelerate was not found. Ignoring
      libraries veclib not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
    Library veclib was not found. Ignoring
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
    non-existing path in 'numpy/distutils': 'site.cfg'
    lapack_opt_info:
    lapack_mkl_info:
      libraries mkl_rt not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_lapack_info:
      libraries openblas not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    openblas_clapack_info:
      libraries openblas,lapack not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    flame_info:
      libraries flame not found in ['/Users/username/.pyenv/versions/3.9.7/lib', '/usr/local/lib', '/usr/lib']
      NOT AVAILABLE
  
    atlas_3_10_threads_info:
    Setting PTATLAS=ATLAS
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries tatlas,tatlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries tatlas,tatlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries tatlas,tatlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_3_10_threads_info'>
      NOT AVAILABLE
  
    atlas_3_10_info:
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries satlas,satlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries satlas,satlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries satlas,satlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_3_10_info'>
      NOT AVAILABLE
  
    atlas_threads_info:
    Setting PTATLAS=ATLAS
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries ptf77blas,ptcblas,atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries ptf77blas,ptcblas,atlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries ptf77blas,ptcblas,atlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_threads_info'>
      NOT AVAILABLE
  
    atlas_info:
      libraries lapack_atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries f77blas,cblas,atlas not found in /Users/username/.pyenv/versions/3.9.7/lib
      libraries lapack_atlas not found in /usr/local/lib
      libraries f77blas,cblas,atlas not found in /usr/local/lib
      libraries lapack_atlas not found in /usr/lib
      libraries f77blas,cblas,atlas not found in /usr/lib
    <class 'numpy.distutils.system_info.atlas_info'>
      NOT AVAILABLE
  
      FOUND:
        extra_compile_args = ['-faltivec', '-I/System/Library/Frameworks/vecLib.framework/Headers']
        extra_link_args = ['-Wl,-framework', '-Wl,Accelerate']
        define_macros = [('NO_ATLAS_INFO', 3), ('HAVE_CBLAS', None)]
  
    /Users/username/.pyenv/versions/3.9.7/lib/python3.9/distutils/dist.py:274: UserWarning: Unknown distribution option: 'define_macros'
      warnings.warn(msg)
    running bdist_wheel
    running build
    running config_cc
    unifing config_cc, config, build_clib, build_ext, build commands --compiler options
    running config_fc
    unifing config_fc, config, build_clib, build_ext, build commands --fcompiler options
    running build_src
    build_src
    building py_modules sources
    building library "npymath" sources
    Could not locate executable gfortran
    Could not locate executable f95
    Could not locate executable f90
    Could not locate executable f77
    Could not locate executable xlf90
    Could not locate executable xlf
    Could not locate executable ifort
    Could not locate executable ifc
    Could not locate executable g77
    Could not locate executable g95
    Could not locate executable pgfortran
    don't know how to compile Fortran code on platform 'posix'
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath' to include_dirs.
    None - nothing done with h_files = ['build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath/npy_math_internal.h']
    building library "npysort" sources
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common' to include_dirs.
    None - nothing done with h_files = ['build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_sort.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_partition.h', 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common/npy_binsearch.h']
    building library "npyrandom" sources
    building extension "numpy.core._multiarray_tests" sources
    building extension "numpy.core._multiarray_umath" sources
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/umath' to include_dirs.
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/npymath' to include_dirs.
      adding 'build/src.macosx-11.4-arm64-3.9/numpy/core/src/common' to include_dirs.
```