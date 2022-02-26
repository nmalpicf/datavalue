*************************************************
CREATE THE PROJECT AND RETRIEVE THE $PROJECT_ID
gcloud projects list

**************************************************
MAKE SURE BILLING IS ACTIVE FOR PROJECT
gcloud beta billing projects describe $PROJECT_ID

*************************************************
ENABLE CLOUD BUILD API
https://console.cloud.google.com/flows/enabl://cloud.google.com/appengine/docs/standard/python3/building-app/creating-gcp-project

**************************************************
ENABLE APP ENGINE AND CREATE RESOURCES
gcloud app create --project $PROJECT_ID

*************************************************
AUTHENTICATE AND ENABLE TESTING
gcloud auth application-default login

**************************************************
BASIC FILE STRUCTURE

    building-an-app/
        app.yaml
        main.py
        requirements.txt
        static/
            script.js
            style.css
        templates/
            index.html

*************************************************
CREATE AN ISOLATED PYTHON ENVIRONMENT
python3 -m venv env
source env/bin/activate

pip install -r requirements.txt

python main.py

**************************************************
CREATE GIT REPO
git init
gh repo create

*************************************************
BUILD IMAGE

gcloud builds submit --tag gcr.io/$PROJECT_ID/$APP_ID --project=$PROJECT_ID

**************************************************
ADD SERVICE ACCOUNT
ROLE: 
	Cloud Run Admin
	Cloud Run Service Agent
	Cloud Build Service Account
	Viewer
GENERATE keys.json

*************************************************
TESTING IMAGE

gcloud auth configure-docker

PORT=8080 && docker run \
-p 9090:${PORT} \
-e PORT=${PORT} \
-e K_SERVICE=dev \
-e K_CONFIGURATION=dev \
-e K_REVISION=dev-00001 \
-e GOOGLE_APPLICATION_CREDENTIALS=keys.json \
-v $GOOGLE_APPLICATION_CREDENTIALS:keys.json:ro \
gcr.io/$PROJECT_ID/$APP_ID

**************************************************
DEPLOY IMAGE TO GCLOUD

gcloud run deploy --image gcr.io/$PROJECT_ID/$APP_ID --platform managed --project= --allow-unauthenticated --region us-east1

*************************************************

gcloud iam service-accounts list --project=

gcloud iam service-accounts keys create ./keys.json --iam-account email@address

gcloud auth activate-service-account --key-file=keys.json
