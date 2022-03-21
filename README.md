# mbed-cli-mbed-deploy-issue

A simple repo to demostrate bug in `mbed deploy`. It does not handle correctly an update in the .lib -URI path change, i.e. if you switch to a different fork of the repo for example.

## Reproduction steps

Install the normal cli tooling following the official Mbed OS documentation.
Personally, I highly recommend using a Python virtualenv for this.

Check installation.

```
mbed --version
1.10.5

```

Start by `mbed import` with this repo.

```
mbed import git@github.com:JanneKiiskila/mbed-cli-mbed-deploy-issue.git
cd mbed-cli-mbed-deploy-issue/
```
This will also do the mbed deploy automatically.

Now, check into the branch `fork`.

```
git checkout fork
mbed deploy
```

The deploy will fail as shown below.

```
~/mbed/mbed-cli-mbed-deploy-issue$ mbed deploy
[mbed] Working path "/home/jannek/mbed/mbed-cli-mbed-deploy-issue" (library)
[mbed] Program path "/home/jannek/mbed/mbed-cli-mbed-deploy-issue"
[mbed] Updating library "wifi-ism43362" to rev #6e2e009be907
fatal: reference is not a tree: 6e2e009be907215bd2fa41df7ca1a18e10ab6b0a
[mbed] ERROR: Unable to update "wifi-ism43362" to rev #6e2e009be907
---
```

This is due to the fact, that the wifi-ism43362 git remote is still pointing to the
original one, which of course does not have the same hashes as the fork.

```
~/mbed/mbed-cli-mbed-deploy-issue/cd wifi-ism43362
~/mbed/mbed-cli-mbed-deploy-issue/wifi-ism43362$ git remote -v
origin	https://github.com/ARMmbed/wifi-ism43362 (fetch)
origin	https://github.com/ARMmbed/wifi-ism43362 (push)
```

## Workaround

You are required to manually delete the deployed repo/folder and then doing `mbed deploy` again.

```
rm -rf wifi-ism43362
mbed deploy
```

This will work, as it will start with a clean `git clone` operation with the correct repo URI.

However, this is a fully manual process. The tool could alternatively also notice the URI change and just add a different remote (or change the remote).
