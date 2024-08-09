# 2801 - Deploying BloxOne Hosts
## Scenario

You're tasked to deploy two docker BloxOne hosts located in two of your corporate new offices, you have remote access to the virtual machines with docker already installed and ready for you to install the BloxOne Host image and connect it to CSP to be configured on a later date.

Estimate Completion Time
* 15 to 20 Minutes

Prerequisites
* Administrative access to the CSP

Course References
* 2161: Managing BloxOne Hosts and Services

## Tasks

1. Create a join token

2. Deploy Docker Instance on oph1

3. Deploy Docker Instance on oph2

4. Validate BloxOne Instances are connected to the CSP

### Task 1: Create a join token

* Using your lab’s **Jump-Desktop**, log into the CSP. Create a join token and save the join token as a text file using the text editor **Geany** for later use.

### Task 2: Deploy Docker Instance on oph1

* SSH into oph1 virtual machine using the IP address `10.100.0.110` on port _65522_, and the credentials of **\[training/infoblox]**

    * Once logged in, run the script named `deploy-bloxone-oph` with admin-level access and provide the join token when prompted. 

### Task 3: Deploy Docker Instance on oph2

* SSH into oph2 virtual machine using the IP address `10.200.0.110` on port _65522_, and the credentials of **\[training/infoblox]**

    * Once logged in run the script named `deploy-bloxone-oph` with admin-level access and provide the join token when prompted. 

### Task 4: Validate BloxOne Instances are connected to the CSP

* Verify both Instances are visible on CSP and both are online

    * Locate the Host with the IP address **10.100.0.110** and change its name to "oph1.techblue.net"

    * Similarly, locate the second host with the address **10.200.0.110** and change its name to "oph2.techblue.net"

**It may take up to 30 mins for the two instances to be online.**

## Solutions
### Task 1 Solution: Create a join token

1. On the _Jump-Desktop_, click on the start menu and run the application _Geany_, which we will use to save our join tokens for use in a later step.

2. Switch to your CSP browser window, navigate to **Manage → Infrastructure**, select the **Join Tokens** tab, and click on **Create**.

3. Enter a name for this join token `Techblue Token`, give the token a description, and **click Save & Close**.

4. The join token will be displayed on a pop-up window. Click **Copy** to copy the join token to the clipboard and paste it into Geany.

5. Save this document to the Desktop as join-token.txt

**Make sure you have saved the text of the join token, as the token will not be visible again. If we don't save it, we will need to revoke the token and create a new one.**

### Task 2 Solution: Deploy Docker Instance on oph1

1. On the _Jump-Desktop_, open the terminal application on your application taskbar.

2. Start a secure shell connection to the _oph1_ virtual machine on IP address _10.100.0.110_ on port _65522_ using the command `ssh -p 65522 training@10.100.0.110` and when prompted, enter the password `infoblox`.

3. Deploy the host using the command `sudo deploy-bloxone-oph` and enter the join token when prompted. Finally, answer `y` after verifying the token is correct.

### Task 3 Solution: Deploy Docker Instance on oph2

1. On the _Jump-Desktop_, open the terminal application on your application taskbar, if it is not already open.

2. Start a secure shell connection to the _oph2_ virtual machine on IP address _10.200.0.110_ on port _65522_ using the command `ssh -p 65522 training@10.200.0.110` and when prompted, enter the password `infoblox`.

3. Deploy the host using the command `sudo deploy-bloxone-oph` and enter the join token when prompted. Finally, answer ``y after verifying the token is correct.

### Task 4 Solution: Validate BloxOne Hosts are connected to the CSP
**It may take up to 30 mins for the two BloxOne Host containers to be online.**

1. Switch to your CSP browser window, and navigate to **Manage → Infrastructure → Hosts**

2. Your new BloxOne Host containers should appear in the list of hosts. If they are not visible, wait 10 seconds and refresh the browser.

3. Your BloxOne Host container status may at first indicate _Offlin_e or _Degraded_. This is normal as it comes up. You should also see the Platform Management in an _Upgrading_ state. All BloxOne Hosts are automatically upgraded to the latest version to work with the CSP. Refreshing the page periodically may show a change.

4. Then your BloxOne Host container will change from _Pending_, to _Review Details_, to _Online_. Once both of your Host's statuses are _Online_, they are ready for configuration. Refreshing the page may show a change.

5. Locate the Host with the Host IP address _10.100.0.110_ by expanding the Hosts entry in the list (the toggle is on the right-hand side).

6. Place a check in the box next to the Host and click on the **Edit** button.

7. Change the name to _oph1.techblue.net_, and then click **Finish** and **Save & Close**.

8. Locate the Host with the Host IP address _10.200.0.110_ by expanding the Hosts entry in the list (the toggle is on the right-hand side).

9. Place a check in the box next to the Host and click on the **Edit** button.

10. Change the name to _oph2.techblue.net_, and then click **Finish** and **Save & Close**.
