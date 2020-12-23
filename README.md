# Fork of Bioconductor iClusterPlus to resolve installation error in conda

Reproduce:

```sh
conda create -n testenv -c conda-forge r-base r-devtools r-biocmanager
conda activate testenv
# in conda env
Rscript -e 'BiocManager::install("iClusterPlus")'
```

Error:

```
# skip some lines
#
* installing *source* package ‘iClusterPlus’ ...
** using staged installation
 This package has only been tested with gfortran.
 So some checks are needed.
 R_HOME is /home/huolinhe/miniconda3/envs/testenv/lib/R
Attempting to determine R_ARCH...
R_ARCH is 
Attempting to detect how R was configured for Fortran 90....
    Unsupported Fortran 90 compiler or Fortran 90
    compilers unavailable! Stop!
ERROR: configuration failed for package ‘iClusterPlus’
* removing ‘/home/huolinhe/miniconda3/envs/testenv/lib/R/library/iClusterPlus’

The downloaded source packages are in
	‘/tmp/huolinhe/RtmpreCgTW/downloaded_packages’
Updating HTML index of packages in '.Library'
Making 'packages.html' ... done
Warning message:
In install.packages(...) : installation of one or more packages failed,
  probably ‘iClusterPlus’
```

Fix:

```diff
diff --git a/configure b/configure
index 671da19..5f9aa21 100755
--- a/configure
+++ b/configure
@@ -2144,7 +2144,7 @@ if test "x${FC}" != x; then
 fi
 
 case "${FC}" in
-    gfortran*)
+    *gfortran*)
       echo "  R configured for gfortran; Good!"
       OUR_FCFLAGS="-fdefault-real-8 -ffixed-form"
```

Explain:

For conda-forge `r-base`, the `FC` environment defined by R `Makeconf` is `x86_64-conda-linux-gnu-gfortran` or something like `x86_64-conda_(* os)-linux-gnu-gfortran`, so a glob pattern fix should match it.

The fixed version of iClusterPlus is on my github https://github.com/zitsen/iClusterPlus .

Install it with `devtools` or `remotes`:

```sh
Rscript -e 'devtools::install_github("zitsen/iClusterPlus")'
```

Works good:

```
* installing to library ‘/home/huolinhe/miniconda3/envs/testenv/lib/R/library’
* installing *source* package ‘iClusterPlus’ ...
** using staged installation
 This package has only been tested with gfortran.
 So some checks are needed.
 R_HOME is /home/huolinhe/miniconda3/envs/testenv/lib/R
Attempting to determine R_ARCH...
R_ARCH is 
Attempting to detect how R was configured for Fortran 90....
  R configured for gfortran; Good!
configure: creating ./config.status
config.status: creating src/Makevars
** libs
x86_64-conda-linux-gnu-cc -I"/home/huolinhe/miniconda3/envs/testenv/lib/R/include" -DNDEBUG   -DNDEBUG -D_FORTIFY_SOURCE=2 -O2 -isystem /home/huolinhe/miniconda3/envs/testenv/include -I/home/huolinhe/miniconda3/envs/testenv/include -Wl,-rpath-link,/home/huolinhe/miniconda3/envs/testenv/lib  -fpic  -march=nocona -mtune=haswell -ftree-vectorize -fPIC -fstack-protector-strong -fno-plt -O2 -ffunction-sections -pipe -isystem /home/huolinhe/miniconda3/envs/testenv/include -fdebug-prefix-map=/home/conda/feedstock_root/build_artifacts/r-base_1603148876453/work=/usr/local/src/conda/r-base-3.6.3 -fdebug-prefix-map=/home/huolinhe/miniconda3/envs/testenv=/usr/local/src/conda-prefix  -c iClusterBayes.c -o iClusterBayes.o
x86_64-conda-linux-gnu-cc -I"/home/huolinhe/miniconda3/envs/testenv/lib/R/include" -DNDEBUG   -DNDEBUG -D_FORTIFY_SOURCE=2 -O2 -isystem /home/huolinhe/miniconda3/envs/testenv/include -I/home/huolinhe/miniconda3/envs/testenv/include -Wl,-rpath-link,/home/huolinhe/miniconda3/envs/testenv/lib  -fpic  -march=nocona -mtune=haswell -ftree-vectorize -fPIC -fstack-protector-strong -fno-plt -O2 -ffunction-sections -pipe -isystem /home/huolinhe/miniconda3/envs/testenv/include -fdebug-prefix-map=/home/conda/feedstock_root/build_artifacts/r-base_1603148876453/work=/usr/local/src/conda/r-base-3.6.3 -fdebug-prefix-map=/home/huolinhe/miniconda3/envs/testenv=/usr/local/src/conda-prefix  -c iClusterPlus.c -o iClusterPlus.o
x86_64-conda-linux-gnu-gfortran -fno-optimize-sibling-calls -fdefault-real-8 -ffixed-form -fpic  -fopenmp -march=nocona -mtune=haswell -ftree-vectorize -fPIC -fstack-protector-strong -fno-plt -O2 -ffunction-sections -pipe -isystem /home/huolinhe/miniconda3/envs/testenv/include -fdebug-prefix-map=/home/conda/feedstock_root/build_artifacts/r-base_1603148876453/work=/usr/local/src/conda/r-base-3.6.3 -fdebug-prefix-map=/home/huolinhe/miniconda3/envs/testenv=/usr/local/src/conda-prefix  -c  newGLMnet.f90 -o newGLMnet.o
x86_64-conda-linux-gnu-cc -shared -L/home/huolinhe/miniconda3/envs/testenv/lib/R/lib -Wl,-O2 -Wl,--sort-common -Wl,--as-needed -Wl,-z,relro -Wl,-z,now -Wl,--disable-new-dtags -Wl,--gc-sections -Wl,-rpath,/home/huolinhe/miniconda3/envs/testenv/lib -Wl,-rpath-link,/home/huolinhe/miniconda3/envs/testenv/lib -L/home/huolinhe/miniconda3/envs/testenv/lib -Wl,-rpath-link,/home/huolinhe/miniconda3/envs/testenv/lib -o iClusterPlus.so iClusterBayes.o iClusterPlus.o newGLMnet.o -llapack -lblas -lgfortran -lm -lgomp -lquadmath -lpthread -lgfortran -lm -lgomp -lquadmath -lpthread -L/home/huolinhe/miniconda3/envs/testenv/lib/R/lib -lR
installing to /home/huolinhe/miniconda3/envs/testenv/lib/R/library/00LOCK-iClusterPlus/00new/iClusterPlus/libs
** R
** data
*** moving datasets to lazyload DB
** demo
** inst
** byte-compile and prepare package for lazy loading
** help
*** installing help indices
** building package indices
** installing vignettes
** testing if installed package can be loaded from temporary location
** checking absolute paths in shared objects and dynamic libraries
** testing if installed package can be loaded from final location
** testing if installed package keeps a record of temporary installation path
* creating tarball
packaged installation of ‘iClusterPlus’ as ‘iClusterPlus_1.26.0_R_x86_64-conda-linux-gnu.tar.gz’
* DONE (iClusterPlus)
```