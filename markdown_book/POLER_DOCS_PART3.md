# POLER Engine Documentation (Часть 3)

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pox-0.3.7.dist-info/top_level.txt`

```markdown
pox

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/sortedcontainers-2.4.0.dist-info/top_level.txt`

```markdown
sortedcontainers

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/crc32c-2.9.post0.dist-info/entry_points.txt`

```markdown
[console_scripts]
crc32c = crc32c:main

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/crc32c-2.9.post0.dist-info/top_level.txt`

```markdown
crc32c

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/tenacity-9.1.4.dist-info/top_level.txt`

```markdown
tenacity

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/diskcache-5.6.3.dist-info/top_level.txt`

```markdown
diskcache

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/skeletor-1.7.1.dist-info/top_level.txt`

```markdown
skeletor

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pybind11/include/pybind11/conduit/README.txt`

```markdown
NOTE
----

The C++ code here

** only depends on <Python.h> **

and nothing else.

DO NOT ADD CODE WITH OTHER EXTERNAL DEPENDENCIES TO THIS DIRECTORY.

Read on:

pybind11_conduit_v1.h — Type-safe interoperability between different
                        independent Python/C++ bindings systems.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/idna-3.19.dist-info/entry_points.txt`

```markdown
[console_scripts]
idna=idna.cli:main


```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/idna-3.19.dist-info/licenses/LICENSE.md`

```markdown
BSD 3-Clause License

Copyright (c) 2013-2026, Kim Davies and contributors.
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are
met:

1. Redistributions of source code must retain the above copyright
   notice, this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright
   notice, this list of conditions and the following disclaimer in the
   documentation and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its
   contributors may be used to endorse or promote products derived from
   this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
"AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED
TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/IPython/testing/plugin/test_example.txt`

```markdown
=====================================
 Tests in example form - pure python
=====================================

This file contains doctest examples embedded as code blocks, using normal
Python prompts.  See the accompanying file for similar examples using IPython
prompts (you can't mix both types within one file). The following will be run
as a test::

    >>> 1+1
    2
    >>> print ("hello")
    hello

More than one example works::

    >>> s="Hello World"

    >>> s.upper()
    'HELLO WORLD'

but you should note that the *entire* test file is considered to be a single
test.  Individual code blocks that fail are printed separately as ``example
failures``, but the whole file is still counted and reported as one test.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/IPython/testing/plugin/test_combo.txt`

```markdown
=======================
 Combo testing example
=======================

This is a simple example that mixes ipython doctests::

    In [1]: import code

    In [2]: 2**12
    Out[2]: 4096

with command-line example information that does *not* get executed::

    $ mpirun -n 4 ipengine --controller-port=10000 --controller-ip=host0

and with literal examples of Python source code::

    controller = dict(host='myhost',
		      engine_port=None, # default is 10105
		      control_port=None,
		      )

    # keys are hostnames, values are the number of engine on that host
    engines = dict(node1=2,
		   node2=2,
		   node3=2,
		   node3=2,
		   )

    # Force failure to detect that this test is being run.
    1/0

These source code examples are executed but no output is compared at all.  An
error or failure is reported only if an exception is raised.

NOTE: the execution of pure python blocks is not yet working!

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/IPython/testing/plugin/test_exampleip.txt`

```markdown
=================================
 Tests in example form - IPython
=================================

You can write text files with examples that use IPython prompts (as long as you
use the nose ipython doctest plugin), but you can not mix and match prompt
styles in a single file.  That is, you either use all ``>>>`` prompts or all
IPython-style prompts.  Your test suite *can* have both types, you just need to
put each type of example in a separate. Using IPython prompts, you can paste
directly from your session::

    In [5]: s="Hello World"

    In [6]: s.upper()
    Out[6]: 'HELLO WORLD'

Another example::

    In [8]: 1+3
    Out[8]: 4

Just like in IPython docstrings, you can use all IPython syntax and features::

    In [9]: !echo hello
    hello

    In [10]: a='hi'

    In [11]: !echo $a
    hi

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/readchar-4.2.2.dist-info/top_level.txt`

```markdown
readchar

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pyarrow/tests/data/orc/README.md`

```markdown
<!---
  Licensed to the Apache Software Foundation (ASF) under one
  or more contributor license agreements.  See the NOTICE file
  distributed with this work for additional information
  regarding copyright ownership.  The ASF licenses this file
  to you under the Apache License, Version 2.0 (the
  "License"); you may not use this file except in compliance
  with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing,
  software distributed under the License is distributed on an
  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  KIND, either express or implied.  See the License for the
  specific language governing permissions and limitations
  under the License.
-->

The ORC and JSON files come from the `examples` directory in the Apache ORC
source tree:
https://github.com/apache/orc/tree/main/examples

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pyarrow/src/arrow/python/CMakeLists.txt`

```markdown
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

arrow_install_all_headers("arrow/python")
add_subdirectory(vendored)

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pyarrow/src/arrow/python/vendored/CMakeLists.txt`

```markdown
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

arrow_install_all_headers("arrow/python/vendored")

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/rsa-4.9.1.dist-info/entry_points.txt`

```markdown
[console_scripts]
pyrsa-decrypt=rsa.cli:decrypt
pyrsa-encrypt=rsa.cli:encrypt
pyrsa-keygen=rsa.cli:keygen
pyrsa-priv2pub=rsa.util:private_to_public
pyrsa-sign=rsa.cli:sign
pyrsa-verify=rsa.cli:verify


```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pyperclip-1.11.0.dist-info/top_level.txt`

```markdown
pyperclip

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pyperclip-1.11.0.dist-info/licenses/AUTHORS.txt`

```markdown
Here is an inevitably incomplete list of MUCH-APPRECIATED CONTRIBUTORS --
people who have submitted patches, reported bugs, added translations, helped
answer newbie questions, and generally made Pyperclip that much better:

