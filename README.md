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

```
Command failed: composer update typo3/cms-core:11.5.30 --no-scripts --no-progress --no-interaction
Loading composer repositories with package information
Updating dependencies
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - Root composer.json requires app/package dev-main -> satisfiable by app/package[dev-main].
    - app/package dev-main requires lavitto/typo3-form-to-database 3.0.1 -> found lavitto/typo3-form-to-database[3.0.1] but the package is fixed to 3.0.0 (lock file version) by a partial update and that version does not match. Make sure you list it as an argument for the update command.

Use the option --with-all-dependencies (-W) to allow upgrades, downgrades and removals for packages currently locked to specific versions.

```

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
