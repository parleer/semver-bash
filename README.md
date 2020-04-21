# semver-bash

Bash-only implementation of [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html)




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



## Usage

The semver script supports both sourcing (i.e. `. semver`) and executing (`semver`).

### semver API example usage:

semverParse 

```
. $(type -p semver)
semverParse "1.2-alpha" && echo ${SEMVER[0]} is valid.
```

semverEQ

```
. $(type -p semver)
semverEQ "1.2" "1.2.0" && echo "Equal" || echo "Not-Equal"
```

semverLT

```
. $(type -p semver)
semverLT "1.2-alpha" "1.2.0" && echo "Less-than" || echo "Not-Less-than"
```

semverGT

```
. $(type -p semver)
semverGT "1.2" "1.2-alpha" && echo "Greater-than" || echo "Not-Greater-than"
```

semverBump

```
. $(type -p semver)
new_version=$(semverBump -p "1.2")
```



### semver CLI:

semver parse

semver eq

semver lt

semver gt

semver bump

