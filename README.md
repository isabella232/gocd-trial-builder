# GoCD Trial - Build scripts

Scripts used to build the contents of the [GoCD Trial repository](https://github.com/gocd/gocd-trial). The scripts in the repository make sure that no secrets are checked in to the trial repository, its configuration, etc. It also does some other housekeeping.

## Usage

### To build locally:

```
bundle install
./build
```

This creates a directory called `output` which brings together the `template` and `config-repo` directories into the format needed to push to the GoCD Trial repository. It also converts the contents of the `config-repo` directory into a bare Git repository, so that it can be used during the trial.


### To build and push changes up to the GoCD Trial repository:

```
bundle install
SSH_KEY_CONTENTS=... ./build push_changes
```

By providing the _contents_ of the SSH private key and calling the `push_changes` task, the `output` directory will again be generated and it will be pushed into the GoCD Trial repository as a new commit.

The SSH private key needs to be a [GitHub deploy key](https://developer.github.com/v3/guides/managing-deploy-keys/#deploy-keys) on the GoCD Trial repository.


# License

```plain
Copyright 2018, ThoughtWorks, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
