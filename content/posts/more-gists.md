---
title: "😃 More useful gists"
date: "2021-02-10"
author: grahamplata
excert: "Useful gists from my travels"
tags: ["gists"]
---

## Authenticating google cloud SDK services in local docker environments

Google's auth libraries attempt find valid credentials in a few locations:

- An Environment variable `GOOGLE_APPLICATION_CREDENTIALS` a json blob ~
- The metadata server on Google Cloud Platform
- Check the default location of credentials file `$HOME/.config/gcloud/application_default_credentials.json` (Location may vary)

---

A mistake I encountered 

- `gcloud auth login` - Allows you to run gcloud commands from your terminal and it will find your credentials automatically.
- `gcloud auth application-default login` Stores Default Credentials' in a file named `application_default_credentials.json`

I needed to use the second so the application credentials are created and accessible for google sdk based libraries

> Below is a somple snippet to mount your local credentials into a docker container via compose

```yaml
# Snippet of docker-compose...
volumes:
  - $HOME/.config/gcloud:/root/.config/gcloud
# rest of file ...
```
