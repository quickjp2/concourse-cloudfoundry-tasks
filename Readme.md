# Description

This repository containt a set of scripts to be use in [Concourse CI](http://concourse.ci/) pipeline.

## Scripts
* Smoke-test
  [smoke-test](https://github.com/cloudfoundry/cf-smoke-tests)
* Acceptance-test
  [acceptance-test](https://github.com/cloudfoundry/cf-acceptance-tests)
* Upload buildpack
  `BUILDPACK_NAME` is the name of the buildpack in cloudfoundry 
`DOWNLOADED_BUILDPACK_NAME` is the name of the downloaded builpack (prefix of the name ex java-buildpack-offline.zip we will use java-buildpack) by default this value is the same as `BUILPACK_NAME`

* Important if the buildpack is already uploaded we consider the job as passed (Green status). 

```yaml
jobs:
- name: go_buildpack_pcfdev
  public: true
  serial: true
  plan:
  - aggregate:
    - get: buildpack-github-release
      resource: gh-release-go-buildpack
      params:
        globs:
          - "*cached*"
      trigger: true
    - put: env-info
      resource: local-env
      params: {acquire: true}
    - get: concourse-cloudfoundry-tasks
  - task: upload-go-buildpack
    file: concourse-cloudfoundry-tasks/ci/updatebuildpack/updatebuildpack.yml
    params:
      BUILDPACK_NAME: go_buildpack
    on_failure:
      put: env-info
      resource: local-env
      params: {release: env-info}
```


Important
* smoke-test can be run in any environment.
* Acceptance-test are more aggressive and should be run in non-production env.




