---
title: "Master Microsoft Teams Administration with PowerShell: Step-by-Step Guide - Part 1 of 3: Managing Teams"
datePublished: Fri Oct 20 2023 10:04:32 GMT+0000 (Coordinated Universal Time)
cuid: clnyg2bv800020amc0sd2469q
slug: master-microsoft-teams-administration-with-powershell-step-by-step-guide-part-1-of-3-managing-teams

---

As a Microsoft Teams administrator, staying on top of the daily maintenance and management tasks can be challenging. Managing users, creating teams, and keeping track of security permissions all take time to manage properly. Thankfully, there are automated ways to manage these tasks quickly and efficiently with PowerShell scripting and Graph API techniques. In this blog, we will explore how you can leverage tools like Windows PowerShell or Azure CLI combined with the Graph API to make efficient administration of your Microsoft Teams environment easier than ever before.

In this guide, I have provided two types of scripts for each action: basic and advanced. The aim of offering these two variations is to accommodate various levels of scripting proficiency and use-case complexity.

### **Basic Scripts**

The basic scripts are straightforward and easy to understand, designed for less complex tasks or beginners. These scripts perform the core operations using minimal code, offering a simple yet effective way to manage your Microsoft Teams.

### **Advanced Scripts**

On the other hand, the advanced scripts follow best practices in scripting, including error handling, validation, function creation, and more. They are designed for more complex tasks or users with intermediate to advanced PowerShell knowledge.

**Error Handling:** This helps to catch and handle errors that occur during script execution. It's essential for maintaining the integrity of your data and systems.

**Validation:** This ensures that the input to the script meets specific criteria before the script is run, helping to avoid errors and unexpected results.

**Function Creation:** Functions let you modularize and reuse code, making your scripts more efficient and easier to maintain.

The advanced scripts offer a more robust and comprehensive approach to managing your Microsoft Teams. Whether you're using basic or advanced scripts, remember that the key to successful scripting lies in understanding your needs, continuously learning, and adapting your scripts to meet those needs.

## Create a new team:

When creating a team using PowerShell, you need to specify the following properties:

* **Display Name:** This is the name of the team that will be displayed in the Teams app.
    
* **Description:** This property provides additional information about the team and its purpose.
    
