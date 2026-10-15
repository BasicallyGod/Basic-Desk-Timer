# Branches and builds

Two long-lived branches keep everyday testing and the tested release line apart. Risky experiments live on a `feature/...` branch until they are ready.

| Branch | Purpose | Automatic artifact | Data |
| --- | --- | --- | --- |
| `main` | The tested release line and the repository default. Only changes that have been tried on `experimental` arrive here, by pull request. | `BasicsDeskTimer-Windows-main` (portable ZIP) | `test-install/channels/main/data` |
| `experimental` | Everyday feature and regression testing. | `BasicsDeskTimer-Windows-experimental` (portable ZIP) | `test-install/channels/experimental/data` |

There is no Store build yet. `microsoft-store-msix.yml` runs only when started by hand, and the packaging files are kept so a Store release can be switched on later.

## Daily workflow

1. Develop on a `feature/...` branch from `experimental`, or on `experimental` itself. Push changes; the tests and native smoke tests run. Pick the channel with **Timer Updater.cmd**. Never test a `main` database against the `experimental` build, or the other way round.
2. Every merge into `experimental` or `main` builds a portable ZIP, so gather related changes on one `feature/...` branch and merge them together instead of opening a pull request for each. Builds are kept for 14 days and rendered previews are kept only when a test fails.
3. When `experimental` has been tested, open a pull request from `experimental` into `main`, review the diff and require successful checks.
4. The shared engine is a separate package. A change to tracking or storage is made in `BasicallyGod/Basic-Timer-Core`, released with a tag, and taken here by raising `SharedTimerVersion` in `migration/Directory.Build.props`. A storage change raises the database format, which is one way: an older build refuses a newer file.

## Safety

- Configure `main` protections under **Settings → Branches / Rulesets**: require passing checks, disallow force pushes and deletion, and merge by pull request.
- Back up stored data before changing database schemas; rollback is not a schema downgrade.
- This app and Basic's Advanced Timer keep separate data and never open each other's files.
