# Patch library

Cross-ROM build fixes, stored as `git format-patch` files so they can be
reapplied to any ROM source tree.

## 0001-Add-bracket-checking-support-to-SQLiteTokenizer.patch

### Why it exists
Some ROMs ship `packages/providers/ContactsProvider` with the AOSP security
commit "Prevent SQL injection in SelectionBuilder" (2026-05-04, Bug 393377017),
which calls `SQLiteTokenizer.OPTION_CHECK_BRACKETS`. If `frameworks/base` has
not been updated with the matching commit, the build fails with:

```
packages/providers/ContactsProvider/src/com/android/providers/contacts/util/SelectionBuilder.java:48: error: cannot find symbol
    SQLiteTokenizer.tokenize(clause, SQLiteTokenizer.OPTION_CHECK_BRACKETS, null);
    symbol:   variable OPTION_CHECK_BRACKETS
```

### Origin
- Upstream: `LineageOS/android_frameworks_base` branch `lineage-23.2`
- Commit: `c08579f5416dbb5990c67b6e9c352cad8de26b14`
  - "Add bracket checking support to SQLiteTokenizer"
  - Author: Duy Truong <duytruong@google.com>, 2026-05-04
  - Change-Id: `I84dd342b7ee2328413c9aef5734d7ffefafa4d85`
  - Files: `core/java/android/database/sqlite/SQLiteTokenizer.java` (+41/-6)
           `core/tests/coretests/src/android/database/sqlite/SQLiteTokenizerTest.java` (+31)

### How to apply to any ROM
From the ROM root:

```
git -C frameworks/base apply --check /home/ikan/patches/0001-Add-bracket-checking-support-to-SQLiteTokenizer.patch
git -C frameworks/base am /home/ikan/patches/0001-Add-bracket-checking-support-to-SQLiteTokenizer.patch
```

`git am` keeps the original author and Change-Id. If you don't want a commit:

```
git -C frameworks/base apply /home/ikan/patches/0001-Add-bracket-checking-support-to-SQLiteTokenizer.patch
```

### How to check whether a ROM needs it
1. Does ContactsProvider use the new API?
   `grep -r OPTION_CHECK_BRACKETS packages/providers/ContactsProvider/src/com/android/providers/contacts/util/SelectionBuilder.java`
2. Does frameworks/base provide it?
   `grep -c OPTION_CHECK_BRACKETS frameworks/base/core/java/android/database/sqlite/SQLiteTokenizer.java`
   (0 = apply the patch; the patch targets exactly the stock 2019 SQLiteTokenizer)

### Add a new patch
```
git -C <clone> format-patch -1 <sha> -o /home/ikan/patches/
```
Then document the origin and failure it fixes in this README.