# PDQDeploy-UpgradeWin10
PDQ Deployment Script to Integrate with MDT

This PDQ package will allow an administrator to perform an in-place upgrade to whichever version of Windows 10 your organization is deploying. The nice thing about this is when you have everything working, you can scale your deployment upgrades to your liking. This can save hours for your technicians who are performing this process manually. 

This package depends on the following:
1. An existing MDT environment with a working upgrade Task Sequence
2. PDQ Deploy
3. MDT Monitoring must be enabled

How the script works:
The script will stage some registry keys to enable auto login of an account. The account must have permissions to your MDT share and also be a local administrator. After staging the settings, PDQ will reboot the machine and allow auto-login to take place. It will then call Litetouch.vbs with the apropriate parameters for a silent deployment. I've found that MDT will not allow execution of Litetouch.vbs without interaction and I've been unable to find a way around that. After the upgrade is started, PDQ will use powershell via ODATA to check that the upgrade is running. It will then sleep for a few hours and check to see if it finished using the same powershell script via ODATA. 

Getting started:
Review all the steps and tweak parameters accordingly. Endpoint hardware and network bandwidth will affect the varios sleep timers I have built into the script. You'll need to adjust them accordingly. 

Step 1 - First we clean up any traces of MININT. Then adjust the domain\username & password fields to your environment. Finally edit the last line to reflect the domain\username used for auto-login. Note: this account must have permissions to your MDT Share.

Step 2 - Reboot

Step 3 - Allow time for the machine to login 

Step 4 - Edit the path your your deployment share. Edit the Task Sequence ID to reflect your Upgrade TS ID

Step 5 - Allow Litetouch.vbs to kick off and begin reporting data to the MDT Monitoring ODATA stream

Step 6 - Using Mikael Nystrom's Powershell script we check to see if the TS is running. Edit the $status line to reflect your MDT server. 

Step 7 - Sleep PDQ for a couple of hours. Tweak this to match your average upgrade time. In our environment it takes 45-90 minutes so I have some buffer in there in case. This Sleep time value must be less than your deployment timeout value in the PDQ Console > Options > Preferences > Deployments > Timeout.

Step 8 - Using Mikael Nystrom's script to check if it finished. Edit the $status line to reflect your MDT server. 

Step 9 - We clean up the registry keys and group settings set in step 1. Edit to reflect the same as Step 1.

Troubleshooting
If your timings are wrong for the sleep steps, be sure to run the re-deploy package starting at step 9 on the failed machines to ensure the cleanup takes place. 

Acknowledgements 
https://deploymentbunny.com/2016/03/07/powershell-is-king-get-mdt-monitor-data-using-the-odata-feed-using-a-powershell-function/
https://social.technet.microsoft.com/Forums/ie/en-US/d292a5f9-b979-477c-943e-df1eef1069ad/upgrading-to-windows-10-using-mdt-zero-touch?forum=mdt
