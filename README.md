# renovate-reproduction-d31461-inherited
Reproduction repo for https://github.com/renovatebot/renovate/discussions/31461

# Content

Files used as inherited configuration
* `default.json` : root inherited config
* `packageRules-npm.json` and `packageRules-maven.json` : configure npm and maven mirror
* `hostRules-nexus.json` : add authentication for mirrors

# Renovate execution

````bash
docker run --rm -it -v "$(pwd):$(pwd)" -w $(pwd) \
  -e NEXUS3_USERNAME="nexus3-user" \
  -e NEXUS3_PASSWORD="nexus3-pass" \
  -e RENOVATE_USERNAME="renovate-user" \
  -e RENOVATE_PASSWORD="renovate-pass" \
  -e RENOVATE_CONFIG_FILE="$(pwd)/test-config.js" \
  -v renovate:/renovate \
  renovate/renovate \
    --platform="gerrit" \
    --persist-repo-data="true" \
    --base-dir="/renovate" \
    --inherit-config-file-name="default.json" \
    --inherit-config-repo-name="xavier-calland/renovate-reproduction-d31461-inherited" \
    --inherit-config="true" \
    --endpoint="https://gerrit.priv.foo.com"
````

Caution : this command is the one I use with Gerrit (although this repo is in Github).

With `test-config.js` :

````js
module.exports = {
  repositories: ["xavier-calland/another-repo"],
  globalExtends: [
    "group:allNonMajor",
  ],

  secrets: {
    NEXUS3_USERNAME: process.env.NEXUS3_USERNAME,
    NEXUS3_PASSWORD: process.env.NEXUS3_PASSWORD,
  },
};
````

# Problem

## Renovate <= 39.63.0

Authentication is applied when accessing `npm.priv.foo.com` and `maven.priv.foo.com`

## Renovate > 39.63.0

Authentication is not used.

Logs tell `hostRules: no authentication for npm.priv.atolcd.com`

I think [this commit](https://github.com/renovatebot/renovate/commit/eb074924655488bbd62dba7f55e75bfb925e0f94) is the cause of the change in behavior.
