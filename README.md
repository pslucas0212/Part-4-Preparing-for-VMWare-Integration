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




## References  
[Installing Satellite Server from a Connected Network](https://access.redhat.com/documentation/en-us/red_hat_satellite/6.9/html/installing_satellite_server_from_a_connected_network/index)   
[Simple Content Access](https://access.redhat.com/articles/simple-content-access)  
[Provisioning VMWare using userdata via Satellite 6.3-6.6](https://access.redhat.com/blogs/1169563/posts/3640721)  
[Understanding Red Hat Content Delivery Network Repositories and their usage with Satellite 6](https://access.redhat.com/articles/1586183)