Al Sweigart
Alexander Cobleigh @cblgh
Andrea Scarpino https://github.com/ilpianista
Aniket Pandey https://github.com/lordaniket06
Anton Yakutovich https://github.com/drakulavich
Brian Levin https://github.com/bnice5000
Carvell Scott https://github.com/CarvellScott
Cees Timmerman https://github.com/CTimmerman
Chris Clark
Christopher Lambert https://github.com/XN137
Chris Woerz https://github.com/erendrake
Corey Bryant https://github.com/coreycb
Daniel Shimon https://github.com/daniel-shimon
Edd Barrett https://github.com/vext01
Eugene Yang https://github.com/eugene-yang
Felix Yan https://github.com/felixonmars
Fredrik Borg https://github.com/frbor
fthoma https://github.com/fthoma
Greg Witt https://github.com/GoodGuyGregory
hinlader https://github.com/hinlader
Hugo van Kemenade https://github.com/hugovk
Hynek Cernoch https://github.com/hynekcer
Jason R. Coombs https://github.com/jaraco
Jon Crall https://github.com/Erotemic
Jonathan Slenders https://github.com/jonathanslenders
JustAShoeMaker https://github.com/JustAShoeMaker
Marcelo Glezer https://github.com/gato
masajxxx https://github.com/masajxxx
Maximilian Hils https://github.com/mhils
mgunyho https://github.com/mgunyho
Melwyn Francis Carlo https://github.com/melwyncarlo
Michał Górny https://github.com/mgorny
Nicola Guerrera https://github.com/nik012003
Nikolaos-Digenis Karagiannis https://github.com/Digenis
Nils Ohlmeier https://github.com/nils-ohlmeier
Orson Peters https://github.com/orlp
pgajdos https://github.com/pgajdos
PirateOfAndaman https://github.com/PirateOfAndaman
Six https://github.com/brbsix
Stefan Devai https://github.com/stefandevai
Stephen Finucane https://github.com/stephenfin
Stefan Scherfke https://github.com/sscherfke
Steve Elam
Tamir Bahar https://github.com/tmr232
Terrel Shumway https://github.com/lernisto
Tim Cuthbertson https://github.com/timbertson
Tim Gates https://github.com/timgates42
Todd Leonhardt https://github.com/tleonhardt
Troy Sankey https://github.com/pwnage101
utagawa kiki https://github.com/utgwkk
Vertliba V.V. https://github.com/vertliba
Vince West https://github.com/dvincentwest
ZEDGR https://github.com/ZEDGR

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pyperclip-1.11.0.dist-info/licenses/LICENSE.txt`

```markdown
Copyright (c) 2014, Al Sweigart
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of the {organization} nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/certifi-2026.7.22.dist-info/top_level.txt`

```markdown
certifi

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/cachetools-7.2.0.dist-info/top_level.txt`

```markdown
cachetools

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/rpds_py-2026.6.3.dist-info/sboms/rpds-py.cyclonedx.json`

```markdown
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.5",
  "version": 1,
  "serialNumber": "urn:uuid:db798bf9-1726-4cb1-82cb-2a13771d0822",
  "metadata": {
    "timestamp": "2026-06-30T07:08:42.115208144Z",
    "tools": [
      {
        "vendor": "CycloneDX",
        "name": "cargo-cyclonedx",
        "version": "0.5.9"
      }
    ],
    "component": {
      "type": "library",
      "bom-ref": "path+file:///home/runner/work/rpds/rpds#rpds-py@2026.6.3",
      "name": "rpds-py",
      "version": "2026.6.3",
      "scope": "required",
      "purl": "pkg:cargo/rpds-py@2026.6.3?download_url=file://.",
      "components": [
        {
          "type": "library",
          "bom-ref": "path+file:///home/runner/work/rpds/rpds#rpds-py@2026.6.3 bin-target-0",
          "name": "rpds",
          "version": "2026.6.3",
          "purl": "pkg:cargo/rpds-py@2026.6.3?download_url=file://.#src/lib.rs"
        }
      ]
    },
    "properties": [
      {
        "name": "cdx:rustc:sbom:target:all_targets",
        "value": "true"
      }
    ]
  },
  "components": [
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#archery@1.2.2",
      "author": "Diogo Sousa <diogogsousa@gmail.com>",
      "name": "archery",
      "version": "1.2.2",
      "description": "Abstract over the atomicity of reference-counting pointers",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "70e0a5f99dfebb87bb342d0f53bb92c81842e100bbb915223e38349580e5441d"
        }
      ],
      "licenses": [
        {
          "expression": "MIT"
        }
      ],
      "purl": "pkg:cargo/archery@1.2.2",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/archery"
        },
        {
          "type": "website",
          "url": "https://github.com/orium/archery"
        },
        {
          "type": "vcs",
          "url": "https://github.com/orium/archery"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#heck@0.5.0",
      "name": "heck",
      "version": "0.5.0",
      "description": "heck is a case conversion library.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "2304e00983f87ffb38b55b444b5e3b60a884b5d30c0fca7d82fe33449bbe55ea"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/heck@0.5.0",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/withoutboats/heck"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#libc@0.2.177",
      "author": "The Rust Project Developers",
      "name": "libc",
      "version": "0.2.177",
      "description": "Raw FFI bindings to platform libraries like libc.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "2874a2af47a2325c2001a6e6fad9b16a53b802102b528163885171cf92b15976"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/libc@0.2.177",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/rust-lang/libc"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#once_cell@1.21.3",
      "author": "Aleksey Kladov <aleksey.kladov@gmail.com>",
      "name": "once_cell",
      "version": "1.21.3",
      "description": "Single assignment cells and lazy values.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "42f5e15c9953c5e4ccceeb2e7382a716482c34515315f7b03532b8b4e8393d2d"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/once_cell@1.21.3",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/once_cell"
        },
        {
          "type": "vcs",
          "url": "https://github.com/matklad/once_cell"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.103",
      "author": "David Tolnay <dtolnay@gmail.com>, Alex Crichton <alex@alexcrichton.com>",
      "name": "proc-macro2",
      "version": "1.0.103",
      "description": "A substitute implementation of the compiler's `proc_macro` API to decouple token-based libraries from the procedural macro use case.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "5ee95bc4ef87b8d5ba32e8b7714ccc834865276eab0aed5c9958d00ec45f49e8"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/proc-macro2@1.0.103",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/proc-macro2"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/proc-macro2"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-build-config@0.29.0",
      "author": "PyO3 Project and Contributors <https://github.com/PyO3>",
      "name": "pyo3-build-config",
      "version": "0.29.0",
      "description": "Build configuration for the PyO3 ecosystem",
      "scope": "excluded",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "c5e2a7d2f0d013342f295c048ad19237add5154a55b1c5a254c0ec93d4109078"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/pyo3-build-config@0.29.0",
      "externalReferences": [
        {
          "type": "website",
          "url": "https://github.com/pyo3/pyo3"
        },
        {
          "type": "vcs",
          "url": "https://github.com/pyo3/pyo3"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-ffi@0.29.0",
      "author": "PyO3 Project and Contributors <https://github.com/PyO3>",
      "name": "pyo3-ffi",
      "version": "0.29.0",
      "description": "Python-API bindings for the PyO3 ecosystem",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "ca85c467da1bbc8d866eea5deff9cf29ea5f7785054a17da36e65bda9c05845b"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/pyo3-ffi@0.29.0",
      "externalReferences": [
        {
          "type": "website",
          "url": "https://github.com/pyo3/pyo3"
        },
        {
          "type": "other",
          "url": "python"
        },
        {
          "type": "vcs",
          "url": "https://github.com/pyo3/pyo3"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-macros-backend@0.29.0",
      "author": "PyO3 Project and Contributors <https://github.com/PyO3>",
      "name": "pyo3-macros-backend",
      "version": "0.29.0",
      "description": "Code generation for PyO3 package",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "4ca3a1557399783172dc5bf39cfca835157732532cba56b71d2292161e53b362"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/pyo3-macros-backend@0.29.0",
      "externalReferences": [
        {
          "type": "website",
          "url": "https://github.com/pyo3/pyo3"
        },
        {
          "type": "vcs",
          "url": "https://github.com/pyo3/pyo3"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-macros@0.29.0",
      "author": "PyO3 Project and Contributors <https://github.com/PyO3>",
      "name": "pyo3-macros",
      "version": "0.29.0",
      "description": "Proc macros for PyO3 package",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "9ac53762fd065daa3194dd09337a38bd793a188100fd1a9304c4ab312d901771"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/pyo3-macros@0.29.0",
      "externalReferences": [
        {
          "type": "website",
          "url": "https://github.com/pyo3/pyo3"
        },
        {
          "type": "vcs",
          "url": "https://github.com/pyo3/pyo3"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3@0.29.0",
      "author": "PyO3 Project and Contributors <https://github.com/PyO3>",
      "name": "pyo3",
      "version": "0.29.0",
      "description": "Bindings to Python interpreter",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "cd274650b21d4bfc26a0a47587962c1edb425f69287324355cd040c3ea66071c"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/pyo3@0.29.0",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/crate/pyo3/"
        },
        {
          "type": "website",
          "url": "https://github.com/pyo3/pyo3"
        },
        {
          "type": "other",
          "url": "pyo3-python"
        },
        {
          "type": "vcs",
          "url": "https://github.com/pyo3/pyo3"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.42",
      "author": "David Tolnay <dtolnay@gmail.com>",
      "name": "quote",
      "version": "1.0.42",
      "description": "Quasi-quoting macro quote!(...)",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "a338cc41d27e6cc6dce6cefc13a0729dfbb81c262b1f519331575dd80ef3067f"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/quote@1.0.42",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/quote/"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/quote"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#rpds@1.2.1",
      "author": "Diogo Sousa <diogogsousa@gmail.com>",
      "name": "rpds",
      "version": "1.2.1",
      "description": "Persistent data structures with structural sharing",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "e025feb26210bc196b908e72deb063b1b4000754304341cbc168a1e72c857ebc"
        }
      ],
      "licenses": [
        {
          "expression": "MIT"
        }
      ],
      "purl": "pkg:cargo/rpds@1.2.1",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/rpds"
        },
        {
          "type": "website",
          "url": "https://github.com/orium/rpds"
        },
        {
          "type": "vcs",
          "url": "https://github.com/orium/rpds"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#smallvec@1.15.1",
      "author": "The Servo Project Developers",
      "name": "smallvec",
      "version": "1.15.1",
      "description": "'Small vector' optimization: store up to a small number of items on the stack",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "67b1b7a3b5fe4f1376887184045fcf45c69e92af734b7aaddc05fb777b6fbd03"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/smallvec@1.15.1",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/smallvec/"
        },
        {
          "type": "vcs",
          "url": "https://github.com/servo/rust-smallvec"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#syn@2.0.111",
      "author": "David Tolnay <dtolnay@gmail.com>",
      "name": "syn",
      "version": "2.0.111",
      "description": "Parser for Rust source code",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "390cc9a294ab71bdb1aa2e99d13be9c753cd2d7bd6560c77118597410c4d2e87"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/syn@2.0.111",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/syn"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/syn"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#target-lexicon@0.13.3",
      "author": "Dan Gohman <sunfish@mozilla.com>",
      "name": "target-lexicon",
      "version": "0.13.3",
      "description": "LLVM target triple types",
      "scope": "excluded",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "df7f62577c25e07834649fc3b39fafdc597c0a3527dc1c60129201ccfcbaa50c"
        }
      ],
      "licenses": [
        {
          "expression": "Apache-2.0 WITH LLVM-exception"
        }
      ],
      "purl": "pkg:cargo/target-lexicon@0.13.3",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/target-lexicon/"
        },
        {
          "type": "vcs",
          "url": "https://github.com/bytecodealliance/target-lexicon"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#triomphe@0.1.15",
      "author": "Manish Goregaokar <manishsmail@gmail.com>, The Servo Project Developers",
      "name": "triomphe",
      "version": "0.1.15",
      "description": "A fork of std::sync::Arc with some extra functionality and without weak references (originally servo_arc)",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "dd69c5aa8f924c7519d6372789a74eac5b94fb0f8fcf0d4a97eb0bfc3e785f39"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/triomphe@0.1.15",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/Manishearth/triomphe"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#unicode-ident@1.0.22",
      "author": "David Tolnay <dtolnay@gmail.com>",
      "name": "unicode-ident",
      "version": "1.0.22",
      "description": "Determine whether characters have the XID_Start or XID_Continue properties according to Unicode Standard Annex #31",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "9312f7c4f6ff9069b165498234ce8be658059c6728633667c526e27dc2cf1df5"
        }
      ],
      "licenses": [
        {
          "expression": "(MIT OR Apache-2.0) AND Unicode-3.0"
        }
      ],
      "purl": "pkg:cargo/unicode-ident@1.0.22",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/unicode-ident"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/unicode-ident"
        }
      ]
    }
  ],
  "dependencies": [
    {
      "ref": "path+file:///home/runner/work/rpds/rpds#rpds-py@2026.6.3",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#archery@1.2.2",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3@0.29.0",
        "registry+https://github.com/rust-lang/crates.io-index#rpds@1.2.1"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#archery@1.2.2",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#triomphe@0.1.15"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#heck@0.5.0"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#libc@0.2.177"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#once_cell@1.21.3"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.103",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#unicode-ident@1.0.22"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-build-config@0.29.0",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#target-lexicon@0.13.3"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-ffi@0.29.0",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#libc@0.2.177",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3-build-config@0.29.0"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-macros-backend@0.29.0",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#heck@0.5.0",
        "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.103",
        "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.42",
        "registry+https://github.com/rust-lang/crates.io-index#syn@2.0.111"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-macros@0.29.0",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.103",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3-macros-backend@0.29.0",
        "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.42",
        "registry+https://github.com/rust-lang/crates.io-index#syn@2.0.111"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3@0.29.0",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#libc@0.2.177",
        "registry+https://github.com/rust-lang/crates.io-index#once_cell@1.21.3",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3-build-config@0.29.0",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3-ffi@0.29.0",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3-macros@0.29.0"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.42",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.103"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#rpds@1.2.1",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#archery@1.2.2",
        "registry+https://github.com/rust-lang/crates.io-index#smallvec@1.15.1"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#smallvec@1.15.1"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#syn@2.0.111",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.103",
        "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.42",
        "registry+https://github.com/rust-lang/crates.io-index#unicode-ident@1.0.22"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#target-lexicon@0.13.3"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#triomphe@0.1.15"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#unicode-ident@1.0.22"
    }
  ]
}
```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/google_cloud_core-2.7.0.dist-info/top_level.txt`

