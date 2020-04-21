#!/usr/bin/env bash

# semverParse
# 
# Parse a valid semver according to https://semver.org/spec/v2.0.0.html and 
# return it's individual compoentns in an array. 
# 
# The array name defaults to SEMVER, but can be specified in parameter 2.
# 
# The array is filled as follows:
# 
#   SEMVER[0] - properly formatted semver
#   SEMVER[1] - major
#   SEMVER[2] - minor
#   SEMVER[3] - patch
#   SEMVER[4] - pre-release
#   SEMVER[5] - buld metadata
#
# Optionally, supports loose parsing of semver:
#  - Optional Minor, Optional Patch: "1" -> "1.0.0" and "1.1" -> "1.1.0"
#  - Allow leading zeros: "001.002" -> "1.2.0"
#
# Note: Do not call with varname of any local varaibles to the semverParse function. (i.e. __v, __t, __i)
# 
# Usage: semverParse semver [varname]
# Returns: 0 on successful parse, 1 otherwise
# Side effects: Variable [varname] is set as follows:
# 
semverParse() {
  local __v __t __i varname="${2-SEMVER}"; [[ ! "$varname" =~ ^[a-zA-Z_][a-zA-Z0-9_]*$ ]] && { echo "Invalid varname: $varname." 1>&2; exit 1; }

# optional minor, optional patch, allow leading zeros, allow empty pre-release identifiers, allow empty metadata identifiers (captures 1, 3, 5, 7, 9)
  if [[ "$1" =~ ^([0-9]+)(\.([0-9]+))?(\.([0-9]+))?(-([0-9A-Za-z\.\-]*))?(\+([0-9A-Za-z\.\-]*))?$ ]]; then
    __v=("$((10#${BASH_REMATCH[1]}))" "$((10#${BASH_REMATCH[3]}))" "$((10#${BASH_REMATCH[5]}))" "${BASH_REMATCH[7]}" "${BASH_REMATCH[9]}")
    for __i in 3 4; do while [[ "${__v[$__i]}" =~ \.\. ]]; do __v[$__i]=${__v[$__i]/\.\./.0.}; done; [[ "${__v[$__i]}" =~ ^\. ]] &&  __v[$__i]="0${__v[$__i]}"; [[ "${__v[$__i]}" =~ \.$ ]] &&  __v[$__i]="${__v[$__i]}0"; done

# optional minor, optional patch, allow leading zeros (captures 1, 3, 5, 7, 11)
  # if [[ "$1" =~ ^([0-9]+)(\.([0-9]+))?(\.([0-9]+))?(-([0-9A-Za-z-]+(\.([0-9A-Za-z-]+))*))?(\+([0-9A-Za-z-]+(\.([0-9A-Za-z-]+))*))?$ ]]; then
  #    __v=("$((10#${BASH_REMATCH[1]}))" "$((10#${BASH_REMATCH[3]}))" "$((10#${BASH_REMATCH[5]}))" "${BASH_REMATCH[7]}" "${BASH_REMATCH[11]}")

# optional minor, optional patch (captures 1, 3, 5, 7, 12)
  # if [[ "$1" =~ ^(0|[1-9][0-9]*)(\.(0|[1-9][0-9]*))?(\.(0|[1-9][0-9]*))?(-((0|[1-9][0-9]*|[0-9]*[a-zA-Z-][0-9a-zA-Z-]*)(\.(0|[1-9][0-9]*|[0-9]*[a-zA-Z-][0-9a-zA-Z-]*))*))?(\+([0-9a-zA-Z-]+(\.[0-9a-zA-Z-]+)*))?$ ]]; then
  #   __v=("$((10#${BASH_REMATCH[1]}))" "$((10#${BASH_REMATCH[3]}))" "$((10#${BASH_REMATCH[5]}))" "${BASH_REMATCH[7]}" "${BASH_REMATCH[12]}")    

# full validation (captures 1, 2, 3, 5, 10)
  # if [[ "$1" =~ ^(0|[1-9][0-9]*)\.(0|[1-9][0-9]*)\.(0|[1-9][0-9]*)(-((0|[1-9][0-9]*|[0-9]*[a-zA-Z-][0-9a-zA-Z-]*)(\.(0|[1-9][0-9]*|[0-9]*[a-zA-Z-][0-9a-zA-Z-]*))*))?(\+([0-9a-zA-Z-]+(\.[0-9a-zA-Z-]+)*))?$ ]]; then
  #   __v=("${BASH_REMATCH[1]}" "${BASH_REMATCH[2]}" "${BASH_REMATCH[3]}" "${BASH_REMATCH[5]}" "${BASH_REMATCH[10]}")

    __t="${__v[0]}.${__v[1]}.${__v[2]}"
    [[ ! -z "${__v[3]}" ]] && __t="${__t}-${__v[3]}"
    [[ ! -z "${__v[4]}" ]] && __t="${__t}+${__v[4]}"
    eval "${varname}=(${__t})"
    for __i in ${!__v[@]}; do eval "${varname}+=(\"${__v[$__i]}\")"; done
  else
    echo "Not a valid semver: $1" 1>&2; return 1;
  fi
  return 0
}

