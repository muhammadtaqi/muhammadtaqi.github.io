---
layout: post
title:  "Pipenv, in Brief with Python projects"
date:   2017-11-28 16:16:01 -0600
categories: tech
---

Pipenv Aims to bring the best of modern packaging (npm, yarn etc.) to Python. It's developed and maintained By Kenneth Reitz (Author of Requests).

## Before Pipenv

- **_Before pipenv_**;
    - you have to create virtual environment via virualenv or virtualenvwrapper.
    - we version all dependencies in requirement.txt file. The issue is that if we store all in one file, then it's difficult to recon which dependencies are for dev or prod.
    - we get rid of this, we divide our requirement.txt into local.txt, dev.txt and prod.txt i.e in django we use like this, 
```bash
reqs/
├── base.txt
├── local.txt
└── prod.txt
```

- **_After pipenv_**;
    - Pipenv have two files, `Pipfile` and `Pipfile.lock` similar to `composer` and `composer.lock` in PHP. Where `Pipfile` list the versions of our Python dependencies and  `Pipfile.lock` contains references to the exact versions used. 
    - You don't need to create virtual environment yourself, just cd into project folder and run `pipenv shell`, it will detect `Pipfile` in project if it's present, it will create and activate virual enviornment for you.
    - Build in support for `.env` file. on loading the env using pipenv shell, it will detects .env if it's present, it will loaded up the env variable first automatically.

```bash
pip install pipenv
cd my_project
pipenv --three # run pipenv along with python version
pipenv install requests
or 
pipenv install requests --dev # for dev env
```

if there is a pipfile.lock then it will update it, else will create a new one.
It will create two files, `Pipfile` file content will be like below;

```
[[source]]
name = "pypi"
verify_ssl = true
url = "https://pypi.python.org/simple"

[dev-packages]

[requires]
python_version = "3.5"

[packages]
requests = "*"
```

and `Pipfile.lock` file content will be like below;
```json
{
    "_meta": {
        "hash": {
            "sha256": "38e7c0aa4cd8d4859a901333a72573d0573f4b06e1f3bacdc118f4dfd723c00a"
        },
        "host-environment-markers": {
            "implementation_name": "cpython",
            "implementation_version": "3.5.2",
            "os_name": "posix",
            "platform_machine": "x86_64",
            "platform_python_implementation": "CPython",
            "platform_release": "4.10.0-40-generic",
            "platform_system": "Linux",
            "platform_version": "#44~16.04.1-Ubuntu SMP Thu Nov 9 15:37:44 UTC 2017",
            "python_full_version": "3.5.2",
            "python_version": "3.5",
            "sys_platform": "linux"
        },
        "pipfile-spec": 6,
        "requires": {
            "python_version": "3.5"
        },
        "sources": [
            {
                "name": "pypi",
                "url": "https://pypi.python.org/simple",
                "verify_ssl": true
            }
        ]
    },
    "default": {
        "certifi": {
            "hashes": [
                "sha256:244be0d93b71e93fc0a0a479862051414d0e00e16435707e5bf5000f92e04694",
                "sha256:5ec74291ca1136b40f0379e1128ff80e866597e4e2c1e755739a913bbc3613c0"
            ],
            "version": "==2017.11.5"
        },
        "chardet": {
            "hashes": [
                "sha256:fc323ffcaeaed0e0a02bf4d117757b98aed530d9ed4531e3e15460124c106691",
                "sha256:84ab92ed1c4d4f16916e05906b6b75a6c0fb5db821cc65e70cbd64a3e2a5eaae"
            ],
            "version": "==3.0.4"
        },
        "idna": {
            "hashes": [
                "sha256:8c7309c718f94b3a625cb648ace320157ad16ff131ae0af362c9f21b80ef6ec4",
                "sha256:2c6a5de3089009e3da7c5dde64a141dbc8551d5b7f6cf4ed7c2568d0cc520a8f"
            ],
            "version": "==2.6"
        },
        "requests": {
            "hashes": [
                "sha256:6a1b267aa90cac58ac3a765d067950e7dbbf75b1da07e895d1f594193a40a38b",
                "sha256:9c443e7324ba5b85070c4a818ade28bfabedf16ea10206da1132edaa6dda237e"
            ],
            "version": "==2.18.4"
        },
        "urllib3": {
            "hashes": [
                "sha256:06330f386d6e4b195fbfc736b297f58c5a892e4440e54d294d7004e3a9bbea1b",
                "sha256:cc44da8e1145637334317feebd728bd869a35285b93cbb4cca2577da7e62db4f"
            ],
            "version": "==1.22"
        }
    },
    "develop": {}
}
```

## More on Pipenv


- **_Upgrade Packages_** - `pipenv install <pkg>==<version>` will update your `Pipfile` and `Pipfile.lock`. Best practice is to run `pipenv install` on each time you update any package. it will install all dependedies from `Pipfile.lock`.
- **_Uninstalling Packages_** - `pipenv uninstall <pkg>`
- **_Deleting the Venv_** - `pipenv --rm`, `Pipfile` and `Pipfile.lock` files will not be deleted.
- **_Setting up on a new environment_** - `pipenv install` will automatically create a new env.