```markdown
google

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/fafbseg-3.2.2.dist-info/top_level.txt`

```markdown
fafbseg

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pint-0.26.1.dist-info/entry_points.txt`

```markdown
[console_scripts]
pint-convert = pint.pint_convert:main

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/ppft-1.7.8.dist-info/top_level.txt`

```markdown
pp
ppft

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/zstandard-0.25.0.dist-info/top_level.txt`

```markdown
zstandard

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/jedi-0.20.0.dist-info/AUTHORS.txt`

```markdown
Main Authors
------------

- David Halter (@davidhalter) <davidhalter88@gmail.com>
- Takafumi Arakaki (@tkf) <aka.tkf@gmail.com>

Code Contributors
-----------------

- Danilo Bargen (@dbrgn) <mail@dbrgn.ch>
- Laurens Van Houtven (@lvh) <_@lvh.cc>
- Aldo Stracquadanio (@Astrac) <aldo.strac@gmail.com>
- Jean-Louis Fuchs (@ganwell) <ganwell@fangorn.ch>
- tek (@tek)
- Yasha Borevich (@jjay) <j.borevich@gmail.com>
- Aaron Griffin <aaronmgriffin@gmail.com>
- andviro (@andviro)
- Mike Gilbert (@floppym) <floppym@gentoo.org>
- Aaron Meurer (@asmeurer) <asmeurer@gmail.com>
- Lubos Trilety <ltrilety@redhat.com>
- Akinori Hattori (@hattya) <hattya@gmail.com>
- srusskih (@srusskih)
- Steven Silvester (@blink1073)
- Colin Duquesnoy (@ColinDuquesnoy) <colin.duquesnoy@gmail.com>
- Jorgen Schaefer (@jorgenschaefer) <contact@jorgenschaefer.de>
- Fredrik Bergroth (@fbergroth)
- Mathias Fußenegger (@mfussenegger)
- Syohei Yoshida (@syohex) <syohex@gmail.com>
- ppalucky (@ppalucky)
- immerrr (@immerrr) immerrr@gmail.com
- Albertas Agejevas (@alga)
- Savor d'Isavano (@KenetJervet) <newelevenken@163.com>
- Phillip Berndt (@phillipberndt) <phillip.berndt@gmail.com>
- Ian Lee (@IanLee1521) <IanLee1521@gmail.com>
- Farkhad Khatamov (@hatamov) <comsgn@gmail.com>
- Kevin Kelley (@kelleyk) <kelleyk@kelleyk.net>
- Sid Shanker (@squidarth) <sid.p.shanker@gmail.com>
- Reinoud Elhorst (@reinhrst)
- Guido van Rossum (@gvanrossum) <guido@python.org>
- Dmytro Sadovnychyi (@sadovnychyi) <jedi@dmit.ro>
- Cristi Burcă (@scribu)
- bstaint (@bstaint)
- Mathias Rav (@Mortal) <rav@cs.au.dk>
- Daniel Fiterman (@dfit99) <fitermandaniel2@gmail.com>
- Simon Ruggier (@sruggier)
- Élie Gouzien (@ElieGouzien)
- Robin Roth (@robinro)
- Malte Plath (@langsamer)
- Anton Zub (@zabulazza)
- Maksim Novikov (@m-novikov) <mnovikov.work@gmail.com>
- Tobias Rzepka (@TobiasRzepka)
- micbou (@micbou)
- Dima Gerasimov (@karlicoss) <karlicoss@gmail.com>
- Max Woerner Chase (@mwchase) <max.chase@gmail.com>
- Johannes Maria Frank (@jmfrank63) <jmfrank63@gmail.com>
- Shane Steinert-Threlkeld (@shanest) <ssshanest@gmail.com>
- Tim Gates (@timgates42) <tim.gates@iress.com>
- Lior Goldberg (@goldberglior)
- Ryan Clary (@mrclary)
- Max Mäusezahl (@mmaeusezahl) <maxmaeusezahl@googlemail.com>
- Vladislav Serebrennikov (@endilll)
- Andrii Kolomoiets (@muffinmad)
- Leo Ryu (@Leo-Ryu)
- Joseph Birkner (@josephbirkner)
- Márcio Mazza (@marciomazza)
- Martin Vielsmaier (@moser) <martin@vielsmaier.net>
- TingJia Wu (@WutingjiaX) <wutingjia@bytedance.com>
- Nguyễn Hồng Quân <ng.hong.quan@gmail.com>

And a few more "anonymous" contributors.

Note: (@user) means a github user name.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/jedi-0.20.0.dist-info/LICENSE.txt`

