# lzc-downtify

[Downtify](https://github.com/henriquesebastiao/downtify) packaged as a Lazycat NAS `.lpk`.
Paste a Spotify link, get a tagged audio file in your Documents.

The package ships no code of its own: it runs the upstream image
`ghcr.io/henriquesebastiao/downtify:3.10.0`, mirrored on `registry.lazycat.cloud`.

## How it is wired

- **Sign-in**: box account through Lazycat OIDC (`oidc_redirect_path`). Password
  login is off. Box administrators (`ADMIN` group) become Downtify admins.
- **Music**: saved to `Documents/<first user>/Music/Downtify`, or to the folder set
  in the deploy parameter `music_dir` (must be under `/lzcapp/document`).
  The image declares `/downloads` as a volume, so `setup_script` links `/music`
  to the chosen folder and the app uses `DOWNLOAD_DIR=/music`.
- **App data** (accounts, settings, YouTube cookies, caches): `/lzcapp/var/data` → `/data`.
- `background_task: true`: queued downloads and playlist monitor keep running
  with no tab open.

## Build & install

```bash
# mirror upstream image (Windows lzc-cli holds the appstore login)
powershell.exe -Command "lzc-cli appstore copy-image ghcr.io/henriquesebastiao/downtify:<ver>"
# put the returned registry.lazycat.cloud/... ref in docker/embed/downtify/Dockerfile,
# bump version in downtify-lzc/package.yml, then build with the Windows lzc-cli:
cd downtify-lzc
powershell.exe -Command "cd '$(wslpath -w .)'; lzc-cli project build"
cp cloud.lazycat.app.downtify-v*.lpk /mnt/c/Users/fede9/Desktop/
powershell.exe -Command "lzc-cli app install C:\Users\fede9\Desktop\cloud.lazycat.app.downtify-v<ver>.lpk"
```

## License

Wrapper: MIT (see `LICENSE`). Downtify: AGPL-3.0.
