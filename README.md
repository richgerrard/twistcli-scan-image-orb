# twistcli-scan-image-orb for CircleCI

## What is this?
With this orb you can easily integrate Twistlock vulnerability and compliance issue scans into your CircleCI Workflows.
You can set thresholds for vulnerabilities and compliance issues and fail builds that exceed them. You can also skip failing
on vulnerabilities unless they have a vendor fix version.

Note: Twistlock is commercial software, so you'll need a license before using this orb. https://www.twistlock.com

## How to get started

### Big idea: If you follow this approach, all you really need is the boilerplate config.yml and your `image` name
Reference docs here: https://circleci.com/orbs/registry/orb/twistlock/twistcli-scan


### 1. Create a "CI User" in Twistlock and note the user name and password you use. Also note down the URL to the Twistlock Console including port.

<p align="left">
  <img alt="Create a context" src="https://github.com/add-twistlock/twistcli-scan-image-orb/blob/master/images/ci_user.png">
</p>

### 2. Create a new Context in the CircleCI app. Use `tl_scan_context` to align with the example in `config.yml`

<p align="left">
  <img alt="Create a context" src="https://github.com/add-twistlock/twistcli-scan-image-orb/blob/master/images/context.png">
</p>

### 3. Add environment variables for `TL_USER`, `TL_PASS`, `TL_CONSOLE_URL` as noted in step 1.

<p align="left">
  <img alt="Create a context" src="https://github.com/add-twistlock/twistcli-scan-image-orb/blob/master/images/vars.png">
</p>

### 4. Adapt the example config.yml to your environment

leave this boilerplate to use the orb, use the orb's executor and its directory,
and checkout your repo
```
version: 2.1

orbs:
  twistcli: twistlock/twistcli-scan@1.0.3

jobs:
  docker-build-and-save:
    executor: twistcli/default
    steps:
      - checkout
```

**1. change** the image name here to your image

```
      - run: docker build -t myrepo/myimage:tag .
```

leave this to use the default Workspace name where we will store the docker image tar before scanning

```
      - run: mkdir -p workspace
```

**2. change** the image name here to your image

```
      - run: docker save myrepo/myimage:tag -o workspace/image.tar
```

leave this boilerplate to save the image tar with its default name to the workspace

```
      - persist_to_workspace:
          root: workspace
          paths:
              - image.tar
```

leave this Workflow boilerplate to invoke the job above and run the twistcli/scan-image job in the orb
using the default Context with your credentials in it

```
workflows:
  scan-image:
    jobs:
      - docker-build-and-save
      - twistcli/scan-image:
          requires:
            - docker-build-and-save
          context: tl_scan_context
          image-tar: image.tar
```

**3. change** the image name here to your image

```
          image: myrepo/myimage:tag
```

**optional:** add/modify any desired options for `vuln-thresh`, `comp-thresh`, `only-fixed`

```
          vuln-thresh: high
          comp-thresh: critical
``` 
Reference docs here: https://circleci.com/orbs/registry/orb/twistlock/twistcli-scan

### 5a. See the results in CircleCI

<p align="left">
  <img alt="Create a context" src="https://github.com/add-twistlock/twistcli-scan-image-orb/blob/master/images/circleci_workflows.png">
</p>

<p align="left">
  <img alt="Create a context" src="https://github.com/add-twistlock/twistcli-scan-image-orb/blob/master/images/circleci_job_details.png">
</p>

### 5b. See the results in the Twistlock Console

<p align="left">
  <img alt="Create a context" src="https://github.com/add-twistlock/twistcli-scan-image-orb/blob/master/images/twistcli_scans.png">
</p>

