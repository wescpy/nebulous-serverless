# Google Gemini API serverless web app samples
These are the code samples that are found in the (_forthcoming_) post in the [series introducing the Gemini API](https://dev.to/wescpy/series/27183). While you're waiting for that, all of the code is explained in [this post](https://dev.to/wescpy/gemini-api-102a-putting-together-a-basic-genai-web-app-3e3). (The apps in that post don't have the resources necessary for you to deploy them to GCP serverless platforms.)

| :exclamation: **API key required** |
|:---------------------------|
| An API key is required to use the Gemini API. Follow the instructions below. The app will ***not*** run without a `.env` file (Node.js) or `settings.py` (Python)s, whether for running locally or for _testing/prototyping_ serverless cloud deployments. However, for _production_ cloud deployments, rather than local files, use environment variables or GCP [Secret Manager](https://cloud.google.com/secret-manager) instead. |

| :exclamation: **GCP "is not free"** |
|:---------------------------|
| GCP services aren't free, however all its serverless platforms have free quota available before you incur billing. Be sure to review the ["Costs" section in the top-level `README`](https://github.com/wescpy/nebulous-serverless#cost). If you are doing this as an event workshop, it's likely you were given GCP credits for a small amount in order to give you access to GCP and its services... refer to those instructions as necessary. |


## Table of contents
There are two versions of the sample app, Node.js and Python. Below are the files in each folder and their purpose. Pick one or both apps to try for yourself.


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


## Setup
Follow the instructions below to deploy these apps to GCP serverless platforms, App Engine and Cloud Run:
1. **Get `gcloud` CLI tool**: [Install the Cloud SDK](https://cloud.google.com/sdk/docs/install) (includes `gcloud`)
1. **Languages**: While _Node 18+_ and _Python 3.9+_ are recommended, prior releases may also work. The code was built & tested with current versions of Node (20 or 22) and Python (3.11 or 3.12). After cloning this repo, go to the desired folder, `nodejs` or `python`.
1. (_optional_) **Create "virtualenv"**: Python developers have the option to [create & activate a virtual environment (virtualenv) for isolation](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments) with `python -m venv <NAME>` (or `python3`) followed by `source <NAME>/bin/activate` where `NAME` can be something like `env`, `myvenv`, etc.


## Run locally
1. [Create API key](https://makersuite.google.com/app/apikey) (required by Gemini API) and...
    - **Node.js**: Set in `.env` file as `API_KEY = <YOUR_API_KEY>;`
    - ^**Python**: Set in `settings.py` file as `API_KEY = '<YOUR_API_KEY>'`
1. Install 3rd-party packages (from scratch or via requirements file):
    - **Node.js** (from `package.json`): `npm up`
    - **Python** (from `requirements.txt`): `pip install -r requirements.txt` (or `pip3`)
    - OR
    - **Node.js** (from scratch): `npm i dotenv express multer nunjucks sharp @google/generative-ai`
    - **Python** (from scratch): `pip install -U pip flask pillow google-generativeai` (or `pip3`)
1. Start local web server:
    - **Node.js**: `npm start` (runs `node main.mjs`) or `node main.js`
    - **Python**: `python main.py` (or `python3`)

<sup>^</sup> — Alternatively for Python, instead of `settings.py`, you can save it to `.env` but would need to install `python-dotenv` and add the code to use it if you want to more closely mirror the Node version.

These apps are nearly-identical to those featured this [blog post](https://dev.to/wescpy/gemini-api-102a-putting-together-a-basic-genai-web-app-3e3). The difference is that the original v0.1 can _only_ run locally. This v0.2 release has the additional config files necessary to make it deployable to the cloud in addition to running locally.


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
| While these sample apps access the Gemini API from Google AI, a great place for beginners and those experimenting with it, the API is also accessible from GCP's Vertex AI platform for those ready to use Gemini for production workloads in the cloud. To learn more about calling the Gemini API from either place (or both), see the first post listed in the **Gemini API** references subsection below.


## References
To learn more, check out these blog posts:

### GCP serverless platforms
1. [A broader perspective of serverless](https://dev.to/wescpy/a-broader-perspective-of-serverless-1md1)
1. [Hosting apps in the cloud with Google App Engine in 2024](https://dev.to/wescpy/hosting-apps-in-the-cloud-with-google-app-engine-3fn)
1. [Google App Engine Jan 2024 deprecation: What you need to know](https://dev.to/wescpy/python-app-engine-jan-2024-deprecation-what-you-need-to-know-4bci)

### Gemini API (from Google AI or GCP Vertex AI)
1. [A better Google Gemini API "Hello World!" sample](https://dev.to/wescpy/a-better-google-gemini-api-hello-world-sample-4ddm)
1. [Gemini API 102: Next steps beyond "Hello World!"](https://dev.to/wescpy/gemini-api-102-next-steps-beyond-hello-world-1pb7)
1. [Gemini API 102a: Putting together basic GenAI web apps](https://dev.to/wescpy/gemini-api-102a-putting-together-a-basic-genai-web-app-3e3)
1. (_A 4th post featuring these sample apps is forthcoming._)