# semverEQ
# 
# Test whether semverA has equal precedence to semverB
# 
# Usage: semverEQ semverA semverB
# Returns: 0 if a == b, else 1
# 
semverEQ() {
  local a b
  semverParse "$1" a && semverParse "$2" b || return 1
  if [[ "${a[1]}" -eq "${b[1]}" && "${a[2]}" -eq "${b[2]}" && "${a[3]}" -eq "${b[3]}" ]]; then
    [[ -z "${a[4]}" && -z "${b[4]}" ]] && return 0 || { [ "${a[4]}" == "${b[4]}" ] && return 0 || return 1; }
  fi
  return 1
}

# semverLT
# 
# Test whether semverA has lower precedence than semverB
# 
# Usage: semverGT semverA semverB
# Returns: 0 if a < b, else 1
# 
semverLT() {
  local a b ap bp
  semverParse "$1" a && semverParse "$2" b || return 1
  for i in 1 2 3; do [[ "${a[$i]}" -ne "${b[$i]}" ]] && { [[ "${a[$i]}" -lt "${b[$i]}" ]] && return 0 || return 1; }; done
  [[ -z "${a[4]}" ]] && return 1
  [[ -z "${b[4]}" ]] && return 0
  ap=(${a[4]//./ }) bp=(${b[4]//./ })
  for i in ${!ap[@]}; do
    [[ -z "${bp[$i]}" ]] && return 1
    if [[ "${ap[$i]}" =~ ^[0-9]+$ ]]; then
        [[ ! "${bp[$i]}" =~ ^[0-9]+$ ]] && return 0
        [[ "${ap[$i]}" -ne "${bp[$i]}" ]] && { [[ "${ap[$i]}" -lt "${bp[$i]}" ]] && return 0 || return 1; }
    else
      [[ "${bp[$i]}" =~ ^[0-9]+$ ]] && return 1
      [[ "${ap[$i]}" != "${bp[$i]}" ]] && { [[ "${ap[$i]}" < "${bp[$i]}" ]] && return 0 || return 1; }
    fi
  done
  [[ $(($i+1)) -lt ${#bp[@]} ]] && return 0 || return 1
}

# semverGT
# 
# Test whether semverA has higher precedence than semverB
# 
# Usage: semverGT semverA semverB
# Returns: 0 if a > b, else 1
# 
semverGT() {
  semverLT "$2" "$1"
}

# semverBump
# 
# Increment a version string using Semantic Versioning (SemVer) terminology.
# 
# Usage: semverBump [-Mmp] semver
# Output: Writes bumped semver to stdout
# 
semverBump() {
  local major minor patch Option OPTIND
  while getopts ":Mmp" Option; do
    case $Option in
      M ) major=true;;
      m ) minor=true;;
      p ) patch=true;;
    esac
  done
  shift $(($OPTIND - 1))
  local a
  semverParse "$1" a || return 1
  [ ! -z $major ] && { ((a[1]++)); a[2]=0; a[3]=0; }
  [ ! -z $minor ] && { ((a[2]++)); a[3]=0; }
  [ ! -z $patch ] && { ((a[3]++)); }
  local t="${a[1]}.${a[2]}.${a[3]}"
  [[ ! -z "${a[4]}" ]] && t="${t}-${a[4]}"
  [[ ! -z "${a[5]}" ]] && t="${t}+${a[5]}"
  echo "$t"
}

if [[ $BASH_SOURCE == $0 ]]; then


SELF_BASENAME=$(basename "${BASH_SOURCE[0]}")

define(){ read -r -d '' "${1}" || true; }

if [ -t 1 ] || [ -n "$OPT_COLOR" ]; then 
  C_RESET="\x1B[0m"; C_BOLD="\x1B[1m"; C_DEFAULT="\x1B[39m"; C_WHITE="\x1B[97m"; C_LTGRAY="\x1B[37m"; C_CYAN="\x1B[36m"; C_LTCYAN="\x1B[96m"; C_RED="\x1B[31m"; C_YELLOW="\x1B[33m";
fi


_echo() {
  [ -t 1 ] && echo -en "${1}" || echo -en "${1}" 1>&2
}

_error() {
  echo -en "${1}" 1>&2
}

semver_parse_usage() {
  define usage <<EOF

${C_BOLD}${C_LTGRAY}semver parse command:${C_RESET}
  Validate a verison string as a semantic version

${C_LTGRAY}Usage:${C_RESET}
  ${SELF_BASENAME} parse version

${C_LTGRAY}Options:
  ${C_LTCYAN}-h, --help    ${C_RESET}help

EOF
  _echo "${usage}\n\n"
}

semver_parse() {
  local arg
  for arg in "$@"; do
    case $arg in
      -h|--help) semver_parse_usage; exit;;
    esac
  done
  semverParse "$@" && echo "${SEMVER[0]}"
}

semver_eq_usage() {
  define usage <<EOF

${C_BOLD}${C_LTGRAY}semver eq command:${C_RESET}
  Returns 0 if versionA == versionB

${C_LTGRAY}Usage:${C_RESET}
  ${SELF_BASENAME} eq versionA versionB

${C_LTGRAY}Options:
  ${C_LTCYAN}-h, --help    ${C_RESET}help for update

EOF
  _echo "${usage}\n\n"
}

semver_eq() {
  local arg
  for arg in "$@"; do
    case $arg in
      -h|--help) semver_eq_usage; exit;;
    esac
  done
  semverEQ "$@"
  local result=$?
  echo $result
  exit $result
}

