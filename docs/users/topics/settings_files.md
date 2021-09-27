## Controlling CMS Settings Files in DDEV

One DDEV feature that lots of people love is its creation and management of CMS-specific settings files. This makes starting and installing a new project a breeze, and is a fantastic time-saver for many users. People can follow one of the many DDEV-Local [Quickstart Guides](https://ddev.readthedocs.io/en/stable/users/cli-usage/#quickstart-guides) and have a project up and installed in no time. To make this happen, DDEV-Local does a quite a bit of settings management for explicitly supported CMSs. DDEV will:

* Create a main settings file if none exists (like Drupal's settings.php).
* Create a specialty config file with DDEV-specific settings (like AdditionalSettings.php for TYPO3 or settings.ddev.php for Drupal).
* Add an include of the specialty file if needed (like adding settings.ddev.php include to the bottom of settings.php for Drupal.

This really helps new users and people who are kicking the tires on a CMS. Plus it's helpful for many developers in their regular workflow. However, there are plenty of you who have sophisticated team and project workflows and don't want DDEV to manage settings files, or prefer to manage your own. DDEV has always tried to give you control as needed:

* If you don't want DDEV to touch a file, remove the `#ddev-generated` line from that file, empty it or put your own contents in it, and check it into version control. DDEV will then ignore that file and not try to regenerate it.
* If you later want DDEV to take that file over again, just remove the one that you edited and `ddev start` and ddev will create its own version. (You may have to remove it from your git project if you added it).
* If you don't want DDEV to even know what kind of CMS (or other project) you have, just use `type: php` in your .ddev/config.yaml (or run `ddev config --project-type=php`). DDEV will no longer create or tweak any settings files, you're now on your own (The one drawback of this approach is that you don't get the nginx configuration which has been tweaked for your CMS. But, as always, you can create your own nginx or apache configurations.) ([docs](https://ddev.readthedocs.io/en/stable/users/extend/customization-extendibility/)).
* If you want DDEV to know about the project type, but not create settings files, use `disable_settings_management: true`. If you want DDEV to use the CMS-specific nginx configuration, but don't want it to touch anything else, you can put `disable_settings_management: true` in your .ddev/config.yaml (or run `ddev config --disable-settings-management`) and DDEV won't try to create any of the CMS-specific settings files.
* There is also an environment variable `$IS_DDEV_PROJECT` that can be used to fence off DDEV-specific behavior. For example, with `$IS_DDEV_PROJECT` empty, the important parts of settings.ddev.php and AdditionalSettings.php (for TYPO3) are not executed. This means that DDEV's settings.ddev.php won't be invoked if it somehow ends up in a production environment or in a non-DDEV local development environment.
* The `.ddev/.gitignore` is created by `ddev start` because it gitignores itself. So the intention is that you should _not_ check in the .ddev/.gitignore and it will be created on start _if_ disable_settings_management is false. This helps teams to share .ddev folder checked in by git, even if the .ddev/.gitignore changes with different versions.