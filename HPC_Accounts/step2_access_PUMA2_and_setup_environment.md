# **Background:**  
## what is PUMA2?
The PUMA2 service was setup by NCAS-CMS (National Centre for Atmospheric Science – Computational Modelling Services) to provide the UK research community with centralised access to the **UM（unified model）** on ARCHER2 and other national HPCs. It hosts mirrors of the Met Office Science Repositories and provides access to **Rose & Cylc** enabling submission of model simulations to ARCHER2 and other machines.   
## what is the next step?  
Access the PUMA2 from the ARCHER2 login nodes, and setup the environment.

# **Official version:**  
visit:https://ncas-cms.github.io/um-training/getting-setup-selfstudy.html

# **Personal version**

1. Set up your ARCHER2 environment:  
   ```bash
   archer2$ cp /work/y07/shared/umshared/um-training/rose-profile ~/.profile  
2. Change the permissions on your /home and /work directories to enable the NCAS-CMS team to help with any queries: 
   ```bash
   chmod -R g+rX /home/n02/n02/<your-username>  
   chmod -R g+rX /work/n02/n02/<your-username>
3. require the access to visit puma2 on the SAFE system. click the `*add machine*` button on the `*login accounts*` page.
4. Access the PUMA2 by the command below:  
   ```bash
   archer2$ ssh -Y puma2
   ```

   To access the PUMA2 from ARCHER2 without a password, you should first generate your SSH key on the archer2 login nodes:  
   ```bash
   archer2$ ssh-keygen -t rsa -f ~/.ssh/id_rsa_puma2
   ```

   Copy the key over to PUMA2:
   ```bash
   archer2$ ssh-copy-id -i ~/.ssh/id_rsa_puma2 puma2
   ```
   Type in your ARCHER2 password when prompted.

   Next, create a file called ~/.ssh/config (if it doesn’t already exist), and add the following lines:
   ```bash
   Host puma2
   IdentityFile ~/.ssh/id_rsa_puma2
   ForwardX11 yes
   ```
5. Set up your PUMA2 environment
   Copy our standard .profile and .bashrc files:
   ```bash
   puma2$ cd
   puma2$ cp ~um1/um-training/puma2/.bash_profile .
   puma2$ cp ~um1/um-training/puma2/.bashrc .
   ```
   logout and login agian to pick up these changes. You should be prompted for your Met Office Science Repository Service (MOSRS) password, then username.
6. Set up your ssh-agent
  In order to submit jobs to ARCHER2 from PUMA2, you will need to set up an ssh-agent and use it to cache the passphrase to your ARCHER2 key.
    1. Copy your ARCHER2 ssh-key pair to PUMA2
       On the **eocene** server or the other you used to login ARCER2 at step1, run the following command:
       ```bash
       scp -i ~/.ssh/id_rsa_archer2 ~/.ssh/id_rsa_archer2* <archer2-username>@login.archer2.ac.uk:/home/n02/n02-puma/<archer2-username>/.ssh
       ```
    2. Start up your ssh-agent
       First copy the ssh-setup script to your .ssh/ directory.
       ```bash
       puma2$ cp ~um1/um-training/setup/ssh-setup ~/.ssh
       ```
       Next log out of PUMA2 and back in again to start up the ssh-agent process. You should see the following message
    3. Add your ARCHER2 key
       Add your ARCHER2 key to the ssh-agent, by running
       ```bash
       puma2$ ssh-add ~/.ssh/id_rsa_archer2
       ```
       The ssh-agent will continue to run even when you log out of PUMA2. However, it may stop from time to time, for example if PUMA2 is rebooted. For instructions on what to do in this situation see [Restarting your ssh agent](https://ncas-cms.github.io/um-training/ssh-tasks.html#restarting-agent) in the Appendix.
    4. Configure access to the ARCHER2 login nodes
       Create a file ~/.ssh/config (if it doesn’t already exist), and add the following lines:
       ```bash
       # ARCHER2 login nodes
       Host ln*
       IdentityFile ~/.ssh/id_rsa_archer2
       ```
    5. Verify the setup is correct
       To test this is all working correctly, run:
       ```bash
       puma2$ rose host-select archer2
       ```
       This should return one of the login nodes, e.g. ln01. If it returns a message like [WARN] ln03: (ssh failed) then something has gone wrong with the ssh setup.
## Restarting your ssh agent after the reboot of archer2
Maybe just run the command below：
```bash
ssh-add ~/.ssh/id_rsa_archer2
```
Or visit the link below for a detailed guidance:    
https://ncas-cms.github.io/um-training/ssh-tasks.html#restarting-agent





   
