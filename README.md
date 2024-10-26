#### Terraform ####

1- Create Main.tf To Hold Resources to be Created

2- Create Providers.tf to hold prividers refrences like "azure/azapi"

3- Create Outputs.tf to configure output variables in case we need to store info regarding created resources 

4- Create Variables.tf to easealy manage configurations that my occur on our Terrafor script like region 

5- Create Service Principal on Azure to be able to use authintication without prompt by run the below Command 

"az ad sp create-for-rbac --name testSP --role Contributor --scopes /subscriptions/<AZ-Subscription-ID> "

6-  Azure CLI Login run "az login --service-principal -u ${{ secrets.SPAPPID }} -p ${{ secrets.SPPassword }} --tenant ${{ secrets.TenantID }}"
      

7- Run terraform init -- upgrade   to inisialize terraform with option to check for latest providers 

8- Run terraform plan -out main.tfplan to make a dry run of your configuration and store the plan to file called main.tfplan

9- Run terraform apply "main.tfplan" to Create your Resources 

#### AKS Prerequists (After it is created by Terraform) ####
1- Create a yaml file called loadbalancer.yaml that holds load balancer configuration to be applied like what ports to be used 

2- Azure CLI Login with Service Principal  run:" az login --service-principal -u ${{ secrets.SPAPPID }} -p ${{ secrets.SPPassword }} --tenant ${{ secrets.TenantID }}"

3- Add AKS to CMD Contiext  run "az aks get-credentials --name ${{ secrets.AKSNAME }} --resource-group ${{ secrets.RG }}"

4- Run kubectl apply -f loadbalancer.yaml to deploy the load balancer service 

5- Get AKS SP App ID by running  "az aks show --resource-group test --name "cluster-simple-honeybee" --query identityProfile.kubeletidentity"

6- Grant App Id found in Step 5 Pullimage Access on your ACR "Azure Container Regisrty " by run  az role assignment create --assignee <APP ID> --scope "/subscriptions/<Subscibtion-ID>/resourceGroups/<RG>/providers/Microsoft.ContainerRegistry/registries/<ACR-Name>" --role acrpull

7- Create deployment.yaml to define your Deployment specs
#### Build ####
1- Run "pip install -r requirements.txt" to install requirment and Dependancies 
Note : I ran into an issue when running the app so i had to add "Werkzeug==2.2.2" to requirments 

2-  USe azure/docker-login@v1 To login into Azure Container Registery (ACR)
      with:
          login-server: ${{ secrets.AZURE_URL }}
          username: ${{ secrets.ACR_USERNAME }}
          password: ${{ secrets.ACR_PASSWORD }}


3- Build Docker image run "docker build  -t python-docker . "

4- Tag Docker Image run "docker tag python-docker dummytest.azurecr.io/python:latest"

5- Push Docker Image to ACR run : "docker push dummytest.azurecr.io/python:latest"

#### Deploy Image to AKS ####
1- Azure CLI Login with Service Principal  run:" az login --service-principal -u ${{ secrets.SPAPPID }} -p ${{ secrets.SPPassword }} --tenant ${{ secrets.TenantID }}"

2- Add AKS to CMD Contiext  run "az aks get-credentials --name ${{ secrets.AKSNAME }} --resource-group ${{ secrets.RG }}"

3- Delete Existing Pods  run  "kubectl delete -f deployment.yaml "

4- Deploy To AKS  run  "kubectl apply -f deployment.yaml"


     
       
