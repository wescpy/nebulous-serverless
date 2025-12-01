# Google Gemini API serverless web app samples

This is (will be) featured in an upcoming post in the [Gemini API series](https://dev.to/wescpy/series/27183). While you're waiting for that, all of the code is explained in [this post](http://bit.ly/3Kqv78c). The app in that post is nearly identical to the one here but lacks the configuration files to be deployable to GCP serverless platforms. (In other words, it's the same web app but can only be self-hosted/hosted locally.)

| :exclamation: **API key required** |
|:---------------------------|
| An API key is required to use the Gemini API. Follow the instructions below. The app will ***not*** run without a `.env` file (Node.js) or `settings.py` (Python)s, whether for running locally or for _testing/prototyping_ serverless cloud deployments. However, for _production_ cloud deployments, rather than local files, use environment variables or GCP [Secret Manager](https://cloud.google.com/secret-manager) instead. A template for each, `.env_TMPL` and `settings_TMPL.py`, respectively, are provided as a convenience. |

| :exclamation: **GCP "is not free"** |
|:---------------------------|
| GCP services aren't free, however all its serverless platforms have free quota available before you incur billing. Be sure to review the ["Costs" section in the top-level `README`](https://github.com/wescpy/nebulous-serverless#cost). If you are doing this as an event workshop, it's likely you were given GCP credits for a small amount in order to give you access to GCP and its services... refer to those instructions as necessary. |


## Table of contents
There are two versions of the sample app, Node.js and Python. Below are the files in each folder and their purpose. Pick one or both apps to try for yourself.


### Node.js
File | Description | Platform
--- | --- | ---
[`nodejs/.env_TMPL`](/multi/webgem/nodejs/.env_TMPL) | `.env` environment settings template | Node
[`nodejs/package.json`](/multi/webgem/nodejs/package.json) |  3rd-party packages | Node
[`nodejs/main.js`](/multi/webgem/nodejs/main.js) | Express.js sample app | Node (CommonJS script)
[`nodejs/main.mjs`](/multi/webgem/nodejs/main.mjs) | Express.js sample app | Node (ECMAScript module)
[`nodejs/templates/index.html`](/multi/webgem/nodejs/templates/index.html) | Web template | Nunjucks (identical to Jinja2)
[`nodejs/app.yaml`](/multi/webgem/nodejs/app.yaml) | Config file | App Engine
[`nodejs/Dockerfile`](/multi/webgem/nodejs/Dockerfile) | Dockerfile | Cloud Run (**with** Docker)
[`nodejs/.dockerignore`](/multi/webgem/nodejs/.dockerignore) | .dockerignore | Cloud Run (**with** Docker)
[`nodejs/Procfile`](/multi/webgem/nodejs/Procfile) | Procfile | Cloud Run (**without** Docker)
[`nodejs/.gcloudignore`](/multi/webgem/nodejs/.gcloudignore) | .gcloudignore | App Engine & Cloud Run


### Python
File | Description | Platform
--- | --- | ---
[`python/settings_TMPL.py`](/multi/webgem/python/settings_TMPL.py) | `settings.py` environment settings template | Python 3
[`python/requirements.txt`](/multi/webgem/python/requirements.txt) | Flask 3rd-party packages | Python
[`python/main.py`](/multi/webgem/python/main.py) | Flask sample app | Python 3
[`python/templates/index.html`](/multi/webgem/python/templates/index.html) | Web template | Jinja2 (identical to Nunjucks)
[`python/app.yaml`](/multi/webgem/python/app.yaml) | Config file (Flask) | App Engine
[`python/Dockerfile`](/multi/webgem/python/Dockerfile) | Dockerfile (Flask) | Cloud Run (**with** Docker)
[`python/.dockerignore`](/multi/webgem/python/.dockerignore) | .dockerignore | Cloud Run (**with** Docker)
[`python/Procfile`](/multi/webgem/python/Procfile) | Procfile (Flask) | Cloud Run (**without** Docker)
[`python/.gcloudignore`](/multi/webgem/python/.gcloudignore) | .gcloudignore | App Engine & Cloud Run
 | |
[`python/fastapi/requirements.txt`](/multi/webgem/python/fastapi/requirements.txt) | FastAPI 3rd-party packages | Python 3
[`python/fastapi/main.py`](/multi/webgem/python/fastapi/main.py) | FastAPI sample app | Python 3
[`python/fastapi/app.yaml`](/multi/webgem/python/fastapi/app.yaml) | Config file (FastAPI) | App Engine
[`python/fastapi/Dockerfile`](/multi/webgem/python/fastapi/Dockerfile) | Dockerfile (FastAPI) | Cloud Run (**with** Docker)
[`python/fastapi/Procfile`](/multi/webgem/python/fastapi/Procfile) | Procfile (FastAPI) | Cloud Run (**without** Docker)

The Python app is available in two flavors, the original Flask (synchronous) version, or a FastAPI async version. For the latter, grab the files from the [`fastapi` subfolder](/multi/webgem/python/fastapi) and overwrite their Flask equivalents in the main folder.

| :exclamation: **FastAPI app on GAE "requires" `gunicorn`** |
|:---------------------------|
| Deploying the FastAPI app on App Engine requires you to uncomment `gunicorn` from its `requirements.txt` file. GAE defaults to providing generic (synchronous) `gunicorn` usage, however FastAPI apps use the (asynchronous) `uvicorn`, so you have to explicity "install" `gunicorn` and tell it to use `uvicorn` workers... see the `entrypoint:` directive in its `app.yaml`. |


## Setup
Follow the instructions below to deploy these apps to GCP serverless platforms, App Engine and Cloud Run:
1. **Get `gcloud` CLI tool**: [Install the Cloud SDK](https://cloud.google.com/sdk/docs/install) (includes `gcloud`)
1. **Languages**: While _Node 18+_ and _Python 3.9+_ are recommended, prior releases may also work. The code was built & tested with current versions of Node (24-26) and Python (3.11-3.13). After cloning this repo, go to the desired folder, `nodejs` or `python`. For Python developers:
    - In the commands below, depending on your system configuration, you will use one of (`pip`, `pip3`, `python3 -m pip`), but the instructions are generalized to `pip`.

For local development, considering creating a virtual environment and using `uv`:
- (_optional_) **Create "virtualenv"**: [Create & activate a virtual environment (virtualenv) for isolation](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments) with `python -m venv <NAME>` (or `python3`) followed by `source <NAME>/bin/activate` where `NAME` can be something like `env`, `myvenv`, etc.
- (optional) Update `pip` and install `uv`: `pip install -U pip uv`


## Run locally
1. [Create API key](https://makersuite.google.com/app/apikey) (required by Gemini API) and...
    - **Node.js**: Set in `.env` file as `API_KEY = <YOUR_API_KEY>;`
    - ^**Python**: Set in `settings.py` file as `API_KEY = '<YOUR_API_KEY>'`
1. Install 3rd-party packages (from scratch or via requirements file):
    - **Node.js**: `npm i`
    - **Python**: `uv pip install -Ur requirements.txt` (drop `uv` if you didn't install it)
1. Start local web server:
    - **Node.js**: `npm start` (runs `node main.mjs`) or `node main.js`
    - **Python**: `python main.py` (or `python3`)

<sup>^</sup> — Alternatively for Python, instead of `settings.py`, you can save it to `.env` but would need to install `python-dotenv` and add the code to use it if you want to more closely mirror the Node version.

This app is nearly-identical to what's featured in this [blog post](http://bit.ly/3Kqv78c). The difference: [that version](https://github.com/wescpy/google/tree/main/gemini/webgem) _can_ ***only*** _run locally_. This cloud-based version has additional config files so it's deployable to the cloud (serverless platforms), in addition to running locally.


## Deploy to and host/run on Google Cloud
The command to deploy this app to Google Cloud are the same for Node.js and Python. Pre-installation of 3rd-party packages is not required as they will automatically be installed as part of the build-and-deploy cycle.


### App Engine (GAE)
1. Create API key per instructions above for local deployments (`.env` or `settings.py` okay for testing/prototypes; for production, use Secret Manager [see sidebar above])
1. [Create new project or reuse existing one](https://cloud.google.com/resource-manager/docs/creating-managing-projects); confirm [assigned billing account](https://cloud.google.com/billing/docs/how-to/modify-project).
1. Update Node.js or Python versions in `app.yaml` as desired.
1. Deploy app to the cloud with `gcloud app deploy`
    - On first deploy, you may be prompted to...
        1. Assign billing account if you haven't already done so
        1. Enable service or to create an App Engine app
        1. Set its (fixed) [region](https://cloud.google.com/appengine/docs/standard/locations). App Engine apps cannot be moved, so the region choice is permanent (app is still accessible globally). Cloud Run does ***not*** have this restriction.


### Cloud Functions (GCF)
Cloud Functions is a product generally used for microservices, simple functions, or short mobile backend requests, not entire web apps. As this is a _basic_ (single-function) web app, I may try to get it working on GCF in the future, or more likely, on its 2nd-gen successor platform, Cloud Run Functions (GCRF). If I did, the deploy command may be similar to what's crossed-out below:

1. ~~Create API key per instructions above.~~
1. ~~Run `gcloud functions deploy genai --runtime PLATFORM --entry-point app --trigger-http --allow-unauthenticated`~~
    - ~~**Node.js**: Use  `nodejs20` or `nodejs22` for `PLATFORM`.~~
    - ~~**Python**: Use  `python311` or `python312` for `PLATFORM`.~~


### Cloud Run (GCR)
1. Create API key per instructions above.
1. Create new project or reuse existing one per instructions above for App Engine and confirm assigned billing account.
1. Perform pre-deployment tweaks/adjustments as necessary:
    - Deploy ***with Docker***:
        - Update Node.js or Python versions in `Dockerfile` as desired.
        - The `Procfile` is unused and can be deleted.
    - Deploy ***without Docker***:
        - ^You **must** delete the `Dockerfile`.
        - The `Procfile` instructs the server on how to start the app... adjust if desired.
1. Deploy app ("service") to the cloud with `gcloud run deploy genai --allow-unauthenticated --platform managed --source .`
    - Service name "genai" can be changed
    - On first deploy, you may be prompted to...
        - Assign billing account if you haven't already done so
        - Enable Cloud Run service and/or Cloud Artifact Registry
    - Optionally specify a [region](https://cloud.google.com/run/docs/locations), e.g., `--region us-west1`, otherwise you'll be given a list to choose from.

<sup>^</sup> — How does it work _without_ Docker? [Cloud Buildpacks](https://github.com/GoogleCloudPlatform/buildpacks) automatically detects your language, selects a [default runtime version](https://cloud.google.com/docs/buildpacks/builders), and provides the instructions to build the containerized app that the `Dockerfile` would normally do.


## Clean-up
Who wants to get billed? Follow one set of instructions below to stop billing, temporarily or permanently.


### Temporarily
This for those who want to temporarily pause serving of their app but allows you to come back and continue to experiment because your credits are good for 6 months.
1. **Disable App Engine app**: To pause serving of your app, [disable it](https://console.cloud.google.com/appengine/settings). When you're ready to resume, re-enable it again from the same place.
1. **Delete Cloud Run service**: To pause serving of your app, [delete the service](https://cloud.google.com/run/docs/managing/services#delete). When you're ready to resume, realize the container image is still in the [Artifact Registry](https://console.cloud.google.com/artifacts), so re-deploy it from there by selecting the `cloud-run-source-deploy` image for the region you specified. Once you select an image, click the 3-dots/ice cream cone menu, and deploy to Cloud Run (or GKE or GCE).


### Permanently
On the other hand, if you're not going to continue with GAE or GCR or this GCP project at all, delete everything forever to avoid possible billing. To do that, [shutdown your project](https://console.cloud.google.com/iam-admin/settings).


| :exclamation: **App billing different from storage billing** |
|:---------------------------|
| While you'd no longer get billed for GAE or GCR app-/container-service, build files and images are still taking up space in Google Cloud Storage (GCS) and Artifact Registry, and both are billable services. See the **GCP is not free** sidebar above. |


| :memo: **Gemini API also accessible from GCP Vertex AI** |
|:---------------------------|
| While these sample apps access the Gemini API from Google AI, a great place for beginners and those experimenting with it, the API is also accessible from GCP's Vertex AI platform for those ready to use Gemini for production workloads. To learn more about calling the Gemini API from either (or both) places, see [this post](https://bit.ly/4kFkmLm). The `.env_TMPL` and `settings_TMPL.py` files have placeholders for the required GCP information to call the Gemini API from the Vertex AI platform. |


## References
To learn more, check out these blog posts:

### GCP serverless platforms
1. [A broader perspective of serverless](https://dev.to/wescpy/a-broader-perspective-of-serverless-1md1)
1. [Hosting apps in the cloud with Google App Engine in 2024](https://dev.to/wescpy/hosting-apps-in-the-cloud-with-google-app-engine-3fn)
1. [Google App Engine Jan 2024 deprecation: What you need to know](https://dev.to/wescpy/python-app-engine-jan-2024-deprecation-what-you-need-to-know-4bci)

### Gemini API (from Google AI or GCP Vertex AI) blog post series
1. [A better Google Gemini API "Hello World!" sample](https://dev.to/wescpy/a-better-google-gemini-api-hello-world-sample-4ddm)
1. [Gemini API 102: Next steps beyond "Hello World!"](https://dev.to/wescpy/gemini-api-102-next-steps-beyond-hello-world-1pb7)
1. [Gemini API 102a: Putting together basic GenAI web apps](https://dev.to/wescpy/gemini-api-102a-putting-together-a-basic-genai-web-app-3e3)
1. [Generating audio clips with Gemini 2.0 Flash](https://dev.to/wescpy/generate-audio-clips-with-gemini-20-flash-from-google-n0g)
1. [Generating images with Gemini 2.0 Flash](https://dev.to/wescpy/generating-images-with-gemini-20-flash-from-google-448e)
1. [Gemini 2.5 API Missing Manual: How to get started (or upgrade from Gemini 1.0/1.5)](https://bit.ly/4kFkmLm)