```markdown
All contributions towards Jedi are MIT licensed.

-------------------------------------------------------------------------------
The MIT License (MIT)

Copyright (c) <2013> <David Halter and others, see AUTHORS.txt>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/jedi-0.20.0.dist-info/top_level.txt`

```markdown
jedi

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/parso-0.8.7.dist-info/AUTHORS.txt`

```markdown
Main Authors
============

David Halter (@davidhalter) <davidhalter88@gmail.com>

Code Contributors
=================
Alisdair Robertson (@robodair)
Bryan Forbes (@bryanforbes) <bryan@reigndropsfall.net>


Code Contributors (to Jedi and therefore possibly to this library)
==================================================================

Takafumi Arakaki (@tkf) <aka.tkf@gmail.com>
Danilo Bargen (@dbrgn) <mail@dbrgn.ch>
Laurens Van Houtven (@lvh) <_@lvh.cc>
Aldo Stracquadanio (@Astrac) <aldo.strac@gmail.com>
Jean-Louis Fuchs (@ganwell) <ganwell@fangorn.ch>
tek (@tek)
Yasha Borevich (@jjay) <j.borevich@gmail.com>
Aaron Griffin <aaronmgriffin@gmail.com>
andviro (@andviro)
Mike Gilbert (@floppym) <floppym@gentoo.org>
Aaron Meurer (@asmeurer) <asmeurer@gmail.com>
Lubos Trilety <ltrilety@redhat.com>
Akinori Hattori (@hattya) <hattya@gmail.com>
srusskih (@srusskih)
Steven Silvester (@blink1073)
Colin Duquesnoy (@ColinDuquesnoy) <colin.duquesnoy@gmail.com>
Jorgen Schaefer (@jorgenschaefer) <contact@jorgenschaefer.de>
Fredrik Bergroth (@fbergroth)
Mathias Fußenegger (@mfussenegger)
Syohei Yoshida (@syohex) <syohex@gmail.com>
ppalucky (@ppalucky)
immerrr (@immerrr) immerrr@gmail.com
Albertas Agejevas (@alga)
Savor d'Isavano (@KenetJervet) <newelevenken@163.com>
Phillip Berndt (@phillipberndt) <phillip.berndt@gmail.com>
Ian Lee (@IanLee1521) <IanLee1521@gmail.com>
Farkhad Khatamov (@hatamov) <comsgn@gmail.com>
Kevin Kelley (@kelleyk) <kelleyk@kelleyk.net>
Sid Shanker (@squidarth) <sid.p.shanker@gmail.com>
Reinoud Elhorst (@reinhrst)
Guido van Rossum (@gvanrossum) <guido@python.org>
Dmytro Sadovnychyi (@sadovnychyi) <jedi@dmit.ro>
Cristi Burcă (@scribu)
bstaint (@bstaint)
Mathias Rav (@Mortal) <rav@cs.au.dk>
Daniel Fiterman (@dfit99) <fitermandaniel2@gmail.com>
Simon Ruggier (@sruggier)
Élie Gouzien (@ElieGouzien)
Tim Gates (@timgates42) <tim.gates@iress.com>
Batuhan Taskaya (@isidentical) <isidentical@gmail.com>
Jocelyn Boullier (@Kazy) <jocelyn@boullier.bzh>


Note: (@user) means a github user name.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/parso-0.8.7.dist-info/LICENSE.txt`

```markdown
All contributions towards parso are MIT licensed.

Some Python files have been taken from the standard library and are therefore
PSF licensed. Modifications on these files are dual licensed (both MIT and
PSF). These files are:

- parso/pgen2/*
- parso/tokenize.py
- parso/token.py
- test/test_pgen2.py

Also some test files under test/normalizer_issue_files have been copied from
https://github.com/PyCQA/pycodestyle (Expat License == MIT License).

-------------------------------------------------------------------------------
The MIT License (MIT)

Copyright (c) <2013-2017> <David Halter and others, see AUTHORS.txt>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

-------------------------------------------------------------------------------

PYTHON SOFTWARE FOUNDATION LICENSE VERSION 2
--------------------------------------------

1. This LICENSE AGREEMENT is between the Python Software Foundation
("PSF"), and the Individual or Organization ("Licensee") accessing and
otherwise using this software ("Python") in source or binary form and
its associated documentation.

2. Subject to the terms and conditions of this License Agreement, PSF hereby
grants Licensee a nonexclusive, royalty-free, world-wide license to reproduce,
analyze, test, perform and/or display publicly, prepare derivative works,
distribute, and otherwise use Python alone or in any derivative version,
provided, however, that PSF's License Agreement and PSF's notice of copyright,
i.e., "Copyright (c) 2001, 2002, 2003, 2004, 2005, 2006, 2007, 2008, 2009, 2010,
2011, 2012, 2013, 2014, 2015 Python Software Foundation; All Rights Reserved"
are retained in Python alone or in any derivative version prepared by Licensee.

3. In the event Licensee prepares a derivative work that is based on
or incorporates Python or any part thereof, and wants to make
the derivative work available to others as provided herein, then
Licensee hereby agrees to include in any such work a brief summary of
the changes made to Python.

4. PSF is making Python available to Licensee on an "AS IS"
basis.  PSF MAKES NO REPRESENTATIONS OR WARRANTIES, EXPRESS OR
IMPLIED.  BY WAY OF EXAMPLE, BUT NOT LIMITATION, PSF MAKES NO AND
DISCLAIMS ANY REPRESENTATION OR WARRANTY OF MERCHANTABILITY OR FITNESS
FOR ANY PARTICULAR PURPOSE OR THAT THE USE OF PYTHON WILL NOT
INFRINGE ANY THIRD PARTY RIGHTS.

5. PSF SHALL NOT BE LIABLE TO LICENSEE OR ANY OTHER USERS OF PYTHON
FOR ANY INCIDENTAL, SPECIAL, OR CONSEQUENTIAL DAMAGES OR LOSS AS
A RESULT OF MODIFYING, DISTRIBUTING, OR OTHERWISE USING PYTHON,
OR ANY DERIVATIVE THEREOF, EVEN IF ADVISED OF THE POSSIBILITY THEREOF.

6. This License Agreement will automatically terminate upon a material
breach of its terms and conditions.

7. Nothing in this License Agreement shall be deemed to create any
relationship of agency, partnership, or joint venture between PSF and
Licensee.  This License Agreement does not grant permission to use PSF
trademarks or trade name in a trademark sense to endorse or promote
products or services of Licensee, or any third party.

8. By copying, installing or otherwise using Python, Licensee
agrees to be bound by the terms and conditions of this License
Agreement.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/parso-0.8.7.dist-info/top_level.txt`