semver_lt_usage() {
  define usage <<EOF

${C_BOLD}${C_LTGRAY}semver lt command:${C_RESET}
  Retursn 0 if versionA < versionB

${C_LTGRAY}Usage:${C_RESET}
  ${SELF_BASENAME} eq versionA versionB

${C_LTGRAY}Options:
  ${C_LTCYAN}-h, --help    ${C_RESET}help for update

EOF
  _echo "${usage}\n\n"
}

semver_lt() {
  local arg
  for arg in "$@"; do
    case $arg in
      -h|--help) semver_lt_usage; exit;;
    esac
  done
  semverLT "$@"
  local result=$?
  echo $result
  exit $result
}

semver_gt_usage() {
  define usage <<EOF

${C_BOLD}${C_LTGRAY}semver eq command:${C_RESET}
  Increment a version

${C_LTGRAY}Usage:${C_RESET}
  ${SELF_BASENAME} eq versionA versionB

${C_LTGRAY}Options:
  ${C_LTCYAN}-h, --help    ${C_RESET}help for update

EOF
  _echo "${usage}\n\n"
}

semver_gt() {
  local arg
  for arg in "$@"; do
    case $arg in
      -h|--help) semver_gt_usage; exit;;
    esac
  done
  semverGT "$@"
  local result=$?
  echo $result
  exit $result
}

semver_bump_usage() {
  define usage <<EOF

${C_BOLD}${C_LTGRAY}semver bump command:${C_RESET}
  Increment a version

${C_LTGRAY}Usage:${C_RESET}
  ${SELF_BASENAME} bump [-Mmp] version

${C_LTGRAY}Options:
  ${C_LTCYAN}-M            ${C_RESET}Increment Major
  ${C_LTCYAN}-m            ${C_RESET}Increment Minor
  ${C_LTCYAN}-p            ${C_RESET}Increment Patch
  ${C_LTCYAN}-h, --help    ${C_RESET}help for update

EOF
  _echo "${usage}\n\n"
}

semver_bump() {
  local arg
  for arg in "$@"; do
    case $arg in
      -h|--help) semver_bump_usage; exit;;
    esac
  done
  semverBump "$@"
}

semver_help() {
   define usage <<EOF

${C_BOLD}${C_LTGRAY}semver${C_RESET}

${C_LTGRAY}Usage:${C_RESET}
  ${SELF_BASENAME} [command]

EOF
  define commands <<EOF

${C_LTGRAY}Commands:${C_RESET}
  ${C_LTCYAN}parse           ${C_RESET}Parse a semver
  ${C_LTCYAN}bump            ${C_RESET}Bump semver version
  ${C_LTCYAN}eq              ${C_RESET}Return whether semverA == semverB
  ${C_LTCYAN}lt              ${C_RESET}Return whether semverA < semverB
  ${C_LTCYAN}gt              ${C_RESET}Return whether semverA > semverB

EOF
  define options <<EOF

${C_LTGRAY}Global Options:${C_RESET}
  ${C_LTCYAN}-h, --help           ${C_RESET}help

EOF
  _echo "${usage}\n\n"
  _echo "${commands}\n\n"
  _echo "${options}\n\n"
}

case "$1" in
  parse|bump|eq|lt|gt|help) 
    cmd="$1"
    shift;
    eval "semver_${cmd}" "$@"
    ;;
  -h|--help|"") semver_help;;
  *) _error "Unrecognized command: $1\n"; semver_help;;
esac


fi


