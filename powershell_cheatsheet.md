## PowerShell Cmdlets: The Core Commands ⚙️

A **Cmdlet** (pronounced "command-let") is a lightweight, single-function command native to the PowerShell environment. It's the fundamental building block for any action in PowerShell.

Think of cmdlets like **specialized tools in a workshop**. You have a `Get-Wrench` tool, a `Set-DrillBit` tool, and a `Start-Motor` tool. Each does one specific thing very well.

### Key Characteristics:
* **`Verb-Noun` Naming Convention:** This makes them predictable and easy to understand.
    * **Verb:** Specifies the action (e.g., `Get`, `Set`, `Start`, `Stop`, `New`, `Remove`).
    * **Noun:** Specifies the resource the action is performed on (e.g., `Process`, `Service`, `Item`, `Content`).
* **Parameters:** Cmdlets use parameters (e.g., `-Name`, `-Path`) to specify options, similar to switches in other shells (`/` or `--`).
* **Object-Based Output:** Instead of just text, cmdlets output **structured objects**. This is PowerShell's most powerful feature, allowing you to easily sort, filter, and manipulate the output by piping it to other cmdlets.

### Cmdlet Code Examples

**Example 1: Getting Information with `Get-*`**
This shows how to retrieve information about running processes on the system.
```powershell
# Get a list of all currently running processes
Get-Process

# Use a parameter to get a specific process by name
Get-Process -Name "explorer"
```

**Example 2: Taking Action with `New-*` and `Remove-*`**
This example shows how to create a new file and then delete it.
```powershell
# Create a new empty file named "log.txt" in the current directory
New-Item -Path "./log.txt" -ItemType File

# Verify that the file was created
Get-ChildItem -Path "./log.txt"

# Remove the file we just created
Remove-Item -Path "./log.txt"
```

**Example 3: The PowerShell Pipeline (`|`)**
This demonstrates the power of piping objects. The output of one cmdlet becomes the input for the next.
```powershell
# Get all running processes, sort them by CPU usage (descending),
# and then select the top 5 to display.
Get-Process | Sort-Object -Property CPU -Descending | Select-Object -First 5
```
---

## PowerShell Logic: Making Decisions and Repeating Actions 🧠

If cmdlets are the individual tools, then **logic structures** are the blueprint or instructions that tell you when and how to use those tools. These constructs allow you to move from single commands to powerful, automated scripts.

### Key Components:
* **Variables:** Store data. In PowerShell, variable names start with a dollar sign (`$`), for example, `$myVariable = "hello"`.
* **Conditional Statements (`If`/`ElseIf`/`Else`):** Make decisions and run different code based on whether a condition is true or false. PowerShell uses comparison operators like `-eq` (equal), `-ne` (not equal), `-gt` (greater than), `-lt` (less than).
* **Looping Constructs (`ForEach-Object`, `For`, `While`):** Repeat a block of code multiple times.

### PowerShell Logic Code Examples

**Example 1: Conditional Logic with `If / Else`**
This script checks the status of a specific Windows service and prints a different colored message based on the result.
```powershell
# Get the Windows Update service and store its object in a variable
$service = Get-Service -Name "wuauserv"

# Use an 'if/else' statement to check the 'Status' property of the service object
if ($service.Status -eq "Running") {
    Write-Host "The Windows Update service is currently running." -ForegroundColor Green
}
else {
    Write-Host "The Windows Update service is currently stopped." -ForegroundColor Yellow
}
```

**Example 2: Looping with `ForEach-Object`**
This is the most common "PowerShell way" to loop. It takes a collection of objects from the pipeline and performs an action on each one.
```powershell
# Get all services that start with "win" AND are currently "Stopped".
# Then, for each service found, print its details.
Get-Service -Name "win*" | Where-Object { $_.Status -eq "Stopped" } | ForEach-Object {
    # '$_' is an automatic variable representing the current object in the pipeline
    $serviceName = $_.Name
    $displayName = $_.DisplayName
    
    Write-Host "Found stopped service: '$displayName' (Name: $serviceName)"
}
```

**Example 3: A Traditional `For` Loop**
This shows a C-style `for` loop, useful when you need to repeat an action a specific number of times.
```powershell
# A traditional for loop to count down from 5
Write-Host "Starting script in..."

for ($i = 5; $i -ge 1; $i--) {
    Write-Host "$i..."
    Start-Sleep -Seconds 1 # This cmdlet pauses the script for 1 second
}

Write-Host "Launch!"
```
---
## Writing and Running PowerShell Scripts

### Where to Write Your Scripts

A PowerShell script is just a plain text file. You can write them in any text editor, but using one with PowerShell support is best.

1.  **Visual Studio Code (VS Code) (Recommended):** The modern standard. Install the official PowerShell extension for excellent syntax highlighting, code completion (IntelliSense), and debugging tools.
2.  **PowerShell ISE:** A simple editor included with Windows by default. It's good for beginners but less powerful than VS Code.
3.  **Notepad:** You can write a script in Notepad, but you won't get any special features.

No matter where you write it, you must save the file with a **`.ps1`** extension (e.g., `my_script.ps1`).

### How to Run Your Scripts

For security, PowerShell does not run scripts by default. You first need to set the **Execution Policy**.

1.  **Set the Execution Policy (One-time setup):**
    * You must do this from a **PowerShell window running as an Administrator**.
    * Right-click the Start Menu, choose "PowerShell (Admin)" or "Terminal (Admin)".
    * Run the following command to allow locally created scripts to run:
        ```powershell
        Set-ExecutionPolicy RemoteSigned
        ```
    * It will ask you to confirm; type `Y` and press Enter. You only need to do this once per machine.

2.  **Run the Script from the Terminal:**
    * Open a regular (non-admin) PowerShell terminal.
    * Use the `cd` command to navigate to the directory where you saved your `.ps1` file.
    * To run the script, you must specify its path using **`.\`** before the filename.
        ```powershell
        # Navigate to the correct folder
        cd C:\Users\YourUser\Documents\Scripts

        # Execute the script
        .\my_script.ps1
        ```
    * The `.\` is a security feature that tells PowerShell to run the script from the current directory, preventing you from accidentally running a malicious script with the same name from a different location.