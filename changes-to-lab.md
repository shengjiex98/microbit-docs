# Changes to microbit-lab

1. Added scrolling code
2. Removed `/build` folders in exercises from git.
3. Removed `/.settings` folders from projects.
4. Changed project names in `.project` setting file from `microbit_led` to `lab4_and_lab5`, `lab6`, `lab4_solution` and so on.
5. Added build configurations for each project (using `make`).
6. Added launch (debug) configuration files. Turns out these configurations are not stored in the `.project` or `.cproject` files and must be imported separately.
7. Changed "Linked resources" to use relative paths. (Was using `/home/pk/workspace/microbit/blessed-devel/include` etc.)