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


### To make changes:

If you need to update the config-repo, then you can just edit the JSON/YAML files in the config-repo directory. However, next time someone checks out the trial, the pipelines will build. So, it's ideal to follow this method:

##### Step 1: Make your changes in `config-repo/` directory

##### Step 2: Build locally

```
bundle install
./build
```

##### Step 3: (Optional) Go to the `output/` directory and remove old DB and artifacts

By removing this, you will start from pipeline counter 1. If you don't mind adding more pipeline counters, skip this step.

```
cd output/
rm -rf data/{db,artifacts}/
```

##### Step 4: Bring up the instances

```
cd output/
docker-compose up -d
```

Wait for the pipeline runs to finish.


##### Step 5: Update the checked in DB and artifacts

Run this outside of the `output/` directory. In the base directory which contains this README file.

```
rm -rf template/data/{db,artifacts}/
mv output/data/{db,artifacts} template/data/
```

##### Step 6: Commit your changes

```
$ git status
On branch cleanup-and-prepare-trial
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   template/data/artifacts/pipelines/acceptance_tests/1/acceptance_tests_build_stage/1/acceptance_tests_build_job/cruise-output/console.log
    modified:   template/data/artifacts/pipelines/build_test/1/build_test_build_stage/1/build_test_job/cruise-output/console.log
    modified:   template/data/artifacts/pipelines/deploy/1/ready_to_deploy_stage/1/ready_to_deploy_job/cruise-output/console.log
    modified:   template/data/artifacts/pipelines/performance_tests/1/performance_tests_build_stage/1/performance_tests_build_job/cruise-output/console.log
    modified:   template/data/db/h2db/cruise.h2.db

no changes added to commit (use "git add" and/or "git commit -a")

$ git add template/data

$ git commit -m "Updated JSON files and DB"
```

##### Step 7: (Optional) Verify your changes

```
bundle install
./build
cd output/
docker-compose up -d
```

You should not see any pipelines building, and you should see the changes you made to the JSON files show up.

Do not copy over and check in the cruise-config.xml file, unless you really want to. It would have changed, due to agent registration.

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
