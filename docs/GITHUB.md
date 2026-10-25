# Source and distribution repositories

Public installers and user documentation: https://github.com/MazeXONE/GeminiBridgePro-Releases

Private application source: https://github.com/MazeXONE/GeminiBridgePro

Only authorized source users can clone/build the application. Cloning the public repository gives documentation only.

```sh
git clone https://github.com/MazeXONE/GeminiBridgePro.git
cd GeminiBridgePro
npm ci
```

Work in that clone. To receive later source changes, save/commit your work, run git pull --ff-only, then npm ci. Build instructions are in [BUILD.md](BUILD.md). Pulling source does not update an installed desktop application.

For users, download the installer or use Settings > Updates. For maintainers with source access, [publication instructions](https://github.com/MazeXONE/GeminiBridgePro/blob/main/docs/UPDATES.md) keep signing secrets and source out of public distribution.
