## Deployment for backend and mongo, frontend is running at local development


### 1. Create a New Google Cloud Project
gcloud projects create new-campusmind-project --name="CampusMind Project"

Set the new project as the active project:
```
gcloud config set project new-campusmind-project
```

### 2. Enable Required Google Cloud APIs

Enable the Cloud Run API:
```
gcloud services enable run.googleapis.com
```

Enable the Cloud Build API:
```
gcloud services enable cloudbuild.googleapis.com
```

Enable the Container Registry API:
```
gcloud services enable containerregistry.googleapis.com
```

### 3. Set Up Billing for the Project

Go to the Google Cloud Console: https://console.cloud.google.com/billing.

Link a billing account to your project (new-campusmind-project).

Alternatively, use the CLI (replace BILLING_ACCOUNT_ID with your billing account ID, found in the Billing section of the Console):
```
gcloud beta billing projects link new-campusmind-project --billing-account BILLING_ACCOUNT_ID
```

### 4. Authenticate Docker for Pushing Images

Configure Docker to authenticate with Google Container Registry:
```
gcloud auth configure-docker
```

### 5. Navigate to the Backend Directory
```
cd C:\Users\User\Desktop\Deploy-CampusMind-Project\CampusMind\backend
```

### 6. Verify or Create the Dockerfile

#### Dockerfile:
```
FROM node:slim
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
ENV PORT=8080
EXPOSE 8080
CMD ["node", "app.js"]
```

### 8. Create a .gcloudignore File to Exclude the .env File
In C:\Users\User\Desktop\Deploy-CampusMind-Project\CampusMind\backend, create a file named **.gcloudignore** with the following content:
```
.env
node_modules
.git
.gitignore
```

### 9. Build and Push the Docker Image
Build the Docker image and push it to Google Container Registry (replace new-campusmind-project with your project ID if different):

powershell
```
gcloud builds submit --tag gcr.io/new-campusmind-project/booking-backend
```

Build the Docker image and push it to Google Container Registry (replace new-campusmind-project with your project ID if different):
powershell
```
gcloud builds submit --tag gcr.io/new-campusmind-project/booking-backend
```

### 10. Deploy the Backend to Cloud Run
```
gcloud run deploy booking-backend `
  --image gcr.io/new-campusmind-project/booking-backend `
  --platform managed `
  --region us-central1 `
  --allow-unauthenticated `
  --set-env-vars MONGODB_URL=mongodb+srv://CampusMind:YEMlXkgb8Q2fXiNX@cluster0.qwhir.mongodb.net/newFrontEnd `
  --set-env-vars JWT_KEY=mind `
  --set-env-vars POSTMARK_API_KEY=189abd5e-3061-4116-8bec-daf84d6accbc `
  --set-env-vars NODE_ENV=production
```

### 11. Test the Deployed Backend
Test the backend using the service URL:
```
curl https://booking-backend-abc123-uc.a.run.app
```

### 12. Navigate to the Frontend Directory
```
cd C:\Users\User\Desktop\Deploy-CampusMind-Project\CampusMind\frontend
```

### 13. Install Frontend Dependencies
```
npm install
```

### 14. Update Frontend Code to Use the Backend URL: 
create a new .env file in CampusMind/frontend then add this line:
```
EXPO_PUBLIC_API_URL=https://booking-backend-abc123-uc.a.run.app
```

### 15. Run the Frontend Locally with Expo
```
npx expo start
```

### Security Recommendation
#### Use Secret Manager for Sensitive Data
Store sensitive variables in Google Cloud Secret Manager:
```
echo mongodb+srv://CampusMind:YEMlXkgb8Q2fXiNX@cluster0.qwhir.mongodb.net/newFrontEnd | gcloud secrets create mongodb-url-secret --data-file=- --replication-policy automatic
echo mind | gcloud secrets create jwt-key-secret --data-file=- --replication-policy automatic
echo 189abd5e-3061-4116-8bec-daf84d6accbc | gcloud secrets create postmark-api-key-secret --data-file=- --replication-policy automatic
```

Update the deploy command in step 10:
```
gcloud run deploy booking-backend `
  --image gcr.io/new-campusmind-project/booking-backend `
  --platform managed `
  --region us-central1 `
  --allow-unauthenticated `
  --set-env-vars MONGODB_URL=sm://new-campusmind-project/mongodb-url-secret `
  --set-env-vars JWT_KEY=sm://new-campusmind-project/jwt-key-secret `
  --set-env-vars POSTMARK_API_KEY=sm://new-campusmind-project/postmark-api-key-secret `
  --set-env-vars NODE_ENV=production
```

#### Notes
MongoDB Atlas Access: Ensure MongoDB Atlas allows connections from Cloud Run (set Network Access to “Allow Access from Anywhere” or whitelist the egress IP, though Cloud Run IPs are dynamic).  
Costs: Monitor costs in the Google Cloud Console (https://console.cloud.google.com/billing). Your usage should be within the free tier for low-traffic testing.  
Troubleshooting: If the deployment fails, check the logs in the Google Cloud Console and share the error messages for further assistance.  
