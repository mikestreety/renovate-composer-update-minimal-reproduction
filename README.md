# Minimal Repoduction for https://github.com/renovatebot/renovate/discussions/23404

## Background

When updating packages in a sub-package, I want to run a `composer update` on that package to update the parent lock file.

E.g. `lavitto/typo3-form-to-database` has a version of `3.0.1` to be bumped to and `typo3/cms-core` needs to go up to `11.5.29`.

This was solved using `allowedPostUpgradeCommands` as per [this answer](https://github.com/renovatebot/renovate/discussions/23404#discussioncomment-6476436)

## Issue

When there is more than 1 package to update, it fails as it seems to update the package lock and then run the `postUpgradeTasks` - I would expect this to run after each file change.

It fails with the following errors:

### ⚠ Artifact update problem

Renovate failed to update artifacts related to this branch. You probably do not want to merge this MR as-is.

♻ Renovate will retry this branch, including artifacts, only when one of the following happens:

 - any of the package files in this branch needs updating, or
 - the branch becomes conflicted, or
 - you click the rebase/retry checkbox if found above, or
 - you rename this MR's title to start with "rebase!" to trigger it manually

The artifact failure details are included below:

##### File name: app/package/composer.json

```
Command failed: composer update lavitto/typo3-form-to-database:3.0.1 --no-scripts --no-progress --no-interaction
Loading composer repositories with package information
Info from https://repo.packagist.org: #StandWithUkraine
Updating dependencies
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].
    - app/package dev-main requires typo3/cms-core 11.5.30 -> found typo3/cms-core[v11.5.30] but the package is fixed to v11.5.29 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.

Use the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.

```

##### File name: app/package/composer.json

```bash
Command failed: composer update typo3/cms-core:11.5.30 --no-scripts --no-progress --no-interaction
Loading composer repositories with package information
Updating dependencies
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].
    - app/package dev-main requires lavitto/typo3-form-to-database 3.0.1 -> found lavitto/typo3-form-to-database[3.0.1] but the package is fixed to 3.0.0 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.

Use the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.

```

## Logs

Output of a renovate run on this repo

<details>
<summary>Log output</summary>

```bash
DEBUG: Using RE2 as regex engine
DEBUG: Parsing configs
DEBUG: Checking for config file in /Users/mike/Sites/renovate/config.js
DEBUG: Converting GITHUB_COM_TOKEN into a global host rule
DEBUG: File config
       "config": {
         "platform": "gitlab",
         "endpoint": "https://hosted.gitlab.com/api/v4//",
         "token": "***********",
         "extends": [
           ":ignoreModulesAndTests",
           "group:monorepos",
           "group:recommended",
           "workarounds:all"
         ],
         "major": {"enabled": false},
         "allowedPostUpgradeCommands": [
           "composer update {{{depName}}}:{{{newVersion}}} --no-scripts --no-progress --no-interaction"
         ],
         "packageRules": [
           {
             "groupName": "Composer dependencies",
             "matchManagers": ["composer"],
             "matchUpdateTypes": ["minor", "patch"],
             "addLabels": ["composer"],
             "semanticCommitScope": "composer",
             "postUpgradeTasks": {
               "commands": [
                 "composer update {{{depName}}}:{{{newVersion}}} --no-scripts --no-progress --no-interaction"
               ],
               "fileFilters": ["composer.lock"],
               "executionMode": "update"
             }
           },
           {
             "matchManagers": ["composer"],
             "matchUpdateTypes": ["patch"],
             "groupSlug": "composer-patch"
           },
           {
             "matchManagers": ["composer"],
             "matchUpdateTypes": ["minor"],
             "groupSlug": "composer-minor"
           }
         ],
         "enabledManagers": ["composer"]
       }
DEBUG: CLI config
       "config": {"repositories": ["ll/testrenovate"]}
DEBUG: Env config
       "config": {
         "hostRules": [
           {"hostType": "github", "matchHost": "github.com", "token": "***********"}
         ]
       }
DEBUG: Combined config
       "config": {
         "platform": "gitlab",
         "endpoint": "https://hosted.gitlab.com/api/v4//",
         "token": "***********",
         "extends": [
           ":ignoreModulesAndTests",
           "group:monorepos",
           "group:recommended",
           "workarounds:all"
         ],
         "major": {"enabled": false},
         "allowedPostUpgradeCommands": [
           "composer update {{{depName}}}:{{{newVersion}}} --no-scripts --no-progress --no-interaction"
         ],
         "packageRules": [
           {
             "groupName": "Composer dependencies",
             "matchManagers": ["composer"],
             "matchUpdateTypes": ["minor", "patch"],
             "addLabels": ["composer"],
             "semanticCommitScope": "composer",
             "postUpgradeTasks": {
               "commands": [
                 "composer update {{{depName}}}:{{{newVersion}}} --no-scripts --no-progress --no-interaction"
               ],
               "fileFilters": ["composer.lock"],
               "executionMode": "update"
             }
           },
           {
             "matchManagers": ["composer"],
             "matchUpdateTypes": ["patch"],
             "groupSlug": "composer-patch"
           },
           {
             "matchManagers": ["composer"],
             "matchUpdateTypes": ["minor"],
             "groupSlug": "composer-minor"
           }
         ],
         "enabledManagers": ["composer"],
         "hostRules": [
           {"hostType": "github", "matchHost": "github.com", "token": "***********"}
         ],
         "repositories": ["ll/testrenovate"]
       }
DEBUG: Adding trailing slash to endpoint
DEBUG: Enabling forkProcessing while in non-autodiscover mode
DEBUG: Found valid git version: 2.39.2
DEBUG: GitLab version is: 16.3.0
DEBUG: Using platform gitAuthor: 🤖 RenovateBot <email@email.com>
DEBUG: Adding token authentication for gitlab.lldev.co.uk to hostRules
DEBUG: Using baseDir: /var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate
DEBUG: Using cacheDir: /var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/cache
DEBUG: Using containerbaseDir: /var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/cache/containerbase
DEBUG: Initializing Renovate internal cache into /var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/cache/renovate/renovate-cache-v1
DEBUG: Commits limit = null
DEBUG: Setting global hostRules
DEBUG: Adding token authentication for github.com to hostRules
DEBUG: Adding token authentication for gitlab.lldev.co.uk to hostRules
DEBUG: validatePresets()
DEBUG: Reinitializing hostRules for repo
DEBUG: Clearing hostRules
DEBUG: Adding token authentication for github.com to hostRules
DEBUG: Adding token authentication for gitlab.lldev.co.uk to hostRules
 INFO: Repository started (repository=ll/testrenovate)
       "renovateVersion": "36.55.0"
DEBUG: Using localDir: /var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/repos/gitlab/ll/testrenovate (repository=ll/testrenovate)
DEBUG: PackageFiles.clear() - Package files deleted (repository=ll/testrenovate)
DEBUG: ll/testrenovate default branch = main (repository=ll/testrenovate)
DEBUG: Enabling Git FS (repository=ll/testrenovate)
DEBUG: Using http URL: https://hosted.gitlab.com/ll/testrenovate.git (repository=ll/testrenovate)
DEBUG: Resetting npmrc (repository=ll/testrenovate)
DEBUG: Resetting npmrc (repository=ll/testrenovate)
DEBUG: checkOnboarding() (repository=ll/testrenovate)
DEBUG: isOnboarded() (repository=ll/testrenovate)
DEBUG: findPr(renovate/configure, Configure Renovate, !open) (repository=ll/testrenovate)
DEBUG: findFile(renovate.json) (repository=ll/testrenovate)
DEBUG: Initializing git repository into /var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/repos/gitlab/ll/testrenovate (repository=ll/testrenovate)
DEBUG: Performing blobless clone (repository=ll/testrenovate)
DEBUG: git clone completed (repository=ll/testrenovate)
       "durationMs": 1122
DEBUG: latest repository commit (repository=ll/testrenovate)
       "latestCommit": {
         "hash": "96e6f2432982782a78fd459dbdf1e4a1a7329bbf",
         "date": "2023-08-25T16:04:34+01:00",
         "message": "Merge branch 'renovate/configure' into 'main'",
         "refs": "HEAD -> main, origin/main, origin/HEAD",
         "body": "Configure Renovate\n\nSee merge request ll/testrenovate!2",
         "author_name": "Mike Street",
         "author_email": "mike@liquidlight.co.uk"
       }
DEBUG: Config file exists, fileName: renovate.json (repository=ll/testrenovate)
DEBUG: ensureIssueClosing() (repository=ll/testrenovate)
DEBUG: Repo is onboarded (repository=ll/testrenovate)
DEBUG: Found renovate.json config file (repository=ll/testrenovate)
DEBUG: Repository config (repository=ll/testrenovate)
       "fileName": "renovate.json",
       "config": {"$schema": "https://docs.renovatebot.com/renovate-schema.json"}
DEBUG: migrateAndValidate() (repository=ll/testrenovate)
DEBUG: No config migration necessary (repository=ll/testrenovate)
DEBUG: Setting hostRules from config (repository=ll/testrenovate)
DEBUG: Found repo ignorePaths (repository=ll/testrenovate)
       "ignorePaths": [
         "**/node_modules/**",
         "**/bower_components/**",
         "**/vendor/**",
         "**/examples/**",
         "**/__tests__/**",
         "**/test/**",
         "**/tests/**",
         "**/__fixtures__/**"
       ]
DEBUG: No vulnerability alerts found (repository=ll/testrenovate)
DEBUG: No baseBranches (repository=ll/testrenovate)
DEBUG: extract() (repository=ll/testrenovate)
DEBUG: Setting current branch to main (repository=ll/testrenovate)
DEBUG: latest commit (repository=ll/testrenovate)
       "branchName": "main",
       "latestCommitDate": "2023-08-25T16:04:34+01:00"
DEBUG: Applying enabledManagers filtering (repository=ll/testrenovate)
DEBUG: Using file match: (^|/)([\w-]*)composer\.json$ for manager composer (repository=ll/testrenovate)
DEBUG: Matched 2 file(s) for manager composer: app/package/composer.json, composer.json (repository=ll/testrenovate)
DEBUG: manager extract durations (ms) (repository=ll/testrenovate)
       "managers": {"composer": 7}
DEBUG: Found composer package files (repository=ll/testrenovate)
DEBUG: Found 2 package file(s) (repository=ll/testrenovate)
 INFO: Dependency extraction complete (repository=ll/testrenovate, baseBranch=main)
       "stats": {
         "managers": {"composer": {"fileCount": 2, "depCount": 3}},
         "total": {"fileCount": 2, "depCount": 3}
       }
DEBUG: Dependency app/package has unsupported/unversioned value dev-main (versioning=composer) (repository=ll/testrenovate)
DEBUG: PackageFiles.add() - Package file saved for base branch (repository=ll/testrenovate, baseBranch=main)
DEBUG: Package releases lookups complete (repository=ll/testrenovate, baseBranch=main)
DEBUG: branchifyUpgrades (repository=ll/testrenovate)
DEBUG: detectSemanticCommits() (repository=ll/testrenovate)
DEBUG: getCommitMessages (repository=ll/testrenovate)
DEBUG: semanticCommits: detected "unknown" (repository=ll/testrenovate)
DEBUG: semanticCommits: disabled (repository=ll/testrenovate)
DEBUG: 2 flattened updates found: lavitto/typo3-form-to-database, typo3/cms-core (repository=ll/testrenovate)
DEBUG: Returning 1 branch(es) (repository=ll/testrenovate)
DEBUG: config.repoIsOnboarded=true (repository=ll/testrenovate)
DEBUG: packageFiles with updates (repository=ll/testrenovate, baseBranch=main)
       "config": {
         "composer": [
           {
             "deps": [
               {
                 "depType": "require",
                 "depName": "lavitto/typo3-form-to-database",
                 "currentValue": "3.0.0",
                 "datasource": "packagist",
                 "updates": [
                   {
                     "bucket": "non-major",
                     "newVersion": "3.0.1",
                     "newValue": "3.0.1",
                     "releaseTimestamp": "2023-08-11T08:04:31.000Z",
                     "newMajor": 3,
                     "newMinor": 0,
                     "updateType": "patch",
                     "branchName": "renovate/composer-patch"
                   }
                 ],
                 "packageName": "lavitto/typo3-form-to-database",
                 "versioning": "composer",
                 "warnings": [],
                 "sourceUrl": "https://gitlab.com/lavitto/typo3-form-to-database",
                 "registryUrl": "https://packagist.org",
                 "homepage": "https://www.lavitto.ch/typo3-ext-form-to-database",
                 "currentVersion": "3.0.0",
                 "isSingleVersion": true,
                 "fixedVersion": "3.0.0"
               },
               {
                 "depType": "require",
                 "depName": "typo3/cms-core",
                 "currentValue": "11.5.29",
                 "datasource": "packagist",
                 "updates": [
                   {
                     "bucket": "non-major",
                     "newVersion": "11.5.30",
                     "newValue": "11.5.30",
                     "releaseTimestamp": "2023-07-25T08:27:09.000Z",
                     "newMajor": 11,
                     "newMinor": 5,
                     "updateType": "patch",
                     "branchName": "renovate/composer-patch"
                   },
                   {
                     "bucket": "major",
                     "newVersion": "12.4.5",
                     "newValue": "12.4.5",
                     "releaseTimestamp": "2023-08-08T06:25:16.000Z",
                     "newMajor": 12,
                     "newMinor": 4,
                     "updateType": "major",
                     "branchName": "renovate/typo3-cms-core-12.x"
                   }
                 ],
                 "packageName": "typo3/cms-core",
                 "versioning": "composer",
                 "warnings": [],
                 "sourceUrl": "https://github.com/TYPO3-CMS/core",
                 "registryUrl": "https://packagist.org",
                 "homepage": "https://typo3.org",
                 "currentVersion": "11.5.29",
                 "isSingleVersion": true,
                 "fixedVersion": "11.5.29"
               }
             ],
             "managerData": {"composerJsonType": "library"},
             "packageFile": "app/package/composer.json"
           },
           {
             "deps": [
               {
                 "depType": "require",
                 "depName": "app/package",
                 "currentValue": "dev-main",
                 "datasource": "packagist",
                 "updates": [],
                 "packageName": "app/package",
                 "versioning": "composer",
                 "warnings": [],
                 "skipReason": "invalid-value"
               }
             ],
             "managerData": {"composerJsonType": "project"},
             "lockFiles": ["composer.lock"],
             "packageFile": "composer.json"
           }
         ]
       }
DEBUG: detectSemanticCommits() (repository=ll/testrenovate)
DEBUG: semanticCommits: returning "disabled" from cache (repository=ll/testrenovate)
DEBUG: processRepo() (repository=ll/testrenovate)
DEBUG: Processing 1 branch: renovate/composer-patch (repository=ll/testrenovate)
DEBUG: Calculating hourly PRs remaining (repository=ll/testrenovate)
DEBUG: currentHourStart=2023-08-29T08:00:00.000+01:00 (repository=ll/testrenovate)
DEBUG: PR hourly limit remaining: 2 (repository=ll/testrenovate)
DEBUG: Calculating prConcurrentLimit (10) (repository=ll/testrenovate)
DEBUG: getBranchPr(renovate/composer-patch) (repository=ll/testrenovate)
DEBUG: findPr(renovate/composer-patch, undefined, open) (repository=ll/testrenovate)
DEBUG: getPr(3) (repository=ll/testrenovate)
DEBUG: getMR(3) (repository=ll/testrenovate)
DEBUG: 1 PRs are currently open (repository=ll/testrenovate)
DEBUG: PR concurrent limit remaining: 9 (repository=ll/testrenovate)
DEBUG: Calculated maximum PRs remaining this run: 2 (repository=ll/testrenovate)
DEBUG: PullRequests limit = 2 (repository=ll/testrenovate)
DEBUG: Calculating hourly PRs remaining (repository=ll/testrenovate)
DEBUG: currentHourStart=2023-08-29T08:00:00.000+01:00 (repository=ll/testrenovate)
DEBUG: PR hourly limit remaining: 2 (repository=ll/testrenovate)
DEBUG: Calculating branchConcurrentLimit (10) (repository=ll/testrenovate)
DEBUG: 1 already existing branches found: renovate/composer-patch (repository=ll/testrenovate)
DEBUG: Branch concurrent limit remaining: 9 (repository=ll/testrenovate)
DEBUG: Calculated maximum branches remaining this run: 2 (repository=ll/testrenovate)
DEBUG: Branches limit = 2 (repository=ll/testrenovate)
DEBUG: syncBranchState() (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: syncBranchState(): Branch cache not found, creating minimal branchState (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: getBranchPr(renovate/composer-patch) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: findPr(renovate/composer-patch, undefined, open) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: getPr(3) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: getMR(3) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: branchExists=true (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: dependencyDashboardCheck=undefined (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Manual rebase requested via PR checkbox for #3 (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: PR rebase requested=true (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Checking if PR has been edited (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: branch.isModified(): using git to calculate (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: branch.isModified() = false (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Found existing branch PR (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Checking schedule(at any time, null) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: No schedule defined (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: User has requested rebase (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Using reuseExistingBranch: false (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Setting current branch to main (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: latest commit (repository=ll/testrenovate, branch=renovate/composer-patch)
       "branchName": "main",
       "latestCommitDate": "2023-08-25T16:04:34+01:00"
DEBUG: manager.getUpdatedPackageFiles() reuseExistingBranch=false (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Starting search at index 117 (repository=ll/testrenovate, packageFile=app/package/composer.json, branch=renovate/composer-patch)
       "depName": "lavitto/typo3-form-to-database"
DEBUG: Found match at index 117 (repository=ll/testrenovate, packageFile=app/package/composer.json, branch=renovate/composer-patch)
       "depName": "lavitto/typo3-form-to-database"
DEBUG: Contents updated (repository=ll/testrenovate, packageFile=app/package/composer.json, branch=renovate/composer-patch)
       "depName": "lavitto/typo3-form-to-database"
DEBUG: Value is not updated (repository=ll/testrenovate, packageFile=app/package/composer.json, branch=renovate/composer-patch)
       "manager": "composer",
       "expectedValue": "11.5.30",
       "foundValue": "11.5.29"
DEBUG: Starting search at index 146 (repository=ll/testrenovate, packageFile=app/package/composer.json, branch=renovate/composer-patch)
       "depName": "typo3/cms-core"
DEBUG: Found match at index 146 (repository=ll/testrenovate, packageFile=app/package/composer.json, branch=renovate/composer-patch)
       "depName": "typo3/cms-core"
DEBUG: Contents updated (repository=ll/testrenovate, packageFile=app/package/composer.json, branch=renovate/composer-patch)
       "depName": "typo3/cms-core"
DEBUG: composer.updateArtifacts(app/package/composer.json) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Composer: unable to read lockfile (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Updated 1 package files (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: No updated lock files in branch (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Setting CONTAINERBASE_CACHE_DIR to /var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/cache/containerbase (repository=ll/testrenovate, branch=renovate/composer-patch)
       "dep": "lavitto/typo3-form-to-database"
DEBUG: Falling back to binarySource=global (repository=ll/testrenovate, branch=renovate/composer-patch)
       "dep": "lavitto/typo3-form-to-database"
DEBUG: Executing command (repository=ll/testrenovate, branch=renovate/composer-patch)
       "dep": "lavitto/typo3-form-to-database",
       "command": "composer update lavitto/typo3-form-to-database:3.0.1 --no-scripts --no-progress --no-interaction"
DEBUG: rawExec err (repository=ll/testrenovate, branch=renovate/composer-patch)
       "dep": "lavitto/typo3-form-to-database",
       "err": {
         "cmd": "/bin/sh -c composer update lavitto/typo3-form-to-database:3.0.1 --no-scripts --no-progress --no-interaction",
         "stderr": "Loading composer repositories with package information\nUpdating dependencies\nYour requirements could not be resolved to an installable set of packages.\n\n  Problem 1\n    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].\n    - app/package dev-main requires typo3/cms-core 11.5.30 -> found typo3/cms-core[v11.5.30] but the package is fixed to v11.5.29 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.\n\nUse the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.\n",
         "stdout": "",
         "options": {
           "cwd": "/var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/repos/gitlab/ll/testrenovate",
           "encoding": "utf-8",
           "env": {
             "HOME": "/Users/mike",
             "PATH": "/Users/mike/Sites/renovate/node_modules/.bin:/Users/mike/Sites/node_modules/.bin:/Users/mike/node_modules/.bin:/Users/node_modules/.bin:/node_modules/.bin:/opt/homebrew/lib/node_modules/npm/node_modules/@npmcli/run-script/lib/node-gyp-bin:/Users/mike/.local/bin:/Users/mike/.docker/bin:/opt/homebrew/opt/php@7.4/sbin:/opt/homebrew/opt/php@7.4/bin:/opt/homebrew/opt/php@7.4/sbin:/opt/homebrew/opt/php@7.4/bin:/Users/mike/.orbstack/bin:/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/System/Cryptexes/App/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/local/bin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/bin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/appleinternal/bin:/Users/mike/.local/bin:/Users/mike/.docker/bin:/opt/homebrew/opt/php@7.4/sbin:/opt/homebrew/opt/php@7.4/bin:/Users/mike/.orbstack/bin:/opt/homebrew/bin:/opt/homebrew/sbin",
             "LANG": "en_US.UTF-8",
             "CONTAINERBASE_CACHE_DIR": "/var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/cache/containerbase"
           },
           "maxBuffer": 10485760,
           "timeout": 900000
         },
         "exitCode": 2,
         "name": "ExecError",
         "message": "Command failed: composer update lavitto/typo3-form-to-database:3.0.1 --no-scripts --no-progress --no-interaction\nLoading composer repositories with package information\nUpdating dependencies\nYour requirements could not be resolved to an installable set of packages.\n\n  Problem 1\n    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].\n    - app/package dev-main requires typo3/cms-core 11.5.30 -> found typo3/cms-core[v11.5.30] but the package is fixed to v11.5.29 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.\n\nUse the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.\n",
         "stack": "ExecError: Command failed: composer update lavitto/typo3-form-to-database:3.0.1 --no-scripts --no-progress --no-interaction\nLoading composer repositories with package information\nUpdating dependencies\nYour requirements could not be resolved to an installable set of packages.\n\n  Problem 1\n    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].\n    - app/package dev-main requires typo3/cms-core 11.5.30 -> found typo3/cms-core[v11.5.30] but the package is fixed to v11.5.29 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.\n\nUse the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.\n\n    at ChildProcess.<anonymous> (/Users/mike/Sites/renovate/node_modules/renovate/lib/util/exec/common.ts:99:11)\n    at ChildProcess.emit (node:events:523:35)\n    at ChildProcess.emit (node:domain:489:12)\n    at Process.ChildProcess._handle.onexit (node:internal/child_process:293:12)"
       },
       "durationMs": 393
DEBUG: Setting CONTAINERBASE_CACHE_DIR to /var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/cache/containerbase (repository=ll/testrenovate, branch=renovate/composer-patch)
       "dep": "typo3/cms-core"
DEBUG: Falling back to binarySource=global (repository=ll/testrenovate, branch=renovate/composer-patch)
       "dep": "typo3/cms-core"
DEBUG: Executing command (repository=ll/testrenovate, branch=renovate/composer-patch)
       "dep": "typo3/cms-core",
       "command": "composer update typo3/cms-core:11.5.30 --no-scripts --no-progress --no-interaction"
DEBUG: rawExec err (repository=ll/testrenovate, branch=renovate/composer-patch)
       "dep": "typo3/cms-core",
       "err": {
         "cmd": "/bin/sh -c composer update typo3/cms-core:11.5.30 --no-scripts --no-progress --no-interaction",
         "stderr": "Loading composer repositories with package information\nUpdating dependencies\nYour requirements could not be resolved to an installable set of packages.\n\n  Problem 1\n    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].\n    - app/package dev-main requires lavitto/typo3-form-to-database 3.0.1 -> found lavitto/typo3-form-to-database[3.0.1] but the package is fixed to 3.0.0 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.\n\nUse the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.\n",
         "stdout": "",
         "options": {
           "cwd": "/var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/repos/gitlab/ll/testrenovate",
           "encoding": "utf-8",
           "env": {
             "HOME": "/Users/mike",
             "PATH": "/Users/mike/Sites/renovate/node_modules/.bin:/Users/mike/Sites/node_modules/.bin:/Users/mike/node_modules/.bin:/Users/node_modules/.bin:/node_modules/.bin:/opt/homebrew/lib/node_modules/npm/node_modules/@npmcli/run-script/lib/node-gyp-bin:/Users/mike/.local/bin:/Users/mike/.docker/bin:/opt/homebrew/opt/php@7.4/sbin:/opt/homebrew/opt/php@7.4/bin:/opt/homebrew/opt/php@7.4/sbin:/opt/homebrew/opt/php@7.4/bin:/Users/mike/.orbstack/bin:/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/System/Cryptexes/App/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/local/bin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/bin:/var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/appleinternal/bin:/Users/mike/.local/bin:/Users/mike/.docker/bin:/opt/homebrew/opt/php@7.4/sbin:/opt/homebrew/opt/php@7.4/bin:/Users/mike/.orbstack/bin:/opt/homebrew/bin:/opt/homebrew/sbin",
             "LANG": "en_US.UTF-8",
             "CONTAINERBASE_CACHE_DIR": "/var/folders/1k/84znrvz53j1g1h68ctbbkvpr0000gn/T/renovate/cache/containerbase"
           },
           "maxBuffer": 10485760,
           "timeout": 900000
         },
         "exitCode": 2,
         "name": "ExecError",
         "message": "Command failed: composer update typo3/cms-core:11.5.30 --no-scripts --no-progress --no-interaction\nLoading composer repositories with package information\nUpdating dependencies\nYour requirements could not be resolved to an installable set of packages.\n\n  Problem 1\n    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].\n    - app/package dev-main requires lavitto/typo3-form-to-database 3.0.1 -> found lavitto/typo3-form-to-database[3.0.1] but the package is fixed to 3.0.0 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.\n\nUse the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.\n",
         "stack": "ExecError: Command failed: composer update typo3/cms-core:11.5.30 --no-scripts --no-progress --no-interaction\nLoading composer repositories with package information\nUpdating dependencies\nYour requirements could not be resolved to an installable set of packages.\n\n  Problem 1\n    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].\n    - app/package dev-main requires lavitto/typo3-form-to-database 3.0.1 -> found lavitto/typo3-form-to-database[3.0.1] but the package is fixed to 3.0.0 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.\n\nUse the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.\n\n    at ChildProcess.<anonymous> (/Users/mike/Sites/renovate/node_modules/renovate/lib/util/exec/common.ts:99:11)\n    at ChildProcess.emit (node:events:523:35)\n    at ChildProcess.emit (node:domain:489:12)\n    at Process.ChildProcess._handle.onexit (node:internal/child_process:293:12)"
       },
       "durationMs": 317
DEBUG: Branch timestamp: 2023-08-11T08:04:31.000Z (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: PR is older than 2 hours, raise PR with lock file errors (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: isBranchConflicted(main, renovate/composer-patch) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: branch.isConflicted(): using git to calculate (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Setting git author name: 🤖 RenovateBot (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Setting git author email: email@email.com (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: branch.isConflicted(): false (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: 1 file(s) to commit (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Preparing files for committing to branch renovate/composer-patch (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: git commit (repository=ll/testrenovate, branch=renovate/composer-patch)
       "deletedFiles": [],
       "ignoredFiles": [],
       "result": {
         "author": null,
         "branch": "renovate/composer-patch",
         "commit": "6e67dab7b89b0e4093c42597ff2d8f6ab07a46d7",
         "root": false,
         "summary": {"changes": 1, "insertions": 2, "deletions": 2}
       }
DEBUG: Pushing refSpec renovate/composer-patch:renovate/composer-patch (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: git push (repository=ll/testrenovate, branch=renovate/composer-patch)
       "result": {
         "pushed": [],
         "ref": {"local": "refs/remotes/origin/renovate/composer-patch"},
         "remoteMessages": {
           "all": [
             "View merge request for renovate/composer-patch:",
             "https://hosted.gitlab.com/ll/testrenovate/-/merge_requests/3"
           ]
         }
       }
DEBUG: Setting current branch to main (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: latest commit (repository=ll/testrenovate, branch=renovate/composer-patch)
       "branchName": "main",
       "latestCommitDate": "2023-08-25T16:04:34+01:00"
 INFO: Branch updated (repository=ll/testrenovate, branch=renovate/composer-patch)
       "commitSha": "6e67dab7b89b0e4093c42597ff2d8f6ab07a46d7"
DEBUG: Got res with 0 results (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Updating status check state to failed (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Ensuring PR (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: There are 0 errors and 0 warnings (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: getBranchPr(renovate/composer-patch) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: findPr(renovate/composer-patch, undefined, open) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: getPr(3) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: getMR(3) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: getPrCache() (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Found existing PR (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: PR rebase requested, so skipping cache check (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Forcing PR because of artifact errors (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Fetching changelog: https://gitlab.com/lavitto/typo3-form-to-database (3.0.0 -> 3.0.1) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Fetching changelog: https://github.com/TYPO3-CMS/core (11.5.29 -> 11.5.30) (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Processing existing PR (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: PR title changed (repository=ll/testrenovate, branch=renovate/composer-patch)
       "branchName": "renovate/composer-patch",
       "oldPrTitle": "build(composer): update composer dependencies [patch]",
       "newPrTitle": "Update Composer dependencies"
 INFO: PR updated (repository=ll/testrenovate, branch=renovate/composer-patch)
       "pr": 3,
       "prTitle": "Update Composer dependencies"
DEBUG: setPrCache() (repository=ll/testrenovate, branch=renovate/composer-patch)
 WARN: artifactErrors (repository=ll/testrenovate, branch=renovate/composer-patch)
       "artifactErrors": [
         {
           "lockFile": "app/package/composer.json",
           "stderr": "Command failed: composer update lavitto/typo3-form-to-database:3.0.1 --no-scripts --no-progress --no-interaction\nLoading composer repositories with package information\nUpdating dependencies\nYour requirements could not be resolved to an installable set of packages.\n\n  Problem 1\n    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].\n    - app/package dev-main requires typo3/cms-core 11.5.30 -> found typo3/cms-core[v11.5.30] but the package is fixed to v11.5.29 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.\n\nUse the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.\n"
         },
         {
           "lockFile": "app/package/composer.json",
           "stderr": "Command failed: composer update typo3/cms-core:11.5.30 --no-scripts --no-progress --no-interaction\nLoading composer repositories with package information\nUpdating dependencies\nYour requirements could not be resolved to an installable set of packages.\n\n  Problem 1\n    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].\n    - app/package dev-main requires lavitto/typo3-form-to-database 3.0.1 -> found lavitto/typo3-form-to-database[3.0.1] but the package is fixed to 3.0.0 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.\n\nUse the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.\n"
         }
       ]
DEBUG: Getting comments for #3 (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Found 11 comments (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Ensuring comment "⚠ Artifact update problem" in #3 (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: Updated comment (repository=ll%2Ftestrenovate, branch=renovate/composer-patch)
       "issueNo": 3
DEBUG: setBranchCommit() (repository=ll/testrenovate, branch=renovate/composer-patch)
DEBUG: getBranchPr(renovate/composer-patch) (repository=ll/testrenovate)
DEBUG: findPr(renovate/composer-patch, undefined, open) (repository=ll/testrenovate)
DEBUG: getPr(3) (repository=ll/testrenovate)
DEBUG: getMR(3) (repository=ll/testrenovate)
DEBUG: getPrCache() (repository=ll/testrenovate)
DEBUG: ensureDependencyDashboard() (repository=ll/testrenovate)
DEBUG: Closing Dependency Dashboard (repository=ll/testrenovate)
DEBUG: ensureIssueClosing() (repository=ll/testrenovate)
DEBUG: Closing issue (repository=ll/testrenovate)
       "issue": {"iid": 2, "title": "Dependency Dashboard", "labels": ["bot"]}
DEBUG: Removing any stale branches (repository=ll/testrenovate)
DEBUG: config.repoIsOnboarded=true (repository=ll/testrenovate)
DEBUG: Branch lists (repository=ll/testrenovate)
       "branchList": ["renovate/composer-patch"],
       "renovateBranches": ["renovate/composer-patch"]
DEBUG: remainingBranches= (repository=ll/testrenovate)
DEBUG: No branches to clean up (repository=ll/testrenovate)
DEBUG: ensureIssueClosing() (repository=ll/testrenovate)
DEBUG: ensureIssueClosing() (repository=ll/testrenovate)
DEBUG: PackageFiles.clear() - Package files deleted (repository=ll/testrenovate)
DEBUG: Branch summary (repository=ll/testrenovate)
       "cacheModified": undefined,
       "baseBranches": [{"branchName": "main", "sha": "96e6f2432982782a78fd459dbdf1e4a1a7329bbf"}],
       "branches": [
         {
           "automerge": false,
           "baseBranch": "main",
           "baseBranchSha": "96e6f2432982782a78fd459dbdf1e4a1a7329bbf",
           "branchName": "renovate/composer-patch",
           "branchSha": "6e67dab7b89b0e4093c42597ff2d8f6ab07a46d7",
           "isModified": false,
           "isPristine": true
         }
       ],
       "defaultBranch": "main",
       "inactiveBranches": []
DEBUG: branches info extended (repository=ll/testrenovate)
       "branchesInformation": [
         {
           "branchName": "renovate/composer-patch",
           "prNo": 3,
           "prTitle": "Update Composer dependencies",
           "result": "done",
           "upgrades": [
             {
               "datasource": "packagist",
               "depName": "lavitto/typo3-form-to-database",
               "displayPending": "",
               "fixedVersion": "3.0.0",
               "currentVersion": "3.0.0",
               "currentValue": "3.0.0",
               "newValue": "3.0.1",
               "newVersion": "3.0.1",
               "packageFile": "app/package/composer.json",
               "updateType": "patch",
               "packageName": "lavitto/typo3-form-to-database"
             },
             {
               "datasource": "packagist",
               "depName": "typo3/cms-core",
               "displayPending": "",
               "fixedVersion": "11.5.29",
               "currentVersion": "11.5.29",
               "currentValue": "11.5.29",
               "newValue": "11.5.30",
               "newVersion": "11.5.30",
               "packageFile": "app/package/composer.json",
               "updateType": "patch",
               "packageName": "typo3/cms-core"
             }
           ]
         }
       ]
DEBUG: Renovate repository PR statistics (repository=ll/testrenovate)
       "stats": {"total": 3, "open": 1, "closed": 2, "merged": 0}
DEBUG: Repository result: done, status: onboarded, enabled: true, onboarded: true (repository=ll/testrenovate)
DEBUG: Repository timing splits (milliseconds) (repository=ll/testrenovate)
       "splits": {"init": 2311, "extract": 64, "lookup": 336, "onboarding": 0, "update": 4891},
       "total": 7796
DEBUG: Package cache statistics (repository=ll/testrenovate)
       "get": {"count": 7, "avgMs": 6, "medianMs": 4, "maxMs": 9},
       "set": {"count": 2, "avgMs": 23, "medianMs": 16, "maxMs": 30}
DEBUG: http statistics (repository=ll/testrenovate)
       "urls": {
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/issues (GET,200)": 1,
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/issues/2 (PUT,200)": 1,
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/merge_requests (GET,200)": 1,
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/merge_requests/3 (GET,200)": 1,
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/merge_requests/3 (PUT,200)": 1,
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/merge_requests/3/notes (GET,200)": 1,
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/merge_requests/3/notes/14887 (PUT,200)": 1,
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/repository/commits/6e67dab7b89b0e4093c42597ff2d8f6ab07a46d7/statuses (GET,200)": 2,
         "https://hosted.gitlab.com/api/v4/projects/ll%2Ftestrenovate/statuses/6e67dab7b89b0e4093c42597ff2d8f6ab07a46d7 (POST,201)": 1,
         "https://packagist.org/p2/lavitto/typo3-form-to-database.json (GET,200)": 1,
         "https://packagist.org/p2/lavitto/typo3-form-to-database~dev.json (GET,200)": 1,
         "https://packagist.org/p2/typo3/cms-core.json (GET,200)": 1,
         "https://packagist.org/p2/typo3/cms-core~dev.json (GET,200)": 1
       },
       "hostStats": {
         "gitlab.lldev.co.uk": {
           "requestCount": 10,
           "requestAvgMs": 165,
           "queueAvgMs": 0
         },
         "packagist.org": {"requestCount": 4, "requestAvgMs": 187, "queueAvgMs": 0}
       },
       "totalRequests": 14
DEBUG: Package lookup durations (repository=ll/testrenovate)
       "packagist": {"count": 3, "averageMs": 175, "totalMs": 526, "maximumMs": 295}
DEBUG: dns cache (repository=ll/testrenovate)
       "hosts": []
 INFO: Repository finished (repository=ll/testrenovate)
       "cloned": true,
       "durationMs": 7796
DEBUG: Checking file package cache for expired items
DEBUG: Deleted 0 of 8 file cached entries in 9ms
DEBUG: Renovate exiting
```

</details>


## Config

Renovate is run via NPM and Docker with the following config:

```js
module.exports = {

	/**
	 * Base Config Extensions
	 */
	extends: [
		// https://docs.renovatebot.com/presets-default/#ignoremodulesandtests
		':ignoreModulesAndTests',
		// https://docs.renovatebot.com/presets-group/#groupmonorepos
		'group:monorepos',
		// https://docs.renovatebot.com/presets-group/#grouprecommended
		'group:recommended',
		// https://docs.renovatebot.com/presets-workarounds/#workaroundsall
		'workarounds:all'
	],

	major: {
		enabled: false
	},

	/**
	 * Post Upgrade
	 */
	// What post upgrade commands are allowed
	allowedPostUpgradeCommands: [
		"composer update {{{depName}}}:{{{newVersion}}} --no-scripts --no-progress --no-interaction"
	],

	/**
	 * Package Rules
	 */
	packageRules: [
		/**
		 * Composer packages
		 */
		{
			groupName: 'Composer dependencies',
			matchManagers: ['composer'],
			matchUpdateTypes: ['minor', 'patch'],
			addLabels: ['composer'],
			semanticCommitScope: 'composer',
			postUpgradeTasks: {
				commands: [
					"composer update {{{depName}}}:{{{newVersion}}} --no-scripts --no-progress --no-interaction"
				],
				fileFilters: ["composer.lock"],
				executionMode: "update"
			}
		},
		{
			matchManagers: ['composer'],
			matchUpdateTypes: ['patch'],
			groupSlug: 'composer-patch'
		},
		{
			matchManagers: ['composer'],
			matchUpdateTypes: ['minor'],
			groupSlug: 'composer-minor'
		},


	],

	/**
	 * Managers
	 */
	// What dependencies are we updating?
	enabledManagers: [
		'composer',
	],

};
```
