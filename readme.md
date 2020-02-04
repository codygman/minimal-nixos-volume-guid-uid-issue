Below is an org generated walkthrough of me trying to get a docker volume to
preserve user uid/gid on nixos that isn't working.

This minimal repro exists because I'm not sure why it's not working, if it's nix
specific, or just some silly user error.

- [readme](#orgebaabf2)
- [create folder on host before mounting on docker so it's uid/gid are inherited](#org02f489a)
- [show foobar and it&rsquo;s perms](#orgd8b5b8b)
- [write file into `pre-created-folder-should-keep-host-user-uid-gid` and it **should** but does not retain host uid/gid permissions](#orgf76064a)
- [ls file in `pre-created-folder-should-keep-host-user-uid-gid` and show incorrect root perms :(](#org5d80a68)


<a id="orgebaabf2"></a>

# readme


<a id="org02f489a"></a>

## create folder on host before mounting on docker so it's uid/gid are inherited

```sh
mkdir -p pre-created-folder-should-keep-host-user-uid-gid
```


<a id="orgd8b5b8b"></a>

## show foobar and it&rsquo;s perms

```sh
ls -larth
```

    total 12K
    drwxr-xr-x 3 cody users 4.0K Feb  4 08:18 ..
    drwxr-xr-x 3 cody users 4.0K Feb  4 08:19 .
    drwxr-xr-x 2 cody users 4.0K Feb  4 08:24 pre-created-folder-should-keep-host-user-uid-gid


<a id="orgf76064a"></a>

## write file into `pre-created-folder-should-keep-host-user-uid-gid` and it **should** but does not retain host uid/gid permissions

```sh
docker run -v $(pwd)/pre-created-folder-should-keep-host-user-uid-gid/:/app alpine sh -c "cd /app/ && touch test"
```


<a id="org5d80a68"></a>

## ls file in `pre-created-folder-should-keep-host-user-uid-gid` and show incorrect root perms :(

```sh
ls -larth pre-created-folder-should-keep-host-user-uid-gid
```

    total 8.0K
    drwxr-xr-x 3 cody users 4.0K Feb  4 08:19 ..
    drwxr-xr-x 2 cody users 4.0K Feb  4 08:24 .
    -rw-r--r-- 1 root root     0 Feb  4 08:27 test
