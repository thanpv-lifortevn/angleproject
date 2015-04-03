If you want to contribute code to ANGLE, there are a few things you can do to make the process flow smoothly.  (These are based on the [Chromium Projects: Contributing Code](http://www.chromium.org/developers/contributing-code/) guidelines.)

# Communicate #

  * Whether you're writing a new feature or fixing an existing bug, it pays to get a second opinion before you get too far. If it's a new feature idea, post to the discussion group ([angleproject](https://groups.google.com/forum/?fromgroups#!forum/angleproject)) and propose it or talk with the ANGLE team on IRC in the #ANGLEproject channel on FreeNode.
  * Not all bugs in our [bug system](https://code.google.com/p/angleproject/issues/list) are assigned, but if the one you're interested in fixing is, send a note to the person it's assigned to and ask if they would like a patch.
  * Behavior changes and anything nontrivial (i.e. anything other than simple cleanups and style fixes) should generally be tracked in the bug system. Please [file a bug](https://code.google.com/p/angleproject/issues/entry) and describe what you're doing if there isn't one already.

# Get your code ready #
  1. Code
    1. Must conform to the [ANGLE style](CodingStandard.md) guidelines.
    1. Must be tested.
    1. Should be a reasonable size to review.  Giant patches are unlikely to get reviewed quickly.
  1. Build maintenance
    1. **If you added or removed source files**:
      * You _must_ update the gyp build scripts lists with your changes. See `src/libEGL.gypi`, `src/libGLESv2.gypi`, and `src/compiler.gypi`.
    1. ANGLE also now maintains a BUILD.gn script for  [Chromium's gn build](https://code.google.com/p/chromium/wiki/gn). If you changed the gyp files other than to add or remove new files, you will also need to update BUILD.gn.
    1. **If you modified `glslang.y` or `glslang.l`**:
      * You _must_ update the bison-generated compiler sources. Download and install the latest 64-bit Bison in [Cygwin](https://cygwin.com/install.html). From the Cygwin shell run `generate_parser.sh` in `src/compiler/translator` and update your CL.
      * If you modified `ExpressionParser.y` or `Tokenizer.l`, follow the same process by running `src/compiler/preprocessor/generate_parser.sh`.
  1. Testing
    * Your contribution must pass a set of tests in order to be accepted. The required tests for ANGLE are:
      * tests/angle\_tests - These tests must pass with no failures. If you are adding new functionality or addressing a bug that does not have current test coverage, you're strongly advised to add a test for your contribution.
      * The [Top-of-Tree WebGL Conformance](https://www.khronos.org/registry/webgl/sdk/tests/webgl-conformance-tests.html) tests
        * If you are a Chromium developer, see [Building ANGLE for Chromium Development](https://code.google.com/p/angleproject/wiki/BuildingANGLEChromiumForANGLEDevelopment) for instructions on building ANGLE within Chromium
        * If you aren't a browser developer, you should be able to drop your compiled DLLs into a Chrome installation, in place of those distributed with Chrome, to check WebGL conformance.
        * This test suite often includes tests which are known to fail on ANGLE or Chromium. A file documenting known failures is located in the repostitory at [tests/WebGL-CTS-known-failures.txt](https://chromium.googlesource.com/angle/angle/+/master/tests/WebGL-CTS-known-failures.txt). Your code should not fail any tests not documented in this file. If you believe there are failing tests missing from the known failures file, please notify project members.
  1. Legal
    1. You must complete the [Individual Contributor License Agreement](http://code.google.com/legal/individual-cla-v1.0.html). You can do this online, and it only takes a minute. If you are contributing on behalf of a corporation, you must fill out the [Corporate Contributor License Agreement](http://code.google.com/legal/corporate-cla-v1.0.html) and send it to Google as described on that page.
    1. Once you've submitted the CLA, please email the following information (as entered on the CLA) to 'shannonwoods at chromium dot org' for record keeping purposes:
      * Full Name:
      * Email:
      * Company (If applicable):
    1. If you've never submitted code before, you must add your (or your organization's) name and contact info to the [AUTHORS](https://code.google.com/p/angleproject/source/browse/AUTHORS?name=master) file.
    1. **NOTE TO REVIEWERS**: Follow the [External Contributor Checklist](http://www.chromium.org/developers/contributing-code/external-contributor-checklist).

# Life of a Change List #

  * The preferred review process is to use the [Gerrit](http://code.google.com/p/gerrit/) open source code review tool. The process is similar, but not identical, to the one used by ChromiumOS, described here: http://www.chromium.org/chromium-os/developer-guide/gerrit-guide
  * The ANGLE workflow is detailed below.

**Getting started with Gerrit for ANGLE**
  1. Go to https://chromium-review.googlesource.com/new-password
  1. Log in with the email you use for your git commits.
  1. Follow the directions on the new-password page to set up/append to your .netrc (`_netrc` on Windows) file
  1. Make sure to set your real name.
    * Visit https://chromium-review.googlesource.com/#/settings/ and check the "Full Name" field.
  1. Check out the repository (see [DevSetup#Getting\_the\_source](DevSetup#Getting_the_source.md))
  1. Install the necessary git hooks
    * Gerrit requires a hook to append change ID numbers to each commit, so that dependencies between commits may be easily tracked. This hook can be found at https://chromium-review.googlesource.com/tools/hooks/commit-msg
    * Copy this file to `.git/hooks/commit-msg` within your local repository, and ensure that permissions are set to allow execution.
    * **BE AWARE:** Some patch management tools, such as StGit, bypass git hooks, and should not currently be used with changes intended for review.

**Making changes**
  1. Commit your changes locally:
    * `git add src/../FileName.cpp`
    * `git commit`
    * A text editor will open. Add a description at the top of the file.
      * If your changes are associated with an issue in the issue tracker (e.g. a fix for a reported bug), please associate the CL with that issue by adding the following line to the commit message: `BUG=angleproject:<issue number>`
    * Save.
    * Close the text editor.
    * Do this again to change the description or add or remove files.
  1. Upload the change list.
    * `git cl upload`
    * The change list and modified files will be uploaded to Gerrit.
    * Follow the generated URL to the new issue.
    * Take a moment to perform a self-review of your code. Gerrit's viewer makes it easy to see whitespace errors, erroneous tabs, and other simple style problems.
    * Designate a reviewer-- if you don't do this, reviewers may not realize you're requesting a review!
      * If you don't know who to use, select either 'geofflang at chromium dot org' or 'jmadill at chromium dot org'.
    * Make changes, upload and repeat as necessary.
    * Project members and others will review your code as described in the CodeReviewProcess.
  1. If your change list needs revision:
    * If you have correctly installed the commit hook from the section above, Gerrit will be able to track your changes by Change-Id.
    * You should need only to update your commit with `git commit --amend` and re-upload with `git cl upload`.
  1. Once your code is reviewed:
    * If you are a committer, you may submit the change yourself via the Gerrit web interface.
    * If you are not a committer, ask your reviewer to commit the change list.