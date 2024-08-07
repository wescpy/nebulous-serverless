# Google Gemini API serverless web app samples

These are the code samples that are found in the 4th post _(upcoming)_ in the [series introducing the Gemini API](https://dev.to/wescpy/series/27183).


| :exclamation: API key required |
|:---------------------------|
| An API key is required to use Google's Gemini API. Follow the instructions below. If deploying locally, the app will ***not*** run without a `.env` file (Node.js) or `settings.py` (Python). For _testing/prototyping_ serverless cloud deployments, you can also use those same local files, However, for _production_ cloud deployments, rather than a local file, use GCP [Secret Manager](https://cloud.google.com/secret-manager) instead. |


| :exclamation: GCP "is not free" |
|:---------------------------|
| GCP services aren't free, however all its serverless platforms have free quota available before you incur billing. Be sure to review the ["Costs" section in the top-level `README`](https://github.com/wescpy/nebulous-serverless#cost). |


## Table of contents

There are two versions of the sample app, a Node.js Express.js app and Python Flask app. Below are the files in each folder and their purpose.


### Node.js

File | Description | Platform
--- | --- | ---
[`nodejs/main.js`](/multi/webgem/nodejs/main.js) | Express.js sample app | Node (CommonJS)
[`nodejs/main.mjs`](/multi/webgem/nodejs/main.mjs) | Express.js sample app | Node (modern JS/ECMAScript module)
[`nodejs/templates/index.html`](/multi/webgem/nodejs/templates/index.html) | Web template | Nunjucks (identical to Jinja2)
[`nodejs/app.yaml`](/multi/webgem/nodejs/app.yaml) | Config file | App Engine
[`nodejs/Dockerfile`](/multi/webgem/nodejs/Dockerfile) | (optional) Dockerfile | Cloud Run (**with** Docker)
[`nodejs/package.json`](/multi/webgem/nodejs/package.json) |  3rd-party packages file | Node


### Python

File | Description | Platform
--- | --- | ---
[`python/main.py`](/multi/webgem/python/main.py) | Flask sample app | Python 3
[`python/templates/index.html`](/multi/webgem/python/templates/index.html) | Web template | Jinja2 (identical to Nunjucks)
[`python/app.yaml`](/multi/webgem/python/app.yaml) | Config file | App Engine
[`python/Dockerfile`](/multi/webgem/python/Dockerfile) | (optional) Dockerfile | Cloud Run (**with** Docker)
[`python/Procfile`](/multi/webgem/python/Procfile) | (optional) Procfile | Cloud Run (**without** Docker)
[`python/requirements.txt`](/multi/webgem/python/requirements.txt) |  3rd-party packages file | Python


## Instructions

For GCP serverless platform deployments, [install the Cloud SDK](https://cloud.google.com/sdk/docs/install) which includes the `gcloud` CLI command. While _Node 18+_ and _Python 3.9+_ are recommended, prior releases may also work. The code was built & tested with **Node 22** and **Python 3.11**. After cloning the repo, go to the folder for whichever deploying you wish to try, e.g., `cd nodejs` for Node.js and/or `cd python` for Python.

Python developers have the option to [create & activate a virtual environment ("virtualenv") for isolation](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments) with the `python -m venv <NAME>` (or `python3`) followed by `source <NAME>/bin/activate` where `NAME` can be something like `env` or `myvenv`.


### Local

The local deployment of this web app is nearly identical to the code sample demonstrated in this [blog post](https://dev.to/wescpy/gemini-api-102a-putting-together-a-basic-genai-web-app-3e3), the third in the series linked up top. The only difference between versions: the original version, v0.1, is only available for local deployments while this v0.2 release has all the extra config files so it can be deployed to GCP serverless platforms as well as locally. This means these startup instructions are the same as what's in that post:

1. [Create API key](https://makersuite.google.com/app/apikey) and...
    - **Node.js**: Set in `.env` file as `API_KEY = <YOUR_API_KEY>;`
    - ^**Python**: Set in `settings.py` file as `API_KEY = <YOUR_API_KEY>`
1. Install packages:
    - **Node.js**: `npm i dotenv express multer nunjucks sharp @google/generative-ai`
    - **Python**: `pip install -U pip flask pillow google-generativeai` (or `pip3`)
1. Start local web server:
    - **Node.js**: `node main.js` or `node main.mjs`
    - **Python**: `python main.py` (or `python3`)

<sup>^</sup> — Alternatively for Python, instead of `settings.py`, you can save it to `.env` but would need to install `python-dotenv` and add the code to use it if you want to more closely mirror the Node version.


### Cloud (GCP serverless platforms)

For all cloud deployments, the commands for Node.js and Python are identical. The local deployment step of installing packages is not required as they will automatically be installed in the cloud per `package.json` (Node.js) and/or `requirements.txt` (Python). (And definitely don't start a local "devserver.")


#### App Engine (GAE)

1. Create API key per instructions above for local deployments (`.env` or `settings.py` okay for testing/prototypes; for product, use Secret Manager [see sidebar above])
1. Update Node.js or Python versions in `app.yaml` as desired.
1. Run `gcloud app deploy`
    - When deploying your app the first time, you'll be prompted for its (fixed) [region](https://cloud.google.com/appengine/docs/standard/locations). App Engine apps cannot be moved, so once you pick a region, that is the only region you can ever deploy the app to. (Neither Cloud Functions nor Cloud Run have this restriction.)


#### Cloud Functions (GCF)

Cloud Functions is a product generally used for microservices, simple functions, or short mobile backend requests, not entire web apps. As this is a_ basic _web app, I may try to it working on GCF, but at this time, deployments are not available. If it ever supports GCF in the future, the instructions will be similar to what's crossed-out below.

1. ~~Create API key per instructions above.~~
1. ~~Run `gcloud functions deploy genai --runtime PLATFORM --entry-point app --trigger-http --allow-unauthenticated`~~
    - ~~**Node.js**: Use  `nodejs20` or `nodejs22` for `PLATFORM`.~~
    - ~~**Python**: Use  `python311` or `python312` for `PLATFORM`.~~


#### Cloud Run (GCR)

1. Create API key per instructions above.
1. Perform pre-deployment tweaks/adjustments as necessary:
    - Deployments ***using Docker***:
        - Update Node.js or Python versions in `Dockerfile` as desired.
        - The `Procfile` is unused and may be deleted if desired.
    - Deployments ***without using Docker***:
        - Deleting the `Dockerfile` is **required**.
        - [Cloud Buildpacks](https://github.com/GoogleCloudPlatform/buildpacks) automatically detects your language and selects a [default runtime version](https://cloud.google.com/docs/buildpacks/builders). (It also creates an internal `Dockerfile` to build your container with, but you won't see it nor have to know anything about Docker or containers other than your application is bundled into one.)
        - Update startup/"entrypoint" instructions in `Procfile` as desired.
1. Run `gcloud run deploy genai --allow-unauthenticated --platform managed --source .`
    - Optionally specify a [region](https://cloud.google.com/run/docs/locations), e.g., `--region us-west1`, otherwise you'll be given a list to choose from.
    - Unlike App Engine, you can deploy apps (called GCR "services") to different or multiple regions.


| :memo: Gemini API also accessible from GCP Vertex AI |
|:---------------------------|
| While these sample apps access the Gemini API from Google AI, a great place for beginners and those experimenting with it, the API is also accessible from GCP's Vertex AI platform for those ready to use Gemini for production workloads in the cloud. To learn more about calling the Gemini API from either place (or both), see my "A better Gemini API 'Hello World!' sample" [blog post](https://dev.to/wescpy/a-better-google-gemini-api-hello-world-sample-4ddm), the first in the series linked up top.
