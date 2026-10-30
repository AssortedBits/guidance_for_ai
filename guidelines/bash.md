# coding guidelines for Bash and POSIX Shell files

This document gives our guidelines for the authoring and maintenance of scripts written in either the Bash language or the POSIX Shell language (sometimes called "Bourne" shell for historical reasons).

## background

We have an ecosystem of scripts, which is mainly concentrated in a single suite. The suite consists of:
- *invokable*[^invokable] scripts that can be invoked from any context (mainly `*.sh`, `*.bash`)
- *source* aka "environment" scripts that can be "sourced"/"loaded" only from within a compatible shell (`*.shrc`, `*.bashrc`).

[^invokable]: Here, by "invoking" a script, we mean executing the script itself as a command (e.g. `./script.bash`), so its *shebang* selects the interpreter. We do **not** mean passing its filename to an explicit shell invocation (e.g. `bash script.bash` or `sh script.bash`).

Altogether, the *source* scripts compose an *API*, and are therefore intended to remain composable with each other.

For the *API*, we have largely standardized on Bash. Because compatibility goes only in one direction, POSIX Shell *source* scripts (`*.shrc`) usually can only inhabit the bottom of the "food chain".

## file extensions:

### Bash

Scripts that require Bash **must** use suffixes `.bash` and `.bashrc`.  
This prevents some unpleasant surprises after deployment, caused by the fact that the command `sh` is an alias to `bash` on most but not all systems.

### POSIX Shell

Scripts that are compatible with POSIX Shell **may** use `.sh` and `.shrc`.  
They may also use the Bash suffixes instead.

Here are considerations that should guide this choice:
- POSIX Shell scripts cannot consume most of our *API*.
- POSIX Shell compatibility is a good thing, all else being equal, because it's universal, whereas Bash is merely very common.  
- POSIX Shell restriction is a bad thing, all else being equal, because POSIX Shell lacks some popular ease-of-use features.  
- Our team conventionally makes Bash available in our environments.
- Changing the suffix on a script that is low-level or widely-shared can be fraught.  
  That is, suffixing with `.bash` defends against the need to update clients if we later want to use Bash features (such as are required by our *API*).
- It is easy for a POSIX Shell incompatibility to go undiscovered until long after deployment, because it is common for systems to alias `/bin/sh` to Bash.

## either *source* or *invokable*, but never both

Each script file should be written to only be invoked or only be sourced, not both.

### requirements for *invokable* scripts

- Suffix is `.bash` for Bash scripts, `.sh` for POSIX Shell scripts.
- A "shebang" is present.  
  That is, the first line of the file is e.g.:  
  ```
  #!/bin/bash
  ```
- Execute bit is set (`chmod a+x`).
- No client "sources" the file (i.e. via the `.` or `source` commands).

### requirements for *source* scripts

- Suffix is `.bashrc` for Bash scripts, `.shrc` for POSIX Shell scripts.
- No "shebang" is present.  
- Execute bit is not set (`chmod a-x`).
- All clients consume the file only by "sourcing" it (i.e. via the `.` or `source` commands).

## coding guidelines

### In *API* scripts, prefer to avoid executable code outside functions.  

Sourcing an *API* script should usually be "free".  
Of course, some "environment" scripts are not API scripts, and appropriately do expensive things (e.g. loading an SDK).

### Prefer to mark variables `local`.

#### Beware the `local` gotcha.

Be advised of a certain "gotcha" regarding Bash's `local` directive: It suppresses exit codes of any commands on the same line.

For this reason, you will often see in the code the following pattern:

```bash
local myVar
myVar="$(myCommand)"
#handle any nonzero exit code $?
```

Whereas, the following code contains a bug because of the gotcha:

```bash
local myVar="$(myFailingCommand)"
#Exit code $? here is ALWAYS 0, even if the above command failed.
```
