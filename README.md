pre-commit-docker-flake8
========================

## What is this?

This is a proof-of-concept of providing a [pre-commit](http://pre-commit.com)
hook for flake8 via docker.  It is suggested to instead use flake8 as provided
by [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks) as
you'll have slightly better performance and more control over the python
executable.

## How does this work?

`pre-commit` by convention mounts the user's code at `/src` inside the
container.  The executable is fed relative path filename arguments.  The hook
may make changes to the files as the source is mounted read-only and the
executables are run as the user

A few key lines in the `Dockerfile` make this work:

```dockerfile
RUN virtualenv /venv -ppython3 && /venv/bin/pip install flake8
ENV PATH=/venv/bin:$PATH
```

This creates a virtual environment inside the docker image and puts the
virtualenv on the `PATH` so executables (such as `flake8`) can be run.

Lastly, the metadata in hooks.yaml hooks this up:

```yaml
    entry: flake8
    language: docker
```

Here `flake8` is the executable inside the repository and we tell
`pre-commit` that the language is `docker`.


## How would I use this repository with pre-commit?

Well, you probably wouldn't (and the hook has been labeled as such) but if you
really wanted to you could add this to your `.pre-commit-config.yaml`:

```yaml
-   repo: git://github.com/pre-commit/pre-commit-docker-flake8
    sha: ''  # Fill this in with a current revision
    hooks:
    -   id: flake8-docker-not-for-production
```

You'll also need to use at least version 0.10.0 of pre-commit.
