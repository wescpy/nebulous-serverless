# Nebulous serverless Cloud Translation API app

## Node.js version

The Node.js version of this app and its deployments are simpler than the [Python equivalent](../python), primarily due to the differences between Python 2 and 3, and also because Node.js is not an App Engine legacy runtime. As a result, there is a single [Node.js codelab](https://codelabs.developers.google.com/codelabs/cloud-nebulous-serverless-nodejs?utm_source=codelabs&utm_medium=et&utm_campaign=CDR_wes_aap-serverless_nebservnodejs_sms_201130&utm_content=-) for deploying this app while there are seven for Python.


## Deployments and their files

File | Description
--- | ---
[`index.js`](index.js) | main application file
[`templates/index.html`](templates/index.html) | application HTML template
[`package.json`](package.json) | 3rd-party package requirements file
[`app.yaml`](app.yaml) | App Engine configuration file (only for App Engine deployments)
[`test/test_neb.js`](test/test_neb.js) |  unit tests (`mocha` &amp; `supertest`)
[`.gcloudignore`](.gcloudignore) | files to exclude deploying to the cloud (administrative)
`README.md` | this file (administrative)

Below are the required settings and instructions for all (documented) deployments; administrative files are not discussed. The `app.yaml` file is only used for specific deployments and can be deleted for the others.

> NOTE: Serverless deployments (as configured here) use [default service accounts](https://cloud.google.com/iam/docs/service-accounts#default) which provide a broad set of permissions to assist you in getting a working prototype. When preparing to launch to production, the Google Cloud team recommends the best practice of "least privileges," and instead use [user-managed service accounts](https://cloud.google.com/iam/docs/service-accounts#user-managed) with the minimal set of permissions allowing your app to function properly.


## **Local Express server**

**TL;DR:** application files (`index.js` &amp; `package.json`). Instructions:

1. **Run** `npm install` (to install packages locally)
1. **Run** `gcloud auth application-default login` to set your credentials
1. **Run** `npm start` to run locally


## **App Engine**

**TL;DR:** application files plus `app.yaml`. You may (first) edit `app.yaml` to specify the desired Node version (default: Node 18). Instruction(s):

1. **Run** `gcloud app deploy` to deploy to App Engine
    - You'll be prompted for the REGION if deploying to App Engine the first time.
    - App Engine apps are tied to one region, so it can't be changed once it's set, meaning you won't be prompted thereafter.


## **Cloud Functions**

**TL;DR:** Uses only the application files. Instruction(s):

1. **Run** `gcloud functions deploy translate --runtime nodejs18 --entry-point app --trigger-http --allow-unauthenticated` to deploy to Cloud Functions
    - You'll be prompted for the REGION if deploying a Cloud Function the first time.
    - Cloud Functions can be deployed to different regions within a project, but once the region has been set for a function, it cannot be changed.

The command creates &amp; deploys a new HTTP-triggered Cloud Function named `translate`. Cloud Functions is directed to call the application object, `app`, via `--entry-point`. During execution `translate()` is called by `app`. In the [Python version](../python), `--entry-point` is unnecessary because `translate()` *is* the application entry point.


## **Cloud Run (via Cloud Buildpacks)**

**TL;DR:** Uses only the application files. Instruction(s):

1. **Run** `gcloud run deploy translate --allow-unauthenticated --platform managed --source .` to deploy to Cloud Run
    - You'll be prompted to provide a REGION unless you also add `--region REGION` on the cmd-line which will give you a full non-interactive deploy
    - A `Dockerfile` is optional, but if you wish to create one, place it in the top-level folder so the build system can access it. Also see the [Python version's `Dockerfile`](../python/Dockerfile) to get an idea of what a Node equivalent would be similar to.


## References

These are relevant links only to the app in this folder (for all others, see the [README one level up](../README.md):

- [Node.js App Engine quickstart](https://cloud.google.com/appengine/docs/standard/nodejs/quickstart)
- [Nodejs App Engine (standard environment) runtime](https://cloud.google.com/appengine/docs/standard/nodejs/runtime)
- [Node.js Cloud Functions quickstart](https://cloud.google.com/functions/docs/quickstart-nodejs)
- [Node.js Cloud Run quickstart](https://cloud.google.com/run/docs/quickstarts/build-and-deploy/nodejs)
- [Express.js](https://expressjs.com)


## Testing

Testing is driven by [`mocha`](https://mochajs.org) which uses [`supertest`](https://github.com/visionmedia/supertest) for testing and [`eslint`](https://eslint.org) for linting, installing both in virtual environments along with application dependencies, `express`, `nunjucks`, and `@google-cloud/translate`. To run the unit tests (testing `GET` and `POST` requests), run `npm install` followed by `npm test`).


### Expected output

```
$ npm test

> cloud-nebulous-serverless-nodejs@0.0.1 test
> mocha test/test_neb.js

Listening on port 8080


  Our application
    ✔ GET / should result in HTML w/"translate" in the body
    ✔ POST / should have translated "hello world" correctly (140ms)
DONE


  2 passing (170ms)
```

### Troubleshooting

When running the test, there's a situation which resulting in the test hanging like this:

```
$ npm test

> cloud-nebulous-serverless-nodejs@0.0.1 test
> mocha test/test_neb.js

Listening on port 8080


  Our application
    ✔ GET / should result in HTML w/"translate" in the body
    1) POST / should have translated "hello world" correctly
DONE


  1 passing (2s)
  1 failing

  1) Our application
       POST / should have translated "hello world" correctly:
     Error: Timeout of 2000ms exceeded. For async tests and hooks, ensure "done()" is called; if returning a Promise, ensure it resolves. (/tmp/cloud-nebulous-serverless/cloud/nodejs/test/test_neb.js)
      at listOnTimeout (node:internal/timers:557:17)
      at processTimers (node:internal/timers:500:7)

```
*(hangs here)*

If this happens to you, **run** `gcloud auth application-default login` to set your credentials and try again.
