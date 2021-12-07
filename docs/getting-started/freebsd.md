# Running PhotoPrism On FreeBSD

*Note: This is contributed content. Click the edit link to perform changes and send a pull request.*

For FreeBSD / FreeNAS users, an unofficial [PhotoPrism port](https://github.com/huo-ju/photoprism-freebsd-port) 
is available.

The port will compile and install libtensorflow 1.15.2 and build `photoprism` from source on FreeBSD.

**1. Clone or download the port:**

```
git clone https://github.com/huo-ju/photoprism-freebsd-port
```

**2. Build TensorFlow and PhotoPrism from source, then install:**

```
cd photoprism-freebsd-port
make config
make && make install
```
When running the make config command, a CPU feature options dialog will be presented, and the default option is NONE.

**2.1 Compliling tensorflow on low spec machine** could lead to build failure as the system will eventually run out of RAM.
You can tune Bazel in this case by adjusting the Makefile, and particularly by adjusting the below line
```
pre-build:
.if ${FLAVOR:U} != notf
      ...
      cd ${WRKSRC}/docker/tensorflow/tensorflow-${TF_VERSION} && bazel --output_user_root="${WRKDIR}/.bazel" build --jobs=2 --config=opt //tensorflow:libtensorflow.so ${BAZEL_COPT}
      ...
```
Here you can adjust by
1. reducing the number of jobs to 1
2. further reduce RAM demand by adding before --jobs (a) --ram_utilization_factor 30 and/or (b) --discard_analysis_cache

**3. Add entries to rc.conf:**

```
photoprism_enable="YES"
photoprism_assetspath="/var/photoprism/assets"
photoprism_storagepath="/var/photoprism/storage"
```

You can add more command line parameters into photoprism_flags="" in the rc.conf

`photoprism config` shows all config parameters. 

**4. Start the service:**

```
service photoprism start
```

Done!