```markdown
parso

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/posix_ipc-1.3.2.dist-info/top_level.txt`

```markdown
posix_ipc

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/xarray/tests/CLAUDE.md`

```markdown
# Testing Guidelines for xarray

## Handling Optional Dependencies

xarray has many optional dependencies that may not be available in all testing environments. Always use the standard decorators and patterns when writing tests that require specific dependencies.

### Standard Decorators

**ALWAYS use decorators** like `@requires_dask`, `@requires_cftime`, etc. instead of conditional `if` statements.

All available decorators are defined in `xarray/tests/__init__.py` (look for `requires_*` decorators).

Use the dask helpers from `xarray.tests` instead of importing `dask.array`
directly. In dask-array expression test mode those helpers point at the
registered chunk manager, so tests can run against either implementation.

### DO NOT use conditional imports or skipif

❌ **WRONG - Do not do this:**

```python
def test_mean_with_cftime():
    if has_dask:  # WRONG!
        ds = ds.chunk({})
        result = ds.mean()
```

❌ **ALSO WRONG - Avoid pytest.mark.skipif in parametrize:**

```python
@pytest.mark.parametrize(
    "chunk",
    [
        pytest.param(
            True, marks=pytest.mark.skipif(not has_dask, reason="requires dask")
        ),
        False,
    ],
)
def test_something(chunk): ...
```

✅ **CORRECT - Do this instead:**

```python
def test_mean_with_cftime():
    # Test without dask
    result = ds.mean()


@requires_dask
def test_mean_with_cftime_dask():
    # Separate test for dask functionality
    ds = ds.chunk({})
    result = ds.mean()
```

✅ **OR for parametrized tests, split them:**

```python
def test_something_without_dask():
    # Test the False case
    ...


@requires_dask
def test_something_with_dask():
    # Test the True case with dask
    ...
```

### Multiple dependencies

When a test requires multiple optional dependencies:

```python
@requires_dask
@requires_scipy
def test_interpolation_with_dask(): ...
```

### Importing optional dependencies in tests

For imports within test functions, use `pytest.importorskip`:

```python
def test_cftime_functionality():
    cftime = pytest.importorskip("cftime")
    # Now use cftime
```

### Common patterns

1. **Split tests by dependency** - Don't mix optional dependency code with base functionality:

   ```python
   def test_base_functionality():
       # Core test without optional deps
       result = ds.mean()
       assert result is not None


   @requires_dask
   def test_dask_functionality():
       # Dask-specific test
       ds_chunked = ds.chunk({})
       result = ds_chunked.mean()
       assert result is not None
   ```

2. **Use fixtures for dependency-specific setup**:

   ```python
   @pytest.fixture
   def dask_array():
       pytest.importorskip("dask.array")
       import dask.array as da

       return da.from_array([1, 2, 3], chunks=2)
   ```

3. **Check available implementations**:

   ```python
   from xarray.core.duck_array_ops import available_implementations


   @pytest.mark.parametrize("implementation", available_implementations())
   def test_with_available_backends(implementation): ...
   ```

### Key Points

- CI environments intentionally exclude certain dependencies (e.g., `all-but-dask`, `bare-minimum`)
- A test failing in "all-but-dask" because it uses dask is a test bug, not a CI issue
- Look at similar existing tests for patterns to follow

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pysimdjson-7.0.2.dist-info/top_level.txt`

```markdown
csimdjson
simdjson

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/fasteners-0.20.dist-info/top_level.txt`

```markdown
fasteners

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pytz-2026.3.post1.dist-info/LICENSE.txt`

```markdown
Copyright (c) 2003-2019 Stuart Bishop <stuart@stuartbishop.net>

Permission is hereby granted, free of charge, to any person obtaining a
copy of this software and associated documentation files (the "Software"),
to deal in the Software without restriction, including without limitation
the rights to use, copy, modify, merge, publish, distribute, sublicense,
and/or sell copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
DEALINGS IN THE SOFTWARE.

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/pytz-2026.3.post1.dist-info/top_level.txt`

```markdown
pytz

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/fonttools-4.65.0.dist-info/entry_points.txt`

```markdown
[console_scripts]
fonttools = fontTools.__main__:main
pyftmerge = fontTools.merge:main
pyftsubset = fontTools.subset:main
ttx = fontTools.ttx:main

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/fonttools-4.65.0.dist-info/top_level.txt`

```markdown
fontTools

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/morphops-0.2.0.dist-info/top_level.txt`

```markdown
morphops

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/botocore-1.43.96.dist-info/LICENSE.txt`

