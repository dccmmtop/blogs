```shell
sudo apt-get remove nginx nginx-common # Removes all but config files.

sudo apt-get purge nginx nginx-common # Removes everything.

sudo apt-get autoremove # After using any of the above commands, use this in order to remove dependencies used by nginx which are no longer required.
```

or

`apt-get remove --purge nginx nginx-full nginx-common`
