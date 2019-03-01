# docs-push-notifications

# Book repo for publishing this content

https://github.com/pivotal-cf/docs-book-pcfservices

The book repository uses the **master** branch to build all branches. See the pipeline Pipeline [here](https://concourse.run.pivotal.io/teams/cf-docs/pipelines/cf-current?groups=pcfservices).

# How the branches here work

Use **master** for next the unreleased version, and numbered branches for the corresponding live releases.
The latest version on **master** is routed to "/svc-sdk/odb/0-2n". This is to facilitate ease of access, push quicker to production, and reduce the need for large changes to the associated files.

For example:

| Branch name     | Use for|
|-----------------| ------|
| master       | Not in use, but do not delete. |
| v1.10        | live on June 10, 2018 https://docs.pivotal.io/push/1-10/index.html | 
| v1.9         | not in use, docs are no longer live. PDF available at https://docs.pivotal.io/archives/push-notifications-1.9.pdf.|
| v1.8         | not in use, docs are no longer live. PDF available at https://docs.pivotal.io/archives/push-notifications-1.8.pdf.|
| v1.7         | not in use, docs are no longer live. PDF available at https://docs.pivotal.io/archives/push-notifications-1.7.pdf.|
| v1.6         | not in use, docs are no longer live. PDF available at https://docs.pivotal.io/archives/push-notifications-1.6.pdf. |
