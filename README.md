Navisworks Auto-Save & Close
Baisch Engineering — BIM Workflow Tool

What It Does
Automatically saves and gracefully closes Navisworks on all drafter workstations at noon daily.
Timeline:

11:55 AM — Toast notification appears: "Navisworks closing in 5 minutes"
11:59 AM — Final 1-minute warning notification
12:00 PM — Auto-save triggered via Ctrl+S, then hard close

No manual IT intervention required. No admin override needed. No lost work.

Files
FilePurposeNavisworksAutoClose.ps1Main script — handles warnings, save, and closeDeploy-NavisworksTask.ps1Deployment script — registers scheduled task on each workstation

Deployment Instructions
Option A — Manual (per workstation)

Copy both scripts to a shared network folder:

   \\your-network-path\scripts\

Update $ScriptPath in Deploy-NavisworksTask.ps1 to match your network path
Update $LogPath in NavisworksAutoClose.ps1 to a writable network log folder
On each drafter workstation, run as Administrator:

powershell   PowerShell.exe -ExecutionPolicy Bypass -File "\\your-network-path\scripts\Deploy-NavisworksTask.ps1"
Option B — Group Policy (all workstations simultaneously)

Place Deploy-NavisworksTask.ps1 on a domain-accessible share
In Group Policy Management:

Create new GPO → Computer Configuration → Windows Settings → Scripts → Startup
Add Deploy-NavisworksTask.ps1 as a startup script
Link GPO to the OU containing drafter workstations


On next workstation restart the task registers automatically on all machines


Configuration Options
In NavisworksAutoClose.ps1:
powershell$NavisworksProcess = "Navisworks*"    # Process name wildcard — covers all Navisworks versions
$WarningMinutes = 5                    # Warning time before hard close
$LogPath = "\\network\logs\..."        # UPDATE: path for activity logging
In Deploy-NavisworksTask.ps1:
powershell$ScriptPath = "\\network\scripts\..."  # UPDATE: UNC path to main script
$TriggerTime = "11:55AM"               # Trigger at 11:55 for noon hard close

Logging
Every save and close event is logged with timestamp and username to $LogPath.
Log entries include:

Script start time
Number of Navisworks processes found
Save command confirmation per process
Close confirmation per process
Any errors encountered

Useful for confirming the system is working and auditing which workstations closed cleanly.

Requirements

Windows 10/11 workstations
PowerShell 5.1+ (included with all modern Windows)
Navisworks Manage or Simulate (any recent version)
Network share accessible from all workstations
One-time admin rights to register scheduled task per machine (or Group Policy deployment)


Notes

Script runs silently in the background — no command window visible to users
If Navisworks is not running at noon the script exits immediately with no impact
If Navisworks doesn't close gracefully within 10 seconds it force-kills the process
Toast notifications appear on the user's desktop regardless of what window is active
Tested process name wildcard Navisworks* covers: Navisworks Manage, Simulate, Freedom


Built for Baisch Engineering BIM workflow coordination — February 2026
