# Part 4: Preparing for the VMWare Environment  

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
  hammer subnet create --name operations_subnet \
  --locations moline \
  --organizations "Operations Department \
  --domains example.com \
  --network 10.1.10.0 \
  --mask 255.255.255.0 \
  --dns-primary 10.1.10.254 \
  --from 10.1.10.149 \
  --to 10.1.10.199 \
  --dns sat01.example.com \
  --dhcp sat01.example.com \
  --boot-mode DHCP \
  --ipam DHCP
```


## References  
[Installing Satellite Server from a Connected Network](https://access.redhat.com/documentation/en-us/red_hat_satellite/6.9/html/installing_satellite_server_from_a_connected_network/index)   
[Simple Content Access](https://access.redhat.com/articles/simple-content-access)  
[Provisioning VMWare using userdata via Satellite 6.3-6.6](https://access.redhat.com/blogs/1169563/posts/3640721)  
[Understanding Red Hat Content Delivery Network Repositories and their usage with Satellite 6](https://access.redhat.com/articles/1586183)
