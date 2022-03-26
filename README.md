 [![GitHub: fourdollars/http-resource](https://img.shields.io/badge/GitHub-fourdollars%2Fhttp%E2%80%90resource-darkgreen.svg)](https://github.com/fourdollars/http-resource/) [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT) [![Bash](https://img.shields.io/badge/Language-Bash-red.svg)](https://www.gnu.org/software/bash/) ![Docker](https://github.com/fourdollars/http-resource/workflows/Docker/badge.svg) [![Docker Pulls](https://img.shields.io/docker/pulls/fourdollars/http-resource.svg)](https://hub.docker.com/r/fourdollars/http-resource/)
# http-resource
[Concourse CI](https://concourse-ci.org/)'s http-resource to watch and download HTTP URL by curl.

## Config 

### Resource Type

```yaml
resource_types:
- name: http
  type: registry-image
  source:
    repository: fourdollars/http-resource
    tag: latest
```

or

```yaml
resource_types:
- name: http
  type: registry-image
  source:
    repository: ghcr.io/fourdollars/http-resource
    tag: latest
```

### Resource

* url: **Required**
* digest: Optional, check 'etag' in HTTP headers by default.

```yaml
resources:
- name: iso
  icon: ubuntu
  type: http
  check_every: 5m
  source:
    url: https://cdimage.ubuntu.com/daily-live/current/jammy-desktop-amd64.iso
    digest: etag
```

#### get step params

 * download: Optional, true or false. False by default.

### Job Example

```yaml
jobs:
- name: check-isoinfo
  plan:
  - get: iso
    params:
      download: true
    trigger: true
  - task: check
    config:
      platform: linux
      image_resource:
        type: registry-image
        source:
          repository: ubuntu
          tag: latest
      inputs:
        - name: iso
      run:
        path: sh
        args:
        - -exc
        - |
          export DEBIAN_FRONTEND=noninteractive
          apt-get update -q -q
          apt-get install --yes --no-install-recommends genisoimage
          isoinfo -d -i iso/jammy-desktop-amd64.iso
```
