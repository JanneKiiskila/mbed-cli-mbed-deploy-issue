# mbed-cli-mbed-deploy-issue

A simple repo to demostrate bug in `mbed deploy`. It does not handle correctly an update in the .lib -URI path change, i.e. if you switch to a different fork of the repo for example.

## Reproduction steps

Install the normal cli tooling following the official Mbed OS documentation.
Personaly, I highly recommend using a Python virtualenv for this.

Check installation.

```
mbed --version
1.10.5

```

Start by `mbed import` with this repo.

Now, check into the branch `fork`.

```
git checkout fork
mbed deploy
```

The deploy will fail with uknown hash.

## Workaround

You are required to manually delete the deployed repo and then doing `mbed deploy` again.

```
rm -rf wifi-ism43362
mbed deploy
```

This will work, as it will start with a clean `git clone` operation with the correct repo URI.
However, this is a fully manual process. The tool could alternatively also notice the URI change and just add a different remote (or change the remote).
