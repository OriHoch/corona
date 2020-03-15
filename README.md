# Corona related stuff

## Published data

* Ministry of health updates, updated hourly from the official Telegram channel: `https://www.odata.org.il/dataset/mohreport-corona/resource/ce4c9482-cd3a-485b-af56-d3d7118a7552`

## Install

Prerequisites

* Python 3.6
* Virtualenv

```
mkdir venv
python3.6 -m virtualenv -p python3 venv
```

## Usage / Development

```
. venv/bin/activate
pip install -r requirements.txt
jupyter lab
```

Run and edit .ipynb files using the Jupyter lab web-UI

## Scheduled jobs

We use Jenkins to run scheduled jobs, define the following job for each notebook:

* Discard old builds - max builds: `50`
* Git repository - set to the corona repo
* Build periodically - `H * * * *` - to run hourly
* Use secret text - variable `CKAN_API_KEY` - your CKAN api key for odata.org.il
* execute shell - 
```
#!/usr/bin/env bash

python3 --version &&\
if ! python3 -m virtualenv --version; then
	python3 -m pip install --user virtualenv
fi &&\
if [ ! -e venv ]; then
	mkdir venv &&\
	python3 -m virtualenv -p python3 venv
fi &&\
venv/bin/python3 -m pip install -r requirements.txt &&\
venv/bin/jupyter-nbconvert --to=python --stdout Israel\ Ministry\ of\ Health\ Updates.ipynb | venv/bin/python3
```
* post build - archive the artifacts - `data/**/*`
