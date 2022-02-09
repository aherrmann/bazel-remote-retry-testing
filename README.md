# Setup

Make sure to have Bazel installed.

Clone this repository:
```
$ git clone https://github.com/aherrmann/bazel-remote-retry-test.git
$ cd bazel-remote-retry-test.git
$ git submodule update --init
```

Build the patched Bazel with HTTP remote cache retry:
```
$ (cd bazel-patched && bazel build //src:bazel-bin)
```

Make the cache directories writable to the Docker users:
```
$ mkdir bazel-remote-dir nginx-dir
$ chmod o+w bazel-remote-dir nginx-dir
```

Start the remote cache and toxiproxy:
```
$ USER_ID=$(id -u) GROUP_ID=$(id -g) docker-compose up
```

Start a cached Bazel build using the bazel-remote HTTP cache:
```
$ BAZEL=$PWD/bazel-patched/bazel-bin/src/bazel
$ (cd bazel-original && $BAZEL clean && $BAZEL build //src:bazel-bin --remote_upload_local_results=true --remote_cache=http://localhost:8182)
```

Start a cached Bazel build using the bazel-remote GRPC cache:
```
$ BAZEL=$PWD/bazel-patched/bazel-bin/src/bazel
$ (cd bazel-original && $BAZEL clean && $BAZEL build //src:bazel-bin --remote_upload_local_results=true --remote_cache=grpc://localhost:9093)
```

Start a cached Bazel build using the nginx HTTP cache:
```
$ BAZEL=$PWD/bazel-patched/bazel-bin/src/bazel
$ (cd bazel-original && $BAZEL clean && $BAZEL build //src:bazel-bin --remote_upload_local_results=true --remote_cache=http://localhost:8184)
```

Clear the cache as needed:
```
$ sudo rm -rf bazel-remote-dir/* nginx-dir/*
```
