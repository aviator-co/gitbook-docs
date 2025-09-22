# Cloud sandboxes

The Cloud based sandboxes offer easy to setup environments managed by us. This setup is ideal for individual developers, small teams who want to get started quickly. If you’d like to manage your own sandboxes, please contact [howto@aviator.co](mailto:howto@aviator.co).

Each cloud runner uses 4 core CPU with 4GB memory.

## Cloud runners

The cloud based runners are currently available in US and EU regions. Each runner consist of a baseline image that contains `git` binary and `claude code` CLI along with a few simple tools to manage the lifecycle of these runners.

All our cloud runners are single tenant and do not share permissions or environment with any other task. These act as background agents running claude code and performing operations as directed by the user, while streaming the output back to the controller.

## Lifecycle management

Every time a new task is created, a new runner will be spawned in the cloud. The runner will automatically select one of the environments suitable for this task. Cloning an existing Runbook creates a new task and a new runner environment.

A runner will automatically pause after 30 mins of inactivity. You may also choose to pause the runner manually anytime during the task.

To unpause an existing runner, simply post a message on the chat associated with that Runbook. This will reload the runner from the previous saved state and resume the conversation. All the context is loaded back when resuming the runner.

All paused runners are permanently deleted after 4 months of inactivity.

## Environment management

Environments control what tools and permissions are enabled for the runners. These environments can be preset for each repository in the Runbooks settings.

### Prebuilt environments

For quick setup, we recommend using one of our prebuilt environments:

* **Python** - Python 3.13, pip, virtualenv, uv, pytest
* **Node.js** - Node 18 LTS, npm, yarn
* **Go** - Go 1.21, go mod
* **Java** - OpenJDK 17, Maven, Gradle
* **Ruby** - Ruby 3.2, RubyGems, Bundler
* **Rust** - Rust stable, Cargo
* **PHP** - PHP 8.2, Composer
* **React/TypeScript** - Node.js, TypeScript, React, Vite, ESLint, Prettier
* **Vue.js** - Node.js, Vue 3, Vite, TypeScript support
* **Angular** - Node.js, Angular CLI, TypeScript
* **Next.js** - Node.js, Next.js 14, TypeScript, Tailwind CSS
* **MEAN Stack** - MongoDB, Express, Angular, Node.js, TypeScript
* **MERN Stack** - MongoDB, Express, React, Node.js, TypeScript
* **Django + React** - Python, Django, Node.js, React, PostgreSQL
* **Rails + Vue** - Ruby on Rails, Node.js, Vue.js, PostgreSQL
* **Spring Boot + React** - Java, Spring Boot, Node.js, React, Maven
* **Go + Next.js** - Go, Node.js, Next.js, TypeScript
* **FastAPI + React** - Python, FastAPI, Node.js, React, TypeScript

### Custom environment

{% hint style="info" %}
Custom environments are still in beta, please reach out for early access: [howto@aviator.co](mailto:howto@aviator.co)
{% endhint %}

You can also choose to build a custom environment. To do so, you can choose one of our prebuild base images and add additional dependencies using a bash script. We will custom build these images and save them to be used by the runners.

## Secrets management

Secrets are credentials that are stored encrypted and can be injected into the runners for each environment. These secrets can be used for two purposes:

* providing access to MCP configuration
* using as environment variables to perform specific actions within the runners

These secrets can be created by anyone, either for the entire organization or just for personal use. Admins can manage the permissions of organization wide secrets. Secrets cannot be retrieved in clear text once saved.
