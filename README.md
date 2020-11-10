# semver-bash

Bash-only implementation of [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html). Correctly parses and compares complex version strings, but with a convenint loose-parsing algorithm that includes support for:
- leading zeros
- optional minor 
- optional patch
- empty pre-lease identifiers
- empty metadata identifiers


## Usage

**semver** can be used as a standard executable:

```
$ semver
semver

Usage:
  semver [command]

Commands:
  parse           Parse a semver
  bump            Bump semver version
  eq              Return whether semverA == semverB
  lt              Return whether semverA < semverB
  gt              Return whether semverA > semverB

Global Options:
  -h, --help           help
```

Or may be sourced and used as an API:

```
. $(type -p semver)
semverParse "1.2-alpha" && echo ${SEMVER[0]} is valid.
```


## Installation

```bash
curl -sSL https://raw.githubusercontent.com/parleer/semver-bash/latest/semver -o semver
chmod ugo+x semver
sudo mv semver /usr/local/bin
```


## Dependencies

The sourced semver API has no dependencies other than Bash 3.2.57+ 
(i.e. default Bash on macOS Catalina)

The semver CLI has the following dependencies:

- bash 3.2.57+
- basename


## CLI Examples

### semver parse

```
$ semver parse -h
semver parse command:
  Validate a verison string as a semantic version

Usage:
  semver parse version

Options:
  -h, --help    help
```

```
# --Parse
$ semver parse 1.2.3
1.2.3
$ semver parse 1.2.3-alpha
1.2.3-alpha
$ semver parse 1.2.3-alpha.beta
1.2.3-alpha.beta
$ semver parse 1.2.3-alpha-dash-beta
1.2.3-alpha-dash-beta
$ semver parse 1.2.3-alpha.doubledot.beta-beta
1.2.3-alpha.doubledot.beta-beta
$ semver parse 1.2.3+metadata
1.2.3+metadata
$ semver parse 1.2.3+metadata-dash-beta
1.2.3+metadata-dash-beta
$ semver parse 1.2.3+metadata.dot.xxx
1.2.3+metadata.dot.xxx
$ semver parse 1.2.3+metadata.dot.xxx.yyy
1.2.3+metadata.dot.xxx.yyy
# --Optional Minor, Optional patch
$ semver parse 1
1.0.0
$ semver parse 1
1.0.0
$ semver parse 1.0
1.0.0
$ semver parse 1.1
1.1.0
$ semver parse 1.0-alpha
1.0.0-alpha
$ semver parse 1-12.1-22.aaa.bbb+ccc-ddd.eee.fff-ggg
1.0.0-12.1-22.aaa.bbb+ccc-ddd.eee.fff-ggg
$ semver parse 1.2-12.1-22.aaa.bbb+ccc-ddd.eee.fff-ggg
1.2.0-12.1-22.aaa.bbb+ccc-ddd.eee.fff-ggg
# --Leading zeros
$ semver parse 01.02.03
1.2.3
$ semver parse 001.2.3-12
1.2.3-12
$ semver parse 001.2.3-12.1
1.2.3-12.1
$ semver parse 001.2.3-12.1-22
1.2.3-12.1-22
$ semver parse 001.2.3-12.1-22.aaa.bbb
1.2.3-12.1-22.aaa.bbb
$ semver parse 001.2.3-12.1-22.aaa.bbb+c
1.2.3-12.1-22.aaa.bbb+c
$ semver parse 001.2.3-12.1-22.aaa.bbb+ccc-ddd.eee.fff-ggg
1.2.3-12.1-22.aaa.bbb+ccc-ddd.eee.fff-ggg
$ semver parse 001.2.3-a-b.c.def.1.12+asd.x.y.zz.eee-fff
1.2.3-a-b.c.def.1.12+asd.x.y.zz.eee-fff
# --Empty pre-release identifiers
$ semver parse 1.2.3-
1.2.3
$ semver parse 1.2.3-.
1.2.3-0.0
$ semver parse 1.2.3-..
1.2.3-0.0.0
$ semver parse 1.2.3-...
1.2.3-0.0.0.0
# --Empty metadata identifiers
$ semver parse 1.2.3+
1.2.3
$ semver parse 1.2.3+.
1.2.3+0.0
$ semver parse 1.2.3+..
1.2.3+0.0.0
$ semver parse 1.2.3+...
1.2.3+0.0.0.0
$ semver parse 1.2.3-...+...
1.2.3-0.0.0.0+0.0.0.0
```

### semver eq

```
$ semver eq -h
semver eq command:
  Returns 0 if versionA == versionB

Usage:
  semver eq versionA versionB

Options:
  -h, --help    help for update
```

```
# --Examples from https://semver.org/
$ semver eq 1.0.0-alpha+001 eq 1.0.0-alpha+20130313144700
0
$ semver eq 1.0.0-alpha+001 eq 1.0.0-alpha+exp.sha.5114f85
0
$ semver eq 1.0.0+20130313144700 eq 1.0.0+exp.sha.5114f85
0
$ semver eq 1.0.0 eq 1.0.0
0
# --Dotted pre-release and build metadata
$ semver eq 1.0.0-rc.1+asdf.x.y.z 1.0.0-rc.1+qwer.a.b.c
0
```

### semver lt

```
$ semver lt -h
semver lt command:
  Retursn 0 if versionA < versionB

Usage:
  semver eq versionA versionB

Options:
  -h, --help    help for update
```

```
# --Examples from https://semver.org/
$ semver lt 1.9.0 1.10.0
0
$ semver lt 1.10.0 1.11.0
0
$ semver lt 1.0.0 2.0.0
0
$ semver lt 2.0.0 2.1.0
0
$ semver lt 2.1.0 2.1.1
0
$ semver lt 1.0.0-alpha 1.0.0
0
$ semver lt 1.0.0-alpha 1.0.0-alpha.1
0
$ semver lt 1.0.0-alpha.1 1.0.0-alpha.beta
0
$ semver lt 1.0.0-alpha.beta 1.0.0-beta
0
$ semver lt 1.0.0-beta 1.0.0-beta.2
0
$ semver lt 1.0.0-beta.2 1.0.0-beta.11
0
$ semver lt 1.0.0-rc.1 1.0.0
0
```

### semver gt

```
$ semver gt -h
semver eq command:
  Increment a version

Usage:
  semver eq versionA versionB

Options:
  -h, --help    help for update
```

```
$ semver gt 1.0.0-alpha.beta 1.0.0-alpha.1
0
$ semver gt 1.0.0-alpha.beta 1.0.0-alpha.1
0
# --Dotted pre-release and build metadata
$ semver gt 1.0.0-rc.123.0aaa.yyy-beta.zzz+asdf.x--y.z 1.0.0-rc.1+qwer.a.b.c
0
```


### semver bump

```
$ semver bump -h
semver bump command:
  Increment a version

Usage:
  semver bump [-Mmp] version

Options:
  -M            Increment Major
  -m            Increment Minor
  -p            Increment Patch
  -h, --help    help for update
```

```
$ semver bump -p 1
1.0.1
$ semver bump -m 1
1.1.0
$ semver bump -M 1
2.0.0
$ semver bump -Mmp 1
2.1.1
```

## API Examples

### semverParse 

```
. $(type -p semver)
semverParse "1.2-alpha" && echo ${SEMVER[0]} is valid.
```

### semverEQ

```
. $(type -p semver)
semverEQ "1.2" "1.2.0" && echo "Equal" || echo "Not-Equal"
```

### semverLT

```
. $(type -p semver)
semverLT "1.2-alpha" "1.2.0" && echo "Less-than" || echo "Not-Less-than"
```

### semverGT

```
. $(type -p semver)
semverGT "1.2" "1.2-alpha" && echo "Greater-than" || echo "Not-Greater-than"
```

### semverBump

```
. $(type -p semver)
new_version=$(semverBump -p "1.2")
```
