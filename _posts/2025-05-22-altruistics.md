---
title: Altruistics malware
categories:
- Malware
---

There are many names this PUP and adware goes by - Altrousik, Altruistics, Altrsik, Atuct, Almoristics, Alrustix, Alrucs, Alrustiq and more. In this post, let's consider our variant being named **Altruistics**, however if you are infected, it could be another variant of the name, not exactly Altruistics. That is why it is important to pay attention to the process, service names.

 It has been around for 3 years and it is being spread as a **bundled software**. An example of bundleware  could be **CCleaner**. Once you start the setup, it asks us if we would like to install AVG antivirus.

![img](https://github.com/rifteyy/alembic-kit/blob/master/img/altrousik/ccleaner.png?raw=true)

If we click accept, the AVG antivirus gets fully installed and started. There is no further confirming, just 1 click on the accept button.

Thankfully, though, CCleaner offers a slim version that will not ask us if we would like any toolbar/external software installed.

### How did I get infected by Altruistics?
There are several ways:
- You ran an installer that asked you if you would like to install Altruistics and you *accidentally* pressed "Accept".
- You ran a malicious installer that installed Altruistics without asking you and that makes it a **malware**.

### How do I know I have Altruistics?

You can check using Task manager, or even better by using [Process Explorer](https://learn.microsoft.com/en-us/sysinternals/downloads/process-explorer). Altruistics can be identified by:
- A process named `Altruistic App` or similiar variant using very high percentage of CPU
- Being persistent - loading up on every computer start
- Some of these folders `%systemdrive%\Program Files (x86)\AltruisticApplication`, `%systemdrive%\ProgramData\AltruisticApplication`, `%localappdata%\AltruisticApplication` are present on your system
- Unable to kill the process that is using high amount of CPU named Altruistics
- The service named usually `AltruisticService` or it's variant exists
- It may in some cases also be present in `appwiz.cpl` with the heart-shaped icon and with the name Altruistic.

![img](https://github.com/rifteyy/alembic-kit/blob/master/img/altrousik/altrousik1.png?raw=true)

### How to remove Altruistics?
#### If the Altruistics application shows up in `appwiz.cpl`:
1. Press Windows + R at once, type `appwiz.cpl` and press enter
2. Search through the programs and once you find Altruistics entry, right click on it
3. Press `Uninstall`
4. Go through the uninstallation process in the Altruistics window and remove all of it
5. If it haven't returned any errors, Altruistics is now gone.
6. I would strongly recommend figuring out what installer caused Altruistics to appear and avoid it in the future.

### If it does not show up in `appwiz.cpl`:
1. Open file explorer
2. Head into folder   `%systemdrive%\Program Files (x86)`
3. Find the folder manually, it should be something like  `<variant name>Application`, so in our case here it should be `AltruisticApplication`.
4. There should be a file named something similiar to `AltruisticsUninstaller.exe`
5. Run the file as administrator and continue through the uninstallation process in the Altruistics window.
6. If it haven't returned any errors, Altruistics is now gone.

#### If it does not show up in `appwiz.cpl` and manual uninstaller also failed:
It is surprisingly embedded further in Windows, specifically after installation it is set as a hidden and locked service.

For us, this means that administrator permissions are not enough to remove this. That is when **Sysinternals PsExec** and it's ability to start a `NT AUTHORITY/SYSTEM` command line comes in.

1. Install PsTools archive - [Download link](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec)
2. Extract it by right clicking on the `.ZIP` archive and selecting `Extract`
3. Press Windows key or open Windows search, type in `cmd.exe` and select `Run as Administrator`. If you do not run it as administrator, it won't work.
4. Now we need to change directory so we are in the same directory where PsExec is located. You can do that by entering `cd /d "path\to\<extracted PsTools folder>`. So for example, if you extracted it in downloads, it should be `cd /d "%userprofile%\Downloads\PsTools`.
5. If the previous step was successful, you will see the line from changed from `C:\Windows\System32>` to something like `C:\Users\admin\Downloads\PsTools>`.
6. Now, enter in a command `psexec.exe -i -s cmd.exe`. After pressing enter, you should get a window to confirm license agreement. Accept it and you should get a new command line window.
7. In the new command-line window verify that you are logged in as `NT AUTHORITY\SYSTEM`. Type in `whoami` to see if it returns `NT AUTHORITY\SYSTEM`. If not, check if you are not in the previous command-line window.
8. Now that we are in the system privileged command line, we need to first stop the service in order to delete all the files. Let's first find the service name! Type in `sc.exe query` and search throughout the output for something like `<variant name>Service`, so in our case it should be `AltruisticService`. Keep in mind that all the discovered variants mentioned in this post start with the letter A, so scroll up and search from top to bottom.
9. Now that we found out the service name, we need to reset it's permissions. If we attempted to stop it at this step, we would get an `Access denied.` error. We can do that using `sc.exe sdset AltruisticService D:(A;;CCLCSWLOCRRC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCLCSWLOCRRC;;;SY)` and once again replace the `AltruisticService` with the service name you found from the previous step.
10. Now, you should get a success message. If so, go ahead and stop the service using `sc.exe stop AltruisticService`. If succeed, go to the next step.
11. Since the service is stopped now, we can delete it. Type in `sc.exe delete AltruisticService`.
12. Now we successfully removed the service persistency mechanism! Let's now delete all the files and folders it created:
`del /f /s "%systemdrive%\Program Files (x86)\AltruisticApplication\*"`
`del /f /s "%systemdrive%\ProgramData\AltruisticApplication\*"`
`del /f /s "%localappdata%\AltruisticApplication"`
`rmdir /s "%systemdrive%\Program Files (x86)\AltruisticApplication"`
`rmdir /s "%systemdrive%\ProgramData\AltruisticApplication"`
`rmdir /s "%localappdata%\AltruisticApplication"`

Keep in mind to use the name we found previously. If it says that the folder(s) were not found, go into the directory using the File Explorer and delete them manually.

If all the commands did not return any error, just the list of deleted files, you successfully removed the Altruistic malware manually. Congratulations!

### Hashes and reports
https://www.virustotal.com/gui/domain/altruistics.org/relations
https://www.virustotal.com/gui/file/333552e9695517152cd1a08725964eed8c36bb4b7018b22514338d21fb7cf003
https://www.virustotal.com/gui/file/bdbf9749c53aad784de41cf89de3d345330dda145f0cc0e55dadc5be8f586d8b
https://www.virustotal.com/gui/file/e035900bc5f284f3bf4615e092d3bc8602e2e03826cc655dba0192e25f956f2d/relations

