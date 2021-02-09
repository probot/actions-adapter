# :electric_plug: `@probot/adapter-github-actions`

> Adapter to run a [Probot](https://probot.github.io/) application function in [GitHub Actions](https://github.com/features/actions)

<a href="https://github.com/probot/adapter-github-actions"><img alt="GitHub Actions status" src="https://github.com/probot/adapter-github-actions/workflows/Build/badge.svg"></a>

## Contents

- [Installation](#installation)
- [Usage](#usage)
- [Authentication](#authentication)
- [Caveats](#caveats)

## Installation

```shell
npm install @probot/adapter-github-actions
```

## Usage

1. Add an `action.js` to your Probot project, like [the one shown below](#example-actionjs)
1. Add an `action.yml` to your Probot project, like [the one shown below](#example-actionyml)
1. _Vendor in_ your `node_modules`, as [recommended by the official Actions documentation](https://docs.github.com/en/actions/creating-actions/creating-a-javascript-action#commit-tag-and-push-your-action-to-github)
1. Optional, but recommended, update your project's README with an example workflow showing how to consume your action

### Example `action.js`

```javascript
// Require the adapter
const { run } = require("@probot/adapter-github-actions");

// Require your Probot application function
const app = require("./app");

// Adapt the Probot app for Actions
// This also acts as the main entrypoint for the Action
run(app);
```

### Example `action.yml`

```yaml
name: "Probot app name"
description: "Probot app description."
runs:
  using: "node12"
  main: "action.js"
```

See [the documentation](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions) for `action.yml` syntax details.

## Authentication

Authentication is via [the `GITHUB_TOKEN` secret _automatically_ provided by GitHub](https://docs.github.com/en/actions/configuring-and-managing-workflows/authenticating-with-the-github_token), which should be exposed as an environment variable, `GITHUB_TOKEN`. This can be achieved by including the appropriate [`env`](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#env), [`jobs.<job_id>.env`](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idenv), or [`jobs.<job_id>.steps.env`](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstepsenv) value in your workflow file.

### Example via `jobs.<job_id>.steps.env`

Include the following in your workflow file, when calling your Probot action:

```yaml
...
steps:
  - name: My probot action
    ...
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
...
```

## Caveats

This adapter is designed to work well with Probot apps that are essentially stateless webhook handlers.

A few other limitations exist:

1. The adapter will _only_ work for Probot apps that receive webhook events that Actions also receives: https://docs.github.com/en/actions/reference/events-that-trigger-workflows
1. The adapter will _only_ work for Probot apps with a permissions set that is less than, or equivalent to the permission set attached to the `GITHUB_TOKEN`: https://docs.github.com/en/actions/reference/authentication-in-a-workflow#permissions-for-the-github_token

If that's you, then great! :rocket:
