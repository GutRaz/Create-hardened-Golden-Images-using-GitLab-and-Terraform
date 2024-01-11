# Create hardened Windows (DSC) and Linux (Ansible) Golden Images using GitLab and Terraform

Pipeline is done in GitLab with 2 stages using a local runner
1) pre-job
   -> check if the infrastructure is available
   -> if not available create the required infrastructure using Terraform modules
                       create -> a storage account
                              -> a container
                              -> shared image gallery
                              -> 3 image definitions where we keep the versioned GI (Windows, Ubuntu, RedHat)
   -> store the terraform state file in the azure container

3) create hardened GI stage
  -> here we take images from Microsoft transform those into GI and using provisioners perform the hardening operations
     * on Windows the hardening is done using PowerShell DSC
        ** also, here WinRM setup -> is being performed
                                  -> at the end the setup is being reset so that the GI remains clean
     * on RedHat the hardening is done using Ansible roles, I have performed here only some checks and some small hardening actions
  -> also, here versioning of the images is being performed, momentarily by increasing the minor with one. The logic is easy and also it can be added the option to increase the patch and also the major version either manual my modifying the version file directly in the azure container or automatically based on condition
  -> at the end the files that keep track of the image version are being uploaded to the container
