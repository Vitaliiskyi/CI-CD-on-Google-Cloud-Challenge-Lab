## Task 2


#### Make change in this task

cd web
skaffold build --interactive=false \
--default-repo <###### Change here ############> \
--file-output artifacts.json
cd ..

----------------------------------------------------------------------------------------------------------------------------------------------------------

## Task 3

1. Run command from lab


----------------------------------------------------------------------------------------------------------------------------------------------------------



2. run this command


gcloud config set deploy/region $REGION
cp clouddeploy-config/delivery-pipeline.yaml.template clouddeploy-config/delivery-pipeline.yaml
sed -i "s/targetId: staging/targetId: cd-staging/" clouddeploy-config/delivery-pipeline.yaml
sed -i "s/targetId: prod/targetId: cd-production/" clouddeploy-config/delivery-pipeline.yaml
sed -i "/targetId: test/d" clouddeploy-config/delivery-pipeline.yaml
gcloud beta deploy apply --file=clouddeploy-config/delivery-pipeline.yaml


----------------------------------------------------------------------------------------------------------------------------------------------------------


gcloud beta deploy delivery-pipelines describe web-app



----------------------------------------------------------------------------------------------------------------------------------------------------------



#### Re run this command till status turns to running from provisioning


gcloud container clusters list --format="csv(name,status)"


----------------------------------------------------------------------------------------------------------------------------------------------------------



CONTEXTS=("cd-staging" "cd-production" )
for CONTEXT in ${CONTEXTS[@]}
do
    gcloud container clusters get-credentials ${CONTEXT} --region ${REGION}
    kubectl config rename-context gke_${PROJECT_ID}_${REGION}_${CONTEXT} ${CONTEXT}
done


----------------------------------------------------------------------------------------------------------------------------------------------------------


for CONTEXT in ${CONTEXTS[@]}
do
    kubectl --context ${CONTEXT} apply -f kubernetes-config/web-app-namespace.yaml
done



----------------------------------------------------------------------------------------------------------------------------------------------------------



envsubst < clouddeploy-config/target-staging.yaml.template > clouddeploy-config/target-cd-staging.yaml
envsubst < clouddeploy-config/target-prod.yaml.template > clouddeploy-config/target-cd-production.yaml
sed -i "s/staging/cd-staging/" clouddeploy-config/target-cd-staging.yaml
sed -i "s/prod/cd-production/" clouddeploy-config/target-cd-production.yam


----------------------------------------------------------------------------------------------------------------------------------------------------------



for CONTEXT in ${CONTEXTS[@]}
do
    envsubst < clouddeploy-config/target-$CONTEXT.yaml.template > clouddeploy-config/target-$CONTEXT.yaml
    gcloud beta deploy apply --file clouddeploy-config/target-$CONTEXT.yaml
done


----------------------------------------------------------------------------------------------------------------------------------------------------------

## Task 4

gcloud beta deploy releases create web-app-001 \
--delivery-pipeline web-app \
--build-artifacts web/artifacts.json \
--source web/


----------------------------------------------------------------------------------------------------------------------------------------------------------


## Task 5

#### promote & approve


----------------------------------------------------------------------------------------------------------------------------------------------------------

## Task 6

gcloud services enable cloudbuild.googleapis.com

----------------------------------------------------------------------------------------------------------------------------------------------------------

cd ~/
git clone https://github.com/GoogleCloudPlatform/cloud-deploy-tutorials.git
cd cloud-deploy-tutorials
git checkout c3cae80 --quiet
cd tutorials/base


----------------------------------------------------------------------------------------------------------------------------------------------------------



envsubst < clouddeploy-config/skaffold.yaml.template > web/skaffold.yaml
cat web/skaffold.yaml


----------------------------------------------------------------------------------------------------------------------------------------------------------


cd web
skaffold build --interactive=false \
--default-repo <#### Make Changes Here ####> \
--file-output artifacts.json
cd ..


----------------------------------------------------------------------------------------------------------------------------------------------------------


gcloud beta deploy releases create web-app-002 \
--delivery-pipeline web-app \
--build-artifacts web/artifacts.json \
--source web/




----------------------------------------------------------------------------------------------------------------------------------------------------------
## End of Lab







