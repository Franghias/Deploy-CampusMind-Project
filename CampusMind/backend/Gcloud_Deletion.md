
## Steps to Stop Deployment and Avoid Charges Overnight

### 1. Delete the Cloud Run Service

Delete the Cloud Run service to stop it from running and incurring charges:

```
gcloud run services delete booking-backend --region us-central1
```

-   **Notes**:
    -   Replace booking-backend with your service name if it’s different.
    -   Replace us-central1 with the region you deployed to if it’s different (e.g., us-east1).
    -   You may be prompted to confirm the deletion (type y and press Enter).

### 2. Verify the Service is Deleted

Check that the service is no longer running:

`gcloud run services list --region us-central1`

-   **Expected Output**: The booking-backend service should not appear in the list.
-   If it’s still listed, repeat step 1 or check for errors.

### 3. (Optional) Delete Docker Images in Google Container Registry

To minimize storage costs, delete the Docker images stored in Google Container Registry (GCR):

`gcloud container images delete gcr.io/deploy-booking-app/booking-backend --force-delete-tags`

-   **Notes**:
    -   Replace deploy-booking-app with your project ID if it’s different (e.g., new-campusmind-project if you followed the earlier guide).
    -   The --force-delete-tags flag deletes all tags associated with the image (e.g., latest, v1).
    -   This reduces storage costs (though likely minimal, as storage is within the 5 GB/month free tier for a single image).

### 4. (Optional) Delete the Cloud Storage Bucket for Build Artifacts

Delete the Cloud Storage bucket created by Cloud Build to store temporary build artifacts (if you no longer need it):

`gsutil rm  -r gs://deploy-booking-app_cloudbuild`

-   **Notes**:
    -   Replace deploy-booking-app with your project ID if it’s different.
    -   This bucket stores build artifacts (e.g., source code tarballs), which are small (~150 KiB for two builds), so costs are negligible.
    -   Be cautious: If you plan to rebuild later, you may need to keep this bucket, as Cloud Build will recreate it.

### 5. Check Billing to Confirm No Active Charges

Verify that no active services are incurring charges:

-   Go to the Google Cloud Console Billing section: https://console.cloud.google.com/billing.
-   Check the **Cost Breakdown** or **Transactions** for your project (deploy-booking-app or new-campusmind-project).
-   Look for charges related to Cloud Run, Cloud Build, or Cloud Storage. After deleting the service, Cloud Run charges should stop (since it scales to zero when deleted).

## Additional Notes on Costs

-   **Cloud Run**: Charges are based on vCPU-seconds, memory-seconds, and requests. Once the service is deleted, no instances will run, and you won’t be charged for Cloud Run usage.
-   **Google Container Registry (GCR)**: Storage costs are ~$0.02/GB/month (Standard Storage, US multi-region). If your image is ~500 MB, this is ~$0.01/month, which is within the 5 GB/month free tier. Deleting the image (step 3) ensures no storage costs.
-   **Cloud Storage Bucket**: The deploy-booking-app_cloudbuild bucket stores build artifacts. If deleted (step 4), there are no storage costs for this bucket.
-   **Free Tier**: Even if you don’t delete the image or bucket, costs are likely within the free tier (5 GB/month for storage, 120 build-minutes/day for Cloud Build).
-   **MongoDB Atlas**: Your MongoDB Atlas usage (e.g., M0 free tier) is separate from Google Cloud and unaffected by these steps. Check your Atlas dashboard to confirm your plan.