* **Visibility:** You can choose whether the team is private or public (In case you want to create a team with an OrgWide template you need to create a Microsoft Teams app. Private teams are only visible to the team members, while public teams are visible to all users within an organization and can be joined by anyone. If the `Visibility` parameter is not specified, then the team will be created with the default visibility setting, which is "Private".
    
* **Owners:** This is an array of user UPNs (user principal names) that represent the owners of the team. The owners have full control over the team and its settings.
    
    When creating a team using PowerShell, the `Owners` parameter is optional. If the `Owners` parameter is not specified, then the user who is executing the script will be set as the owner of the team by default.
    
* **Members:** This is an array of user UPNs that represent the members of the team. Members have access to the team and its resources.
    
* **Member Access:** You can choose whether members can add new members or not.
    
* **Group ID:** This is a unique identifier for the team and is generated automatically when the team is created if this property is not specified.
    

### Basic Script Example:

The easiest way to create a new team is by using the `New-Team` cmdlet in PowerShell.

Microsoft Official Documentation: [Creating a New Team](https://learn.microsoft.com/en-us/powershell/module/teams/new-team?view=teams-ps)

```powershell
# Load the Microsoft Teams module
Import-Module MicrosoftTeams

# Connect to the Microsoft Teams
Connect-MicrosoftTeams

# Define the parameters for the new team
$teamName = "MyTeam"
$description = "MyTeam Description"
$visibility = "Public" # Options: Public or Private
$allowCreatePrivateChannels = $false # Options: True or False
$owner = "owner@domain.com" # User Principal Name of the owner
$member = "member@domain.com" # User Principal Name of the member
$channelName = "newChannel"

# Create the new team
$newteam = New-Team -DisplayName $teamName -Description $description -AllowCreatePrivateChannels $allowCreatePrivateChannels -Visibility $visibility -Owner $owner

# Formatting the newly created team details as a table 
$newTeam | Select-Object GroupId, DisplayName, Description, Visibility, AllowCreatePrivateChannels | Format-Table -AutoSize

# Add the member to the team
Add-TeamUser -GroupId $newTeam.GroupId -User $member -Role 'Member' # Role property options: 'Member' or 'Owner'

# Create a new channel
New-TeamChannel -GroupId $newTeam.GroupId -DisplayName $channelName -Description "Description of the channel"

# Disconnect
Disconnect-MicrosoftTeams
```

### Advanced Script Example:

For more complex needs, it's wise to follow best practices in scripting. This includes error handling, validation, function creation, and more:

```powershell
<#
    .TITLE
        Create a New Microsoft Teams Team

    .DESCRIPTION
        This script creates a new Microsoft Teams team.

    .PARAMETERS
        - teamName: The display name of the new team. It must contain only alphanumeric characters and spaces.
        - description: The description of the new team.
        - allowDeleteChannels: Boolean value that specifies whether delete channels are allowed. Default value is 'True'.
        - visibility: The visibility of the team. When using PowerShell, you can only choose 'Public' or 'Private'.
        - ownerUserIds: An array of owner user IDs (email addresses) to be added to the team.
        - memberUserIds: An array of member user IDs (email addresses) to be added to the team.

    .VALIDATION
        1) The teamName parameter is validated using the pattern "^[a-zA-Z0-9\s]+$". This ensures that they contain only:
        - Letters (both uppercase and lowercase)
        - Numbers
        - Whitespace characters

    .DEPENDENCIES
        - PowerShell 5.0 or higher.
        - MicrosoftTeams: This module is required to interact with Microsoft Teams through PowerShell.
    
    .LINK
        Official documentation for Microsoft Teams PowerShell: https://learn.microsoft.com/en-us/powershell/teams/overview
#>

#region ==============================================================
# Check if the Teams module is installed and Import the Teams module
# ====================================================================

if (-not (Get-Module -ListAvailable -Name 'MicrosoftTeams')) {
    $message = "MicrosoftTeams module is not installed. Do you want to install it now?"
    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", "Installs the MicrosoftTeams module."
    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", "Does not install the MicrosoftTeams module."
    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

    $result = $host.ui.PromptForChoice($null, $message, $options, 0)

    if ($result -eq 0) {
        Install-Module -Name MicrosoftTeams -Force -Scope CurrentUser
    }
    else {
        Write-Error "MicrosoftTeams module is required to run this script. Please install it and rerun the script."
        return
    }
}

# Import the Teams module
Import-Module MicrosoftTeams

#endregion

#region ==========================================
# Function Definitions
# ================================================

# Function to connect to Microsoft Teams
function Connect-ToTeams {
    try {
        Write-Output "Connecting to Microsoft Teams..." # Output line before connecting
        Connect-MicrosoftTeams
        Write-Output "Connected to Microsoft Teams."
    }
    catch {
        Write-Error "Failed to connect to Microsoft Teams. Please ensure you have the necessary permissions."
        return
    }
}

# Function to add an owner to a Microsoft Teams team
function Add-OwnersToTeam {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [string]$teamId,
        
        [Parameter(Mandatory = $true)]
        [string[]]$ownerIds
    )
    foreach ($ownerId in $ownerIds) {
        try {
            Add-TeamUser -GroupId $teamId -User $ownerId -Role Owner
            Write-Output "Successfully added user '$ownerId' as an owner to the team with GroupId '$teamId'."
        }
        catch {
            Write-Error "Failed to add user '$ownerId' as an owner to the team with GroupId '$teamId'. Error: $_"
            return
        }
    }
}

# Function to add members to a Microsoft Teams team
function Add-MembersToTeam {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [string]$teamId,
        
        [Parameter(Mandatory = $true)]
        [string[]]$memberIds
    )
    
    foreach ($memberId in $memberIds) {
        try {
            Add-TeamUser -GroupId $teamId -User $memberId -Role Member 
            Write-Output "Added user '$memberId' as a member to the team with GroupId '$teamId'."
        }
        catch {
            Write-Error "Failed to add user '$memberId' as a member to the team with GroupId '$teamId'. Error: $_"
        }
    }
}

# Function to create a new Microsoft Teams team
function New-MSTeam {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [ValidatePattern("^[a-zA-Z0-9\s]+$")]
        [string]$teamName,

        [Parameter(Mandatory = $false)]
        [string]$description,

        [Parameter(Mandatory = $false)]
        [bool]$allowDeleteChannels = $true, # Default value is 'True'

        [Parameter(Mandatory = $false)]
        [ValidateSet("Public", "Private")]
        [string]$visibility = 'Public', # Default value is 'Public'

        [Parameter(Mandatory = $false)]
        [string[]]$ownerUserIds,

        [Parameter(Mandatory = $false)]
        [string[]]$memberUserIds
    )

    # Connect to Microsoft Teams
    Connect-ToTeams

    try {
        # Create the new team
        Write-Output "Creating team $teamName..."
        $newTeam = New-Team -DisplayName $teamName -Description $description -AllowDeleteChannels $allowDeleteChannels -Visibility $visibility -ErrorAction Stop
       
        # Formatting the newly created team details as a table 
        $newTeam | Select-Object GroupId, DisplayName, Description, Visibility, AllowDeleteChannels | Format-Table -AutoSize
        Write-Output "Team '$teamName' created successfully."
    }
    catch {
        Write-Error "Failed to create team with name $teamName. Error: $_"
        return
    }

    # Add owner if specified
    if ($null -ne $ownerUserIds -and $ownerUserIds.Count -gt 0) {
        Add-OwnersToTeam -teamId $newTeam.GroupId -ownerIds $ownerUserIds
    }

    # Add members if specified
    if ($null -ne $memberUserIds -and $memberUserIds.Count -gt 0) {
        Add-MembersToTeam -teamId $newTeam.GroupId -memberIds $memberUserIds
    }
}
#endregion

#region ==========================================
# Main Execution Block
# ================================================

# Variable Initialization
$teamName = "MyTeam"
$teamDescription = "This is my new team created by PowerShell script"
$deleteChannelsAllowed = $false
$teamVisibility = 'Public'
$teamOwners = @("owner1@example.com", "owner2@example.com")
$teamMembers = @("member1@example.com", "member2@example.com")

# Create a new team
New-MSTeam -teamName $teamName -description $teamDescription -allowDeleteChannels $deleteChannelsAllowed -visibility $teamVisibility `
    -ownerUserIds $teamOwners `
    -memberUserIds $teamMembers

#endregion

# Disconnect
Disconnect-MicrosoftTeams
```

## **Retrieving a Specific Team in Microsoft Teams:**

### **Basic Script:**

To retrieve a specific team, you can use the `Get-Team` cmdlet with the team's ID.

```powershell
# Load the Microsoft Teams module
Import-Module MicrosoftTeams

# Connect to the Microsoft Teams service
Connect-MicrosoftTeams

# Define the display name of the team to retrieve
$teamDisplayName = "MyTeam"

# Get the team by its display name
$team = Get-Team -DisplayName $teamDisplayName 

if ($null -eq $team) {
    Write-Host "No team found with the display name $teamDisplayName." -ForegroundColor Red
}
else {
    Write-Host "Team retrieved successfully." -ForegroundColor Green
    # Display the retrieved team information
    Write-Host "Displaying the retrieved team information:" -ForegroundColor Yellow
    $team | Format-Table -Property DisplayName, GroupId, Visibility, Description -AutoSize
}

# Disconnect
Disconnect-MicrosoftTeams
```

### **Advanced Script:**

If you want to retrieve more detailed information about the team, you can pipe the output to the `Format-List` cmdlet.

```powershell
<#
    .TITLE
        Get Microsoft Teams Team Details

    .DESCRIPTION
        This script retrieves the details of a specified Microsoft Teams team by its display name.

    .PARAMETERS
        - teamName: The display name of the team. It must contain only alphanumeric characters and spaces.

    .FUNCTIONS
        - Connect-ToTeams: Connects to the Microsoft Teams service.
        - Get-MSTeam: Retrieves the details of the specified Microsoft Teams team by its display name.

    .VALIDATION
       1) The teamName parameter is validated using the pattern "^[a-zA-Z0-9\s]+$". This ensures that it contains only:
        - Letters (both uppercase and lowercase)
        - Numbers
        - Whitespace characters

    .USER INTERACTION
        Prompts the user to install the MicrosoftTeams module if it is not found on the system.

    .DEPENDENCIES
        - PowerShell v5.1 or later.
        - MicrosoftTeams PowerShell module.

    .WORKFLOW
        1) Check if the MicrosoftTeams module is installed; prompt for installation if not found.
        2) Import the MicrosoftTeams module.
        3) Define functions for connecting to Microsoft Teams and retrieving team details.
        4) Connect to Microsoft Teams.
        5) Call the Get-MSTeam function with the specified team name.
        6) Retrieve and display the team's details (DisplayName, GroupId, Description).
        7) Disconnect from Microsoft Teams.

    .LINK
        Official documentation for Microsoft Teams PowerShell: https://learn.microsoft.com/en-us/powershell/teams/overview
#>

# Suppress the progress bar to prevent it from continuing to run after the script has finished

$ProgressPreference = 'SilentlyContinue'

#region ==============================================================
# Check if the Teams module is installed and Import the Teams module
# ====================================================================

if (-not (Get-Module -ListAvailable -Name 'MicrosoftTeams')) {
    $message = "MicrosoftTeams module is not installed. Do you want to install it now?"
    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", "Installs the MicrosoftTeams module."
    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", "Does not install the MicrosoftTeams module."
    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

    $result = $host.ui.PromptForChoice($null, $message, $options, 0)

    if ($result -eq 0) {
        Install-Module -Name MicrosoftTeams -Force -Scope CurrentUser
    }
    else {
        Write-Error "MicrosoftTeams module is required to run this script. Please install it and rerun the script."
        return
    }
}

# Import the Teams module
Import-Module MicrosoftTeams

#endregion

#region ==========================================
# Function Definitions
# ================================================

# Function to connect to Microsoft Teams
function Connect-ToTeams {
    try {
        Write-Output "Connecting to Microsoft Teams..." # Output line before connecting
        Connect-MicrosoftTeams
        Write-Output "Connected to Microsoft Teams."
    }
    catch {
        Write-Error "Failed to connect to Microsoft Teams. Please ensure you have the necessary permissions."
        throw
    }
}

# Function to get Microsoft Teams team details
function Get-MSTeam {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [ValidatePattern("^[a-zA-Z0-9\s]+$")]
        [string]$teamName
    )

    # Connect to Microsoft Teams
    Connect-ToTeams

    try {
        # Get the team details
        Write-Output "Getting team $teamName..."
        $team = Get-Team -DisplayName $teamName

        # Check if the team was found
        if ($null -eq $team) {
            Write-Host "No team found with the display name $teamDisplayName." -ForegroundColor Red
        }
        else {
            Write-Host "Team retrieved successfully." -ForegroundColor Green
            Write-Output "Details for team '$teamName':"
            
            # Display the retrieved team information
            Write-Host "Displaying the retrieved team information:" -ForegroundColor Yellow
            $team | Format-Table -Property DisplayName, GroupId, Visibility, Description -AutoSize
        }
    }
    catch {
        Write-Error "Failed to find team with name $teamName."
        return
    }
}
#endregion

#region ==========================================
# Main Execution Block
# ================================================

# Variable Initialization
$teamName = "MyTeam"

# Get details for a team with the name "MyTeam"
Get-MSTeam -teamName $teamName

#endregion

# Disconnect
Disconnect-MicrosoftTeams
Write-Output "Disconnected from Microsoft Teams."
```

## **Updating a Team in Microsoft Teams**

### **Basic Script:**

To update a team, you can use the `Set-Team` cmdlet with the desired parameters.

```powershell
# Define variables
$teamDisplayName = "MyTeam"
$newDescription = "MyTeam Updated Team Description"
$newVisibility = "Public" # Options: Public, Private

# Load the Microsoft Teams module
Import-Module MicrosoftTeams

# Connect to the Microsoft Teams service
Connect-MicrosoftTeams

# Get the team by its display name
$team = Get-Team -DisplayName $teamDisplayName

# Update the team information
$team | Set-Team -Description $newDescription -Visibility $newVisibility

# Get and display the updated team information
$updatedTeam = Get-Team -DisplayName $teamDisplayName
$updatedTeam | Format-Table -Property DisplayName, GroupId, Visibility, Description -AutoSize

# Disconnect from Microsoft Teams
Disconnect-MicrosoftTeams
```

### **Advanced Script:**

For a more comprehensive update, you can change multiple properties at once.

```powershell
<#
    .Script Name
       Update-MSTeam

    .SYNOPSIS
        Master Microsoft Teams Administration with PowerShell: Step-by-Step Guide.
        Part 1 of 3: Managing Teams.

    .DESCRIPTION
        This script updates the specified Microsoft Teams team's display name and description.

    .DEPENDENCIES
        - MicrosoftTeams: This module is required to interact with Microsoft Teams through PowerShell. The script checks for the presence of this module and prompts the user to install it if missing.
    
    .PARAMETERS
        - teamName: The current display name of the team. It must contain only alphanumeric characters and spaces (Mandatory).
        - newTeamName: The new display name of the team. It must contain only alphanumeric characters and spaces (Mandatory).
        - description: The new description for the team (Optional).
        - allowDeleteChannels: Whether members are allowed to delete channels (Optional).
        - ownerUserId: The user ID of the new owner for the team (Optional).
        - memberUserIds: The member user IDs of the members which they have to be added to the team (Optional).

    .VALIDATION
       1) The teamName and newTeamName parameters are validated using the pattern "^[a-zA-Z0-9\s]+$". This ensures that they contain only:
        - Letters (both uppercase and lowercase)
        - Numbers
        - Whitespace characters

    .WORKFLOW
        1. Check if the MicrosoftTeams module is installed, and prompt to install if not.
        2. Import the MicrosoftTeams module.
        3. Connect to Microsoft Teams.
        4. Retrieve the specific team by display name or GroupId.
        5. Update the Microsoft Teams team with the new display name, description, visibility, and other optional parameters.
        6. Add owner and members to the team.
        7. Verify the changes and provide the updated team details.
        8. Disconnect from Microsoft Teams.

    .LINK
        Official documentation for Microsoft Teams PowerShell: https://learn.microsoft.com/en-us/powershell/teams/overview
        Blog Post: [Master Microsoft Teams Administration with PowerShell: Step-by-Step Guide - Part 1  1/3 Managing Teams](URL_TO_YOUR_BLOG_POST)

#>

#region Check if the Teams module is installed
if (-not (Get-Module -ListAvailable -Name 'MicrosoftTeams')) {
    $message = "MicrosoftTeams module is not installed. Do you want to install it now?"
    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", "Installs the MicrosoftTeams module."
    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", "Does not install the MicrosoftTeams module."
    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

    $result = $host.ui.PromptForChoice($null, $message, $options, 0)

    if ($result -eq 0) {
        Install-Module -Name MicrosoftTeams -Force -Scope CurrentUser
    }
    else {
        Write-Error "MicrosoftTeams module is required to run this script. Please install it and rerun the script."
        return
    }
}
#endregion

# Import the Teams module
Import-Module MicrosoftTeams

#region Function Definitions

# Function to connect to Microsoft Teams
function Connect-ToTeams {
    try {
        Write-Output "Connecting to Microsoft Teams..." # Output line before connecting
        Connect-MicrosoftTeams
        Write-Output "Connected to Microsoft Teams."
    }
    catch {
        Write-Error "Failed to connect to Microsoft Teams. Please ensure you have the necessary permissions."
        throw
    }
}

# Function to get Microsoft Teams team details
function Get-MSTeamByName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [ValidatePattern("^[a-zA-Z0-9\s]+$")]
        [string]$teamName
    )

    try {
        # Get the team details
        $team = Get-Team -DisplayName $teamName
        return $team
    }
    catch {
        Write-Error "Failed to find team with name $teamName."
        return
    }
}

# Function to get Microsoft Teams team details by GroupId
function Get-MSTeamById {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [string]$teamId
    )

    try {
        # Get the team details by GroupId
        $team = Get-Team -GroupId $teamId
        return $team
    }
    catch {
        Write-Error "Failed to find team with GroupId $teamId."
        return
    }
}

# Function to add an owner to a Microsoft Teams team
function Add-OwnerToTeam {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [string]$teamId,
        
        [Parameter(Mandatory = $true)]
        [string]$userId
    )
    
    try {
        Add-TeamUser -GroupId $teamId -User $userId -Role Owner
        Write-Output "Successfully added user '$userId' as an owner to the team with GroupId '$teamId'."
    }
    catch {
        Write-Error "Failed to add user '$userId' as an owner to the team with GroupId '$teamId'. Error: $_"
        return
    }
}

# Function to add members to a Microsoft Teams team
function Add-MembersToTeam {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [string]$teamId,
        
        [Parameter(Mandatory = $true)]
        [string[]]$userIds
    )
    
    foreach ($userId in $userIds) {
        try {
            Add-TeamUser -GroupId $teamId -User $userId -Role Member -ErrorAction Stop
            Write-Output "Added user '$userId' as a member to the team with GroupId '$teamId'."
        }
        catch {
            Write-Error "Failed to add user '$userId' as a member to the team with GroupId '$teamId'. Error: $_"
        }
    }
}

# Function to update a Microsoft Teams team
function Update-MSTeam {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [ValidatePattern("^[a-zA-Z0-9\s]+$")]
        [string]$teamName,

        [Parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [ValidatePattern("^[a-zA-Z0-9\s]+$")]
        [string]$newTeamName,

        [Parameter(Mandatory = $false)]
        [string]$description,

        [Parameter(Mandatory = $false)]
        [bool]$allowDeleteChannels,

        [Parameter(Mandatory = $false)]
        [string]$ownerUserId,

        [Parameter(Mandatory = $false)]
        [string[]]$memberUserIds
    )

    # Connect to Microsoft Teams
    Connect-ToTeams

    # Retrieve the team object based on the display name
    $team = Get-MSTeamByName -teamName $teamName

    # Check if the team exists
    if ($null -eq $team) {
        Write-Error "No team found with the display name '$teamName'. Update operation aborted."
        return
    }

    try {
        # Update the team's display name and description
        Set-Team -GroupId $team.GroupId -DisplayName $newTeamName -Description $description -AllowDeleteChannels $allowDeleteChannels -ErrorAction Stop

        # Get the updated team details
        $updatedTeam = Get-MSTeamById -teamId $team.GroupId
        if ($null -ne $updatedTeam) {
            Write-Output "Found team with GroupId $teamId."
        }
    
        # Check if the update was successful for all properties
        $updatesSuccessful = $true
        if ($updatedTeam.DisplayName -ne $newTeamName) { $updatesSuccessful = $false }
        if ($null -ne $description -and $updatedTeam.Description -ne $description) { $updatesSuccessful = $false }
        if ($null -ne $allowDeleteChannels -and $updatedTeam.AllowDeleteChannels -ne $allowDeleteChannels) { $updatesSuccessful = $false }
      
        if ($updatesSuccessful) {
            Write-Output "Team '$teamName' has been successfully updated with the new properties."
            # Display the updated team details in a table
            $updatedTeam | Select-Object DisplayName, Description, GroupId, Visibility, AllowDeleteChannels, Owner | Format-Table -AutoSize
        }
        else {
            Write-Error "Failed to update the team with name '$teamName' for one or more properties. Please check and try again."
        }

        # Add owner if specified
        if ($null -ne $ownerUserId) {
            Add-OwnerToTeam -teamId $team.GroupId -userId $ownerUserId
        }

        # Add members if specified
        if ($null -ne $memberUserIds -and $memberUserIds.Count -gt 0) {
            Add-MembersToTeam -teamId $team.GroupId -userIds $memberUserIds
        }

    }
    catch {
        Write-Error "Failed to find or update the team with name $teamName. Error: $_"
        return
    }
}
#endregion

# Update team properties  
Update-MSTeam -teamName "currentTeamName" -newTeamName "NewTeamName" -description "New Team Description" -allowDeleteChannels $false

<#
    Check the example below for adding owners and members
    .EXAMPLE
        Update-MSTeam -teamName "ExistingTeamName" -newTeamName "NewTeamName" -description "New Description" `
                      -ownerUserId "owner@example.com" `
                      -memberUserIds @("member1@example.com", "member2@example.com")
#>

# Disconnect
Disconnect-MicrosoftTeams
```

## **Removing a Team in Microsoft Teams**

### **Basic Script:**

To remove a team, you can use the `Remove-Team` cmdlet with the team's ID.

```powershell
# Define variables
$teamDisplayName = "MyTeam"

# Load the Microsoft Teams module
Import-Module MicrosoftTeams

# Connect to the Microsoft Teams service
Connect-MicrosoftTeams

# Get the team by display name
$team = Get-Team -DisplayName $teamDisplayName

# Delete the team
Remove-Team -GroupId $team.GroupId 

# Disconnect
Write-Host "Disconnecting from Microsoft Teams..." -ForegroundColor Yellow
Disconnect-MicrosoftTeams
Write-Host "Disconnected." -ForegroundColor Green
```