```markdown

                                 Apache License
                           Version 2.0, January 2004
                        http://www.apache.org/licenses/

   TERMS AND CONDITIONS FOR USE, REPRODUCTION, AND DISTRIBUTION

   1. Definitions.

      "License" shall mean the terms and conditions for use, reproduction,
      and distribution as defined by Sections 1 through 9 of this document.

      "Licensor" shall mean the copyright owner or entity authorized by
      the copyright owner that is granting the License.

      "Legal Entity" shall mean the union of the acting entity and all
      other entities that control, are controlled by, or are under common
      control with that entity. For the purposes of this definition,
      "control" means (i) the power, direct or indirect, to cause the
      direction or management of such entity, whether by contract or
      otherwise, or (ii) ownership of fifty percent (50%) or more of the
      outstanding shares, or (iii) beneficial ownership of such entity.

      "You" (or "Your") shall mean an individual or Legal Entity
      exercising permissions granted by this License.

      "Source" form shall mean the preferred form for making modifications,
      including but not limited to software source code, documentation
      source, and configuration files.

      "Object" form shall mean any form resulting from mechanical
      transformation or translation of a Source form, including but
      not limited to compiled object code, generated documentation,
      and conversions to other media types.

      "Work" shall mean the work of authorship, whether in Source or
      Object form, made available under the License, as indicated by a
      copyright notice that is included in or attached to the work
      (an example is provided in the Appendix below).

      "Derivative Works" shall mean any work, whether in Source or Object
      form, that is based on (or derived from) the Work and for which the
      editorial revisions, annotations, elaborations, or other modifications
      represent, as a whole, an original work of authorship. For the purposes
      of this License, Derivative Works shall not include works that remain
      separable from, or merely link (or bind by name) to the interfaces of,
      the Work and Derivative Works thereof.

      "Contribution" shall mean any work of authorship, including
      the original version of the Work and any modifications or additions
      to that Work or Derivative Works thereof, that is intentionally
      submitted to Licensor for inclusion in the Work by the copyright owner
      or by an individual or Legal Entity authorized to submit on behalf of
      the copyright owner. For the purposes of this definition, "submitted"
      means any form of electronic, verbal, or written communication sent
      to the Licensor or its representatives, including but not limited to
      communication on electronic mailing lists, source code control systems,
      and issue tracking systems that are managed by, or on behalf of, the
      Licensor for the purpose of discussing and improving the Work, but
      excluding communication that is conspicuously marked or otherwise
      designated in writing by the copyright owner as "Not a Contribution."

      "Contributor" shall mean Licensor and any individual or Legal Entity
      on behalf of whom a Contribution has been received by Licensor and
      subsequently incorporated within the Work.

   2. Grant of Copyright License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      copyright license to reproduce, prepare Derivative Works of,
      publicly display, publicly perform, sublicense, and distribute the
      Work and such Derivative Works in Source or Object form.

   3. Grant of Patent License. Subject to the terms and conditions of
      this License, each Contributor hereby grants to You a perpetual,
      worldwide, non-exclusive, no-charge, royalty-free, irrevocable
      (except as stated in this section) patent license to make, have made,
      use, offer to sell, sell, import, and otherwise transfer the Work,
      where such license applies only to those patent claims licensable
      by such Contributor that are necessarily infringed by their
      Contribution(s) alone or by combination of their Contribution(s)
      with the Work to which such Contribution(s) was submitted. If You
      institute patent litigation against any entity (including a
      cross-claim or counterclaim in a lawsuit) alleging that the Work
      or a Contribution incorporated within the Work constitutes direct
      or contributory patent infringement, then any patent licenses
      granted to You under this License for that Work shall terminate
      as of the date such litigation is filed.

   4. Redistribution. You may reproduce and distribute copies of the
      Work or Derivative Works thereof in any medium, with or without
      modifications, and in Source or Object form, provided that You
      meet the following conditions:

      (a) You must give any other recipients of the Work or
          Derivative Works a copy of this License; and

      (b) You must cause any modified files to carry prominent notices
          stating that You changed the files; and

      (c) You must retain, in the Source form of any Derivative Works
          that You distribute, all copyright, patent, trademark, and
          attribution notices from the Source form of the Work,
          excluding those notices that do not pertain to any part of
          the Derivative Works; and

      (d) If the Work includes a "NOTICE" text file as part of its
          distribution, then any Derivative Works that You distribute must
          include a readable copy of the attribution notices contained
          within such NOTICE file, excluding those notices that do not
          pertain to any part of the Derivative Works, in at least one
          of the following places: within a NOTICE text file distributed
          as part of the Derivative Works; within the Source form or
          documentation, if provided along with the Derivative Works; or,
          within a display generated by the Derivative Works, if and
          wherever such third-party notices normally appear. The contents
          of the NOTICE file are for informational purposes only and
          do not modify the License. You may add Your own attribution
          notices within Derivative Works that You distribute, alongside
          or as an addendum to the NOTICE text from the Work, provided
          that such additional attribution notices cannot be construed
          as modifying the License.

      You may add Your own copyright statement to Your modifications and
      may provide additional or different license terms and conditions
      for use, reproduction, or distribution of Your modifications, or
      for any such Derivative Works as a whole, provided Your use,
      reproduction, and distribution of the Work otherwise complies with
      the conditions stated in this License.

   5. Submission of Contributions. Unless You explicitly state otherwise,
      any Contribution intentionally submitted for inclusion in the Work
      by You to the Licensor shall be under the terms and conditions of
      this License, without any additional terms or conditions.
      Notwithstanding the above, nothing herein shall supersede or modify
      the terms of any separate license agreement you may have executed
      with Licensor regarding such Contributions.

   6. Trademarks. This License does not grant permission to use the trade
      names, trademarks, service marks, or product names of the Licensor,
      except as required for reasonable and customary use in describing the
      origin of the Work and reproducing the content of the NOTICE file.

   7. Disclaimer of Warranty. Unless required by applicable law or
      agreed to in writing, Licensor provides the Work (and each
      Contributor provides its Contributions) on an "AS IS" BASIS,
      WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
      implied, including, without limitation, any warranties or conditions
      of TITLE, NON-INFRINGEMENT, MERCHANTABILITY, or FITNESS FOR A
      PARTICULAR PURPOSE. You are solely responsible for determining the
      appropriateness of using or redistributing the Work and assume any
      risks associated with Your exercise of permissions under this License.

   8. Limitation of Liability. In no event and under no legal theory,
      whether in tort (including negligence), contract, or otherwise,
      unless required by applicable law (such as deliberate and grossly
      negligent acts) or agreed to in writing, shall any Contributor be
      liable to You for damages, including any direct, indirect, special,
      incidental, or consequential damages of any character arising as a
      result of this License or out of the use or inability to use the
      Work (including but not limited to damages for loss of goodwill,
      work stoppage, computer failure or malfunction, or any and all
      other commercial damages or losses), even if such Contributor
      has been advised of the possibility of such damages.

   9. Accepting Warranty or Additional Liability. While redistributing
      the Work or Derivative Works thereof, You may choose to offer,
      and charge a fee for, acceptance of support, warranty, indemnity,
      or other liability obligations and/or rights consistent with this
      License. However, in accepting such obligations, You may act only
      on Your own behalf and on Your sole responsibility, not on behalf
      of any other Contributor, and only if You agree to indemnify,
      defend, and hold each Contributor harmless for any liability
      incurred by, or claims asserted against, such Contributor by reason
      of your accepting any such warranty or additional liability.

   END OF TERMS AND CONDITIONS

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/botocore-1.43.96.dist-info/top_level.txt`

```markdown
botocore

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/orjson-3.12.0.dist-info/sboms/orjson.cyclonedx.json`

