# semver-bash

Bash-only implementation of [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html)

The sourced semverXX theselves have oo dependencies other than Bash 3.2.57+ (i.e. Default Bash on macOS Catalina)



## Installation


```bash
curl -sSL https://raw.githubusercontent.com/parleer/semver-bash/latest/semver -o semver
chmod ugo+x semver
sudo mv semver /usr/local/bin
```


## Usage

The semver script supports both sourcing (i.e. `. semver`) and executing (`semver`).

Sourcing semver API:

semverParse 
semverEQ
semverLT
semverGT
semverBump


semver CLI:

semver parse
semver eq
semver lt
semver gt
semver bump

