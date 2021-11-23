## Logos 9 snap package

### Download packages
```bash
rsync -r -t -v --progress -u --partial -s rsync://<ServerIP>/snaps/wine-platform-6-logos_6.17_amd64.snap .
rsync -r -t -v --progress -u --partial -s rsync://<ServerIP>/snaps/logos9_0.1_amd64.snap .
```
or use grsync with source as: "rsync://<ServerIP>/snaps/logos9_0.1_amd64.snap"
and your preferred destination.

### Install wine and logos9 packages
```bash
sudo snap install --dangerous ./wine-platform-6-logos_6.17_amd64.snap
sudo snap install --devmode --dangerous ./logos9_0.1_amd64.snap
# Manually connect platform plug (b/c of "--dangerous" flag?).
sudo snap connect logos9:wine-6-logos wine-platform-6-logos:wine-6-logos
```

### Run app (installs Logos and dependencies on 1st run [1.4 to 1.6 GB])
```bash
logos9
# OR
WINEDEBUG=1 SOMMELIER_DEBUG=1 logos9 # if you really want to see what's going on
```
or find it in your apps menu.

### Dependencies
#### snap packages [1072 MB]:
- logos9 [<1 MB]
  - snapd [34 MB]
  - core18 [58 MB]
  - gnome-3-28-1804 [172 MB]
    - gtk-common-themes [68 MB]
  - wine-platform-6-logos [361 MB]
  - *wine-platform-6-staging [376 MB]: currently not used*
  - wine-platform-runtime [363 MB]
#### wine installs [130 MB]
- corefonts (11 total) [4 MB]
- dotnet48 [120 MB] (dotNetFx40 [48 MB]+ dotNetFx48 [69 MB])
#### Logos installer [217 MB or 450 MB]
- Logos-x64.msi [217 MB or 450 MB]
  - Should be 217 MB, but nethogs reports ~450 MB.
- Snap installer will use ~/Downloads/Logos-x64.msi if it exists, otherwise it
  will download this one: https://downloads.logoscdn.com/LBS9/Installer/9.9.0.0011/Logos-x64.msi

### Debugging
Get full debugging info with these ENV variables:
```bash
WINEDEBUG=+all SOMMELIER_DEBUG=1 logos9 # WAY too much output!
```
However, the snap package sets some environment variables at runtime, so it
doesn't work to override them like this:
```bash
WINEDLLOVERRIDES= logos9
```
Instead, you need to enter into the snap's shell environment and run the command
like this:
```bash
# Enter logos9's shell.
snap run --shell logos9
# Run Logos.exe via sommelier script.
WINEDLLOVERRIDES= sommelier run-exe
# If running other executable, pass its location as RUN_EXE variable.
WINEDLLOVERRIDES= RUN_EXE="${SNAP_USER_COMMON}/.wine/drive_c/users/$USER/AppData/Local/Logos/System/LogosIndexer.exe" sommelier run-exe
```

### logos9 snap commands
```bash
logos9              # initialize wine; install Logos.exe; run Logos.exe
logos9.indexer      # run LogosIndexer.exe; still needs to be debugged
logos9.init         # ensure that wine is initialized, then exit
logos9.wine
logos9.winecfg
logos9.winetricks   # CLI only; fails if no args passed because of mis-linked yad executable
```
