# Part 4: Preparing for VMWare Integration  

[Tutorial Menu](https://github.com/pslucas0212/RedHat-Satellite-VM-Provisioning-to-vSphere-Tutorial)  

In this tutorial we will finish setting up both Satellite and VMWare for provisioning VMs from Satellite.  Throughout the tutorial we will mixu up the using the Satellite console GUI and the command line so that you get familiar with using both.

When we installed and configured Satellite, the domain example.com was added to the default organization and location.  We need to add the domain to the Operations Department organizaiton and the moline location.  

On the Satellite Console chose Administer -> Locations from the left side navigation bar.
![Administer -> Locations](/images/sat34.png)  

On the Locations page, click the moline link.  

![Locations -> moline](/images/sat35.png)  

On the Locations > Edit moline page, chose Domains and the click example.com in the Select domains list.  

![Domains -> Select domains list](/images/sat36.png)

The example.com domain will move from the All items to the Selected itmes list.  Click the blue Submit button.

![Click blud Submit button](/images/sat37.png)  

We need to add the example.com domain to the Operations Department organziation.  We will do this via the command line.  First we need ID for the Operations Derpartment.

```
# hammer organization list
---|-----------------------|-----------------------|-------------|---------------------
ID | TITLE                 | NAME                  | DESCRIPTION | LABEL               
---|-----------------------|-----------------------|-------------|---------------------
1  | Default Organization  | Default Organization  |             | Default_Organization
3  | Operations Department | Operations Department |             | operations          
---|-----------------------|-----------------------|-------------|---------------------
```  

Now let's add the example.com domain to the Operations Department Organization
```
# hammer organization add-domain --domain example.com --id 3
The domain has been associated.
```  

You will now see the update in the Red Hat Satellite console.  Do you remember how to navigate to Organization page for the Operations Department organziation?  Follow the same steps above that we used to naviage to the moline locations page.  

Here's a reminder for you how to navigate in the Satellite console Domains selection for the Operations Department organization.
1. Chose Administer -> Organizations from the left side navigation bar.
2. On the Organziations page, click the Operations Department link.
3. Click the Domains option on the Organziations > Edit Operations Department page.
4. You will see that the example.com domain is now in the Selected items list.  

![Operations Domain | Selected items list](/images/sat38.png)

Next we will create a provisioning subnet for Operations Department organziation and the moline location.  Remember that for this lab and lab environment we are using Satellite installed DHCP and DNS services on the same server where Satellite is running.
```
# hammer subnet create --name sn-operations-department \
--locations moline \
--organizations "Operations Department" \
--domains example.com \
--network 10.1.10.0 \
--mask 255.255.255.0 \
--gateway 10.1.10.1 \
--dns-primary 10.1.10.254 \
--from 10.1.10.149 \
--to 10.1.10.199 \
--dns sat01.example.com \
--dhcp sat01.example.com \
--boot-mode DHCP \
--ipam DHCP
Subnet created.
```

We can view the newly created subnet by clicking going to the left navigation bar and click Infrastructure -> Submnets.  If you don't see the newly create subnet, it's probably because you have not chosen Operations Department for the organization and moline for the location.  Check the top of the Satellite console.

![Infrastructure -> Subnets](/images/sat39.png)  

If you want to view the details of the subnet we just created, you can click on the sn-operations-department link on the Subnets page.  

![Subnets page](/images/sat40.png)

Next we will create a compute resource which allows Satellite to communicate with your vMware EXSi environemtn's vCenter.  Before creating the compute resource, you will need to create a vCenter user with permissions.  Read this article for details regarding the user premissions needed for Satellite to communicate to vCenter - [What user permissions/roles are required for the VMware vCenter user account to provision from Satellite 6.x?](https://access.redhat.com/solutions/1339483).  

After you've completed creating your vCenter user id and password, we will create the compute resource from the Satellite console.  Using the navigation bar on the left of the Satellite console, click Infrastructure -> Compute Resources.  

![Infrastructure -> Compute Resources](/images/sat41.png)  

On the Compute Resources page, click the blue Create Compute Resources button.  Make sure that you have selected Operations Department for the organization and moline for the location.   

![Compute Resources - Create Compute Resources](/images/sat42.png)  


We will enter the following data when create a Compute Resource.  Click on the Compute Resouce tab in the Compute Resources | Create Compute Resource Screen.  On the Create Compute Sreen fill in the following values.  Note: After you enter the username and password, you will click the Load Datacenters button next to the Datacente field.  If your permissions, username and password are correct, you will be able to chose the target datacenter for your RHEL VM deployments.  The Load Datacenters button will not turn into a green Test Connection button.  Continue filling out the values in the Compute Resource Screen.  

Field Name | Value
---------- | -----
Name | cr-vcenter
Provider | VMWare
VCenter/Server | vsca01.example.com
Username | VSPHERE.LOCSAL\satadmin
Password | Passw0rd!
Datacenter | LabDatacenter
Distplay Typer | VMRC
VNC Console Passwords | uncheck
Enable Caching | check

The correct Organziaion and location should be set since we chose the choices earlier in the Satellite console,  If you want to review the settings, click on the Locations tab to make sure moline has been selected.  And then click on the Operations tab to make sure Operations Department has been selected.  

Click the blue Submit button to create our Compute Resource.  

![Create Compute Resource](/images/sat43.png)

Note: you can also use the following hammer command to set up the Compute Resource in Satellite

```
# hammer compute-resource create \
--caching-enabled 1 \
--datacenter LabDatacenter \
--name "cr-vcenter" \
--password Passw0rd\! \
--provider Vmware \
--server vsca01.example.com \
--user "VSPHERE.LOCAL\satadmin" \
--locations moline \
--organizations "Operations Department"
Compute resource created.
```

We are going pre-define hardware settings for a virtual machine in Satellite by creating a compute profile.  On the Satellite Console chose Infrastructure -> Compute Profiles.

![Infrastructure -> Compute Profiles](/images/sat44.png)

On the Compute Profiles page, click the blue Create Compute Profile button.  

![Click the blue Create Compute Profile button](/images/sat45.png)

On the Compute Profiles > Create Compute Profile, enter cp-vmware-small for the Name and click the blue Submit button.  

![Name Compute Profile](/images/sat46.png)  

On the Compute Profiles > cp-vmware-small page click the cr-vcenter link to define the new compute profile cp-vmware-small.  

![Click cr-vcenter link](/images/sat47.png)

We will now define the configuration of the RHEL VM that will be provisioned to VMWare from Satellite.  We will mostly accept default values.  Configuration changes are listed in the followng table.  

Name | Value
---- | -----
Cluster | LabCluster
Guest OS | Red Hat Enterprise Linux 8 (64 bit)
Virtual H/W version | 14 (EXSi 6.7)
Create SCSI controller | VMware Paravirtual
Datastore | LabDatastore
Thin Provision | Uncheck
NIC type | VNXNET3

The next two screen shots show what your configuration should lokk like.  Clikc the blus Submit button when you have finished with the configuration.  You will be returned to the  Compute Profiles > cp-vmware-small page.  

![cr-vmware-small config](/images/sat48.png)
![cr-vmware-small config continued](/images/sat49.png)

## References  
[Installing Satellite Server from a Connected Network](https://access.redhat.com/documentation/en-us/red_hat_satellite/6.9/html/installing_satellite_server_from_a_connected_network/index)   
[Simple Content Access](https://access.redhat.com/articles/simple-content-access)  
[Provisioning VMWare using userdata via Satellite 6.3-6.6](https://access.redhat.com/blogs/1169563/posts/3640721)  
[Understanding Red Hat Content Delivery Network Repositories and their usage with Satellite 6](https://access.redhat.com/articles/1586183). 
[CHAPTER 11. PROVISIONING VIRTUAL MACHINES IN VMWARE VSPHERE](https://access.redhat.com/documentation/en-us/red_hat_satellite/6.9/html/provisioning_guide/provisioning_virtual_machines_in_vmware_vsphere#Provisioning_Virtual_Machines_in_VMware_vSphere-Creating_a_VMware_vSphere_User)  
[What user permissions/roles are required for the VMware vCenter user account to provision from Satellite 6.x?](https://access.redhat.com/solutions/1339483)
