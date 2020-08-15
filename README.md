# Gentoo emerge patch: make Qt5 build on older systems without SSE2

Regardless of what is set in CPU_FLAGS_X86, on Gentoo, Qt5 packages are built in such a way that they require SSE2 support. This means that on pre-SSE2 CPU, illegal instruction (SIGILL) errors occur while trying to emerge packages depending on Qt5.

The Qt5 build system includes a config option -no-sse2 to explicitly switch off Qt5 dependency on SSE2, but the Qt5 packages provided by Gentoo do not seem to provide any means to set this config option. At some point work-arounds have been included and then reverted by the Qt5 maintainers (as documented in [Gentoo bug 552942](https://bugs.gentoo.org/552942), for example), but as things stand there seems to be no supported way to address this. I am confident that the reasons for this are valid from a maintainer's perspective.

Because that did not change my wish to build Qt5 packages on my Pentium3 system, I have created a way to do so, using facilities provided by the portage build system.
Concretely, it provides for the -no-sse2 option to be passed to the Qt5 build config when the sse2 CPU flag is not set in CPU_FLAGS_X86. It does this by running a post-sync script that applies a patch to the qt5-build.eclass file provided in the "gentoo" repository. The original version of the patch was attached to [Gentoo bug 648004](https://bugs.gentoo.org/648004), this version has been aligned with the version of qt5-build.eclass that was then current.

## Installing

After obtaining (and, where applicable, extracting) the contents of this repository, the patch can be installed by issuing the following command from the directory where the source files reside:

```
$ sudo cp -t /etc/portage/repo.postsync.d qt5-nosse2-patch qt5-build-eclass-nosse2.patch
```

## Notes

The script:
* only executes for the gentoo repo;
* checks for the existence of the patch file before doing anything;
* first performs a dry run to verify that the patch can be applied successfully. Amongst others, this prevents the patch from being applied multiple times;
* reports success or failure, when run.

Furthermore:
* Future changes to the qt5-build.eclass file as provided by Gentoo may make the patch unusable, although I will try to maintain the patch at least as long as my Pentium3 system  remains alive;
* Not all Qt5 packages will build on older systems with the patch applied. QtWebEngine, for instance, will still refuse to build;
* If you do run into problems, please reach out and I'll see what I can do to help.

## Acknowledgments

* **Felix Tiede** - *Original version of the qt5-build.eclass patch*
* **eccerr0r** on [Gentoo forums](https://forums.gentoo.org) - *Bringing [Gentoo bug 648004](https://bugs.gentoo.org/648004) back to memory.*