```markdown
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.5",
  "version": 1,
  "serialNumber": "urn:uuid:0fd14afa-18a5-47be-963d-b021adf13ddc",
  "metadata": {
    "timestamp": "2026-08-14T16:04:20.005431843Z",
    "tools": [
      {
        "vendor": "CycloneDX",
        "name": "cargo-cyclonedx",
        "version": "0.5.9"
      }
    ],
    "authors": [
      {
        "name": "ijl",
        "email": "ijl@mailbox.org"
      }
    ],
    "component": {
      "type": "library",
      "bom-ref": "path+file:///__w/orjson/orjson#3.12.0",
      "author": "ijl <ijl@mailbox.org>",
      "name": "orjson",
      "version": "3.12.0",
      "description": "Fast, correct Python JSON library supporting dataclasses, datetimes, and numpy",
      "scope": "required",
      "licenses": [
        {
          "expression": "MPL-2.0 AND (Apache-2.0 OR MIT)"
        }
      ],
      "purl": "pkg:cargo/orjson@3.12.0?download_url=file://.",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/ijl/orjson"
        }
      ],
      "components": [
        {
          "type": "library",
          "bom-ref": "path+file:///__w/orjson/orjson#3.12.0 bin-target-0",
          "name": "orjson",
          "version": "3.12.0",
          "purl": "pkg:cargo/orjson@3.12.0?download_url=file://.#src/lib.rs"
        }
      ]
    },
    "properties": [
      {
        "name": "cdx:rustc:sbom:target:all_targets",
        "value": "true"
      }
    ]
  },
  "components": [
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#associative-cache@3.0.1",
      "author": "Nick Fitzgerald <fitzgen@gmail.com>",
      "name": "associative-cache",
      "version": "3.0.1",
      "description": "A generic N-way associative cache with fixed-size capacity and random or least recently used (LRU) replacement.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "138b4febdc7d0135523c55358c97361fd45089bc65fe859ef21a58d0892deb00"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/associative-cache@3.0.1",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/associative-cache"
        },
        {
          "type": "vcs",
          "url": "https://github.com/fitzgen/associative-cache"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#bytecount@0.6.9",
      "author": "Andre Bogus <bogusandre@gmail.de>, Joshua Landau <joshua@landau.ws>",
      "name": "bytecount",
      "version": "0.6.9",
      "description": "count occurrences of a given byte, or the number of UTF-8 code points, in a byte slice, fast",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "175812e0be2bccb6abe50bb8d566126198344f707e304f45c648fd8f2cc0365e"
        }
      ],
      "licenses": [
        {
          "expression": "Apache-2.0 OR MIT"
        }
      ],
      "purl": "pkg:cargo/bytecount@0.6.9",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/llogiq/bytecount"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#cc@1.4.3",
      "name": "cc",
      "version": "1.4.3",
      "description": "A build-time dependency for Cargo build scripts to assist in invoking the native C compiler to compile native C code into a static archive to be linked into Rust code. ",
      "scope": "excluded",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "509591b7bcd67f4ef775afad7662703b4935daaa6ec0e5605cfb1090b32a2b6d"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/cc@1.4.3",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/cc"
        },
        {
          "type": "website",
          "url": "https://github.com/rust-lang/cc-rs"
        },
        {
          "type": "vcs",
          "url": "https://github.com/rust-lang/cc-rs"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#cfg-if@1.0.4",
      "author": "Alex Crichton <alex@alexcrichton.com>",
      "name": "cfg-if",
      "version": "1.0.4",
      "description": "A macro to ergonomically define an item depending on a large number of #[cfg] parameters. Structured like an if-else chain, the first matching branch is the item that gets emitted. ",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "9330f8b2ff13f34540b44e946ef35111825727b38d33286ef986142615121801"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/cfg-if@1.0.4",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/rust-lang/cfg-if"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#encoding_rs@0.8.35",
      "author": "Henri Sivonen <hsivonen@hsivonen.fi>",
      "name": "encoding_rs",
      "version": "0.8.35",
      "description": "A Gecko-oriented implementation of the Encoding Standard",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "75030f3c4f45dafd7586dd6780965a8c7e8e285a5ecb86713e63a79c5b2766f3"
        }
      ],
      "licenses": [
        {
          "expression": "(Apache-2.0 OR MIT) AND BSD-3-Clause"
        }
      ],
      "purl": "pkg:cargo/encoding_rs@0.8.35",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/encoding_rs/"
        },
        {
          "type": "website",
          "url": "https://docs.rs/encoding_rs/"
        },
        {
          "type": "vcs",
          "url": "https://github.com/hsivonen/encoding_rs"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#find-msvc-tools@0.1.11",
      "name": "find-msvc-tools",
      "version": "0.1.11",
      "description": "Find windows-specific tools, read MSVC versions from the registry and from COM interfaces",
      "scope": "excluded",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "d45db016d36b838f563236e9193d0ee6ce38f3f68b6c94e914b4929c96bbb890"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/find-msvc-tools@0.1.11",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/find-msvc-tools"
        },
        {
          "type": "vcs",
          "url": "https://github.com/rust-lang/cc-rs"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#itoap@1.0.1",
      "author": "Ryohei Machida <orcinus4627@gmail.com>",
      "name": "itoap",
      "version": "1.0.1",
      "description": "Even faster functions for printing integers with decimal format",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "9028f49264629065d057f340a86acb84867925865f73bbf8d47b4d149a7e88b8"
        }
      ],
      "licenses": [
        {
          "expression": "MIT"
        }
      ],
      "purl": "pkg:cargo/itoap@1.0.1",
      "externalReferences": [
        {
          "type": "website",
          "url": "https://github.com/Kogia-sima/itoap"
        },
        {
          "type": "vcs",
          "url": "https://github.com/Kogia-sima/itoap"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#jiff-core@0.1.0",
      "author": "Andrew Gallant <jamslam@gmail.com>",
      "name": "jiff-core",
      "version": "0.1.0",
      "description": "Low level datetime primitives for the Jiff library.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "7feca88439efe53da3754500c1851dedf3cb36c524dd5cf8225cc0794de95d09"
        }
      ],
      "licenses": [
        {
          "expression": "Unlicense OR MIT"
        }
      ],
      "purl": "pkg:cargo/jiff-core@0.1.0",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/jiff-core"
        },
        {
          "type": "website",
          "url": "https://github.com/BurntSushi/jiff/tree/master/crates/jiff-core"
        },
        {
          "type": "vcs",
          "url": "https://github.com/BurntSushi/jiff"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#libc@0.2.189",
      "name": "libc",
      "version": "0.2.189",
      "description": "Raw FFI bindings to platform libraries like libc.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "3eaf3ede3fee6db1a4c2ee091bf8a8b4dccdc6d17f656fb07896ee72867612f2"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/libc@0.2.189",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/rust-lang/libc"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#no-panic@0.1.37",
      "author": "David Tolnay <dtolnay@gmail.com>",
      "name": "no-panic",
      "version": "0.1.37",
      "description": "Attribute macro to require that the compiler prove a function can't ever panic.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "fc80370b4544f28ffa317e3c3474ee3ecbfe269196c01ae657d9f837a7d944a1"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/no-panic@0.1.37",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/no-panic"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/no-panic"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#once_cell@1.21.4",
      "author": "Aleksey Kladov <aleksey.kladov@gmail.com>",
      "name": "once_cell",
      "version": "1.21.4",
      "description": "Single assignment cells and lazy values.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "9f7c3e4beb33f85d45ae3e3a1792185706c8e16d043238c593331cc7cd313b50"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/once_cell@1.21.4",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/once_cell"
        },
        {
          "type": "vcs",
          "url": "https://github.com/matklad/once_cell"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.107",
      "author": "David Tolnay <dtolnay@gmail.com>, Alex Crichton <alex@alexcrichton.com>",
      "name": "proc-macro2",
      "version": "1.0.107",
      "description": "A substitute implementation of the compiler's `proc_macro` API to decouple token-based libraries from the procedural macro use case.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "985e7ec9bb745e6ce6535b544d84d6cd6f7ad8bd711c398938ae983b91a766d9"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/proc-macro2@1.0.107",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/proc-macro2"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/proc-macro2"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-build-config@0.28.3",
      "author": "PyO3 Project and Contributors <https://github.com/PyO3>",
      "name": "pyo3-build-config",
      "version": "0.28.3",
      "description": "Build configuration for the PyO3 ecosystem",
      "scope": "excluded",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "e368e7ddfdeb98c9bca7f8383be1648fd84ab466bf2bc015e94008db6d35611e"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/pyo3-build-config@0.28.3",
      "externalReferences": [
        {
          "type": "website",
          "url": "https://github.com/pyo3/pyo3"
        },
        {
          "type": "vcs",
          "url": "https://github.com/pyo3/pyo3"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-ffi@0.28.3",
      "author": "PyO3 Project and Contributors <https://github.com/PyO3>",
      "name": "pyo3-ffi",
      "version": "0.28.3",
      "description": "Python-API bindings for the PyO3 ecosystem",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "7f29e10af80b1f7ccaf7f69eace800a03ecd13e883acfacc1e5d0988605f651e"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/pyo3-ffi@0.28.3",
      "externalReferences": [
        {
          "type": "website",
          "url": "https://github.com/pyo3/pyo3"
        },
        {
          "type": "other",
          "url": "python"
        },
        {
          "type": "vcs",
          "url": "https://github.com/pyo3/pyo3"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.47",
      "author": "David Tolnay <dtolnay@gmail.com>",
      "name": "quote",
      "version": "1.0.47",
      "description": "Quasi-quoting macro quote!(...)",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "1fbf4db142a473a8d80c26bbf18454ed458bf8d26c8219c331daecfdbd079001"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/quote@1.0.47",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/quote/"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/quote"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#shlex@2.0.1",
      "author": "comex <comexk@gmail.com>, Fenhl <fenhl@fenhl.net>, Adrian Taylor <adetaylor@chromium.org>, Alex Touchet <alextouchet@outlook.com>, Daniel Parks <dp+git@oxidized.org>, Garrett Berg <googberg@gmail.com>",
      "name": "shlex",
      "version": "2.0.1",
      "description": "Split a string into shell words, like Python's shlex.",
      "scope": "excluded",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "f8fadd59c855ef2080decdef8ff161eb6661b86933c9d82e5ba29dc602a55aba"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/shlex@2.0.1",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/comex/rust-shlex"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#simdutf8@0.1.5",
      "author": "Hans Kratz <hans@appfour.com>",
      "name": "simdutf8",
      "version": "0.1.5",
      "description": "SIMD-accelerated UTF-8 validation.",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "e3a9fe34e3e7a50316060351f37187a3f546bce95496156754b601a5fa71b76e"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/simdutf8@0.1.5",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/simdutf8/"
        },
        {
          "type": "website",
          "url": "https://github.com/rusticstuff/simdutf8"
        },
        {
          "type": "vcs",
          "url": "https://github.com/rusticstuff/simdutf8"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#syn@3.0.3",
      "author": "David Tolnay <dtolnay@gmail.com>",
      "name": "syn",
      "version": "3.0.3",
      "description": "Parser for Rust source code",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "53e9bae58849f64dfa4f5d5ae372c8341f7305f82a3868709269343628b659a3"
        }
      ],
      "licenses": [
        {
          "expression": "MIT OR Apache-2.0"
        }
      ],
      "purl": "pkg:cargo/syn@3.0.3",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/syn"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/syn"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#target-lexicon@0.13.5",
      "author": "Dan Gohman <sunfish@mozilla.com>",
      "name": "target-lexicon",
      "version": "0.13.5",
      "description": "LLVM target triple types",
      "scope": "excluded",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "adb6935a6f5c20170eeceb1a3835a49e12e19d792f6dd344ccc76a985ca5a6ca"
        }
      ],
      "licenses": [
        {
          "expression": "Apache-2.0 WITH LLVM-exception"
        }
      ],
      "purl": "pkg:cargo/target-lexicon@0.13.5",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/target-lexicon/"
        },
        {
          "type": "vcs",
          "url": "https://github.com/bytecodealliance/target-lexicon"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#unicode-ident@1.0.24",
      "author": "David Tolnay <dtolnay@gmail.com>",
      "name": "unicode-ident",
      "version": "1.0.24",
      "description": "Determine whether characters have the XID_Start or XID_Continue properties according to Unicode Standard Annex #31",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "e6e4313cd5fcd3dad5cafa179702e2b244f760991f45397d14d4ebf38247da75"
        }
      ],
      "licenses": [
        {
          "expression": "(MIT OR Apache-2.0) AND Unicode-3.0"
        }
      ],
      "purl": "pkg:cargo/unicode-ident@1.0.24",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/unicode-ident"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/unicode-ident"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#xxhash-rust@0.8.18",
      "author": "Douman <douman@gmx.se>",
      "name": "xxhash-rust",
      "version": "0.8.18",
      "description": "Implementation of xxhash",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "aee1b19627c7c60102ab80d3a9cbe18de90bfe03bfa6c3715447681f0e8c8af6"
        }
      ],
      "licenses": [
        {
          "expression": "BSL-1.0"
        }
      ],
      "purl": "pkg:cargo/xxhash-rust@0.8.18",
      "externalReferences": [
        {
          "type": "vcs",
          "url": "https://github.com/DoumanAsh/xxhash-rust"
        }
      ]
    },
    {
      "type": "library",
      "bom-ref": "registry+https://github.com/rust-lang/crates.io-index#zmij@1.0.23",
      "author": "David Tolnay <dtolnay@gmail.com>",
      "name": "zmij",
      "version": "1.0.23",
      "description": "A double-to-string conversion algorithm based on Schubfach and xjb",
      "scope": "required",
      "hashes": [
        {
          "alg": "SHA-256",
          "content": "29666d0abbfad1e3dc4dcf6144730dd3a3ab225bbbdac83319345b1b44ccfc1b"
        }
      ],
      "licenses": [
        {
          "expression": "MIT"
        }
      ],
      "purl": "pkg:cargo/zmij@1.0.23",
      "externalReferences": [
        {
          "type": "documentation",
          "url": "https://docs.rs/zmij"
        },
        {
          "type": "vcs",
          "url": "https://github.com/dtolnay/zmij"
        }
      ]
    }
  ],
  "dependencies": [
    {
      "ref": "path+file:///__w/orjson/orjson#3.12.0",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#associative-cache@3.0.1",
        "registry+https://github.com/rust-lang/crates.io-index#bytecount@0.6.9",
        "registry+https://github.com/rust-lang/crates.io-index#cc@1.4.3",
        "registry+https://github.com/rust-lang/crates.io-index#encoding_rs@0.8.35",
        "registry+https://github.com/rust-lang/crates.io-index#itoap@1.0.1",
        "registry+https://github.com/rust-lang/crates.io-index#jiff-core@0.1.0",
        "registry+https://github.com/rust-lang/crates.io-index#once_cell@1.21.4",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3-build-config@0.28.3",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3-ffi@0.28.3",
        "registry+https://github.com/rust-lang/crates.io-index#simdutf8@0.1.5",
        "registry+https://github.com/rust-lang/crates.io-index#xxhash-rust@0.8.18",
        "registry+https://github.com/rust-lang/crates.io-index#zmij@1.0.23"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#associative-cache@3.0.1"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#bytecount@0.6.9"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#cc@1.4.3",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#find-msvc-tools@0.1.11",
        "registry+https://github.com/rust-lang/crates.io-index#shlex@2.0.1"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#cfg-if@1.0.4"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#encoding_rs@0.8.35",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#cfg-if@1.0.4"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#find-msvc-tools@0.1.11"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#itoap@1.0.1"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#jiff-core@0.1.0"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#libc@0.2.189"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#no-panic@0.1.37",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.107",
        "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.47",
        "registry+https://github.com/rust-lang/crates.io-index#syn@3.0.3"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#once_cell@1.21.4"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.107",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#unicode-ident@1.0.24"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-build-config@0.28.3",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#target-lexicon@0.13.5"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#pyo3-ffi@0.28.3",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#libc@0.2.189",
        "registry+https://github.com/rust-lang/crates.io-index#pyo3-build-config@0.28.3"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.47",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.107"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#shlex@2.0.1"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#simdutf8@0.1.5"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#syn@3.0.3",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#proc-macro2@1.0.107",
        "registry+https://github.com/rust-lang/crates.io-index#quote@1.0.47",
        "registry+https://github.com/rust-lang/crates.io-index#unicode-ident@1.0.24"
      ]
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#target-lexicon@0.13.5"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#unicode-ident@1.0.24"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#xxhash-rust@0.8.18"
    },
    {
      "ref": "registry+https://github.com/rust-lang/crates.io-index#zmij@1.0.23",
      "dependsOn": [
        "registry+https://github.com/rust-lang/crates.io-index#no-panic@0.1.37"
      ]
    }
  ]
}
```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/cloud_volume-12.14.4.dist-info/top_level.txt`

```markdown
cloudvolume

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/kiwisolver-1.5.1.dist-info/top_level.txt`

```markdown
kiwisolver

```

---

## Doc: `docs/flywire-connectome/venv/lib/python3.14/site-packages/jinxed-2.1.0.dist-info/top_level.txt`

```markdown
jinxed

```

---

