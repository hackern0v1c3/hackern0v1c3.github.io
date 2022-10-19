## Powershell Tips
* `Get-Module` _List the powershell modules that are imported in current session_
* `Get-Module -ListAvailable` _List the powershell modules that can be imported_
* `Get-Command -Module {Module}` _List the available commands for a given module_
* `Import-Module -Name {Available Module}` _Import an available module for current session_
* `Import-Module -Name {File Path}` _Import a module for current session from a psm1 or ps1 file_
* `Get-Help {Command}` _Get help for a command use -Detail, -Example, or -Full for more info_
* `Get-Help *{string}*` _Use help to search for any commands that contain string. Includes modules that arent imported into session_
* `Update-Help` _Download the latest help info for commands.  Needs to be done at least once on new machines_