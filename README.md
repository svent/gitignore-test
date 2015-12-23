# gitignore-test

This is a test repository to test tools that implement support for .gitignore files.

The documentation of the .gitignore format and some examples that
have been reproduced here can be found at [https://git-scm.com/docs/gitignore](https://git-scm.com/docs/gitignore).

The main purpose of this repository is to test the .gitignore implementation of [sift](https://github.com/svent/sift),
a grep alternative.


## Usage

### Preparation
As this repository contains files that should normally be ignored, you need to
re-build a correct state after cloning it:
```
$ git rm -r --cached .
$ git add .
$ git commit -m "preparing test"
```


### Test

All files that are not excluded by a .gitignore contain the line ```foo: OK```.

all files that should be excluded contain the line ```foo: FAIL```.

```git grep``` should be used as a reference implementation:
```
$ git grep ^foo: | sort
Documentation/foo.html:foo: OK
arch/foo/kernel/vmlinux.lds.S:foo: OK
bar/testfile:foo: OK
dirpattern:foo: OK
git-sample-3/foo/bar:foo: OK
htmldoc/docs.html:foo: OK
log/foo.log:foo: OK
src/findthis.o:foo: OK
subdir/rootsubdir/foo:foo: OK
```

Executing sift with git support should show ALL files containing ```foo: OK``` and
NO files containing ```foo: FAIL```:

```
$ sift --git ^foo: | sort
Documentation/foo.html:foo: OK
arch/foo/kernel/vmlinux.lds.S:foo: OK
bar/testfile:foo: OK
dirpattern:foo: OK
git-sample-3/foo/bar:foo: OK
htmldoc/docs.html:foo: OK
log/foo.log:foo: OK
src/findthis.o:foo: OK
subdir/rootsubdir/foo:foo: OK
```

When sift is used from another working directory, the result should be the same:
```
$ DIR=$PWD; (cd /etc/; sift --git ^foo: $DIR| sort)
/tmp/gitignore-test/Documentation/foo.html:foo: OK
/tmp/gitignore-test/arch/foo/kernel/vmlinux.lds.S:foo: OK
/tmp/gitignore-test/bar/testfile:foo: OK
/tmp/gitignore-test/dirpattern:foo: OK
/tmp/gitignore-test/git-sample-3/foo/bar:foo: OK
/tmp/gitignore-test/htmldoc/docs.html:foo: OK
/tmp/gitignore-test/log/foo.log:foo: OK
/tmp/gitignore-test/src/findthis.o:foo: OK
/tmp/gitignore-test/subdir/rootsubdir/foo:foo: OK
```

