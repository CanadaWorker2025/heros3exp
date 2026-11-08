# Engine patches

`save-name-utf8.patch` fixes the save dialog filename field on Windows and adds
UTF-8/native-path regression tests. Paths are relative to the engine repository.

Apply to a matching VCMI source checkout with `git apply save-name-utf8.patch`,
then rebuild the client. The tests require a build with the unit test target
enabled. This patch does not modify the save format or existing save files.

The source fix has passed static checks only. No compiled client is included.
