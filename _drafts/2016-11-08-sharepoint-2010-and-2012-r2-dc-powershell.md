```powershell
cscript scregedit.wsf /AU 4

Install-WindowsFeature `
	-Name AD-Domain-Services

	
$IP = "192.168.1.200"
$MaskBits = 24 # This means subnet mask = 255.255.255.0
$Gateway = "192.168.1.1"
$Dns = "192.168.1.200"
$IPType = "IPv4"

# Retrieve the network adapter that you want to configure
$adapter = Get-NetAdapter | ? {$_.Status -eq "up"}

# Remove any existing IP, gateway from our ipv4 adapter
If (($adapter | Get-NetIPConfiguration).IPv4Address.IPAddress) {
    $adapter | Remove-NetIPAddress -AddressFamily $IPType -Confirm:$false
}

If (($adapter | Get-NetIPConfiguration).Ipv4DefaultGateway) {
    $adapter | Remove-NetRoute -AddressFamily $IPType -Confirm:$false
}

 # Configure the IP address and default gateway
$adapter | New-NetIPAddress `
    -AddressFamily $IPType `
    -IPAddress $IP `
    -PrefixLength $MaskBits `
    -DefaultGateway $Gateway

# Configure the DNS client server IP addresses
$adapter | Set-DnsClientServerAddress -ServerAddresses $DNS

	
$password = read-host -assecurestring
	
Install-ADDSForest `
	-DomainName "azeroth.jgarfield.com" `
	-DomainNetbiosName "AZEROTH" `
	-DomainMode Win2012 `
	-ForestMode Win2012 `
	-Force `
	-SafeModeAdministratorPassword $password `
	-DatabasePath "C:\DATA" `
	-SYSVOLPath "C:\SYSVOL" `
	-LogPath "C:\LOGS" `
	-NoRebootOnCompletion

	
	
shutdown /r -t 3
	
	
	
Add-KdsRootKey -EffectiveTime ((get-date).addhours(-10))

Set-ADForest -Identity "azeroth.jgarfield.com" -UPNSuffixes @{Add="jgarfield.com"}

Rename-Computer "AZEROTH-DC"

shutdown /r -t 3



-- On WFE Machine

cscript c:\windows\system32\scregedit.wsf /AU 4
Wuauclt /detectnow

$IP = "192.168.1.201"
$MaskBits = 24 # This means subnet mask = 255.255.255.0
$Gateway = "192.168.1.1"
$Dns = "192.168.1.200"
$IPType = "IPv4"

# Retrieve the network adapter that you want to configure
$adapter = Get-NetAdapter | ? {$_.Status -eq "up"}

# Remove any existing IP, gateway from our ipv4 adapter
If (($adapter | Get-NetIPConfiguration).IPv4Address.IPAddress) {
    $adapter | Remove-NetIPAddress -AddressFamily $IPType -Confirm:$false
}

If (($adapter | Get-NetIPConfiguration).Ipv4DefaultGateway) {
    $adapter | Remove-NetRoute -AddressFamily $IPType -Confirm:$false
}

 # Configure the IP address and default gateway
$adapter | New-NetIPAddress `
    -AddressFamily $IPType `
    -IPAddress $IP `
    -PrefixLength $MaskBits `
    -DefaultGateway $Gateway

# Configure the DNS client server IP addresses
$adapter | Set-DnsClientServerAddress -ServerAddresses $DNS


Rename-Computer "AZEROTH-WFE"

shutdown /r -t 3

Add-Computer -DomainName AZEROTH -Restart

Install-WindowsFeature Application-Server, AS-NET-Framework, AS-TCP-Port-Sharing, AS-Web-Support, AS-WAS-Support, AS-HTTP-Activation, AS-Named-Pipes, AS-TCP-Activation, Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Http-Redirect, Web-Health, Web-Http-Logging, Web-Log-Libraries, Web-Request-Monitor, Web-Http-Tracing, Web-Performance, Web-Stat-Compression, Web-Dyn-Compression, Web-Security, Web-Filtering, Web-Basic-Auth, Web-Client-Auth, Web-Digest-Auth, Web-Cert-Auth, Web-IP-Security, Web-Url-Auth, Web-Windows-Auth, Web-App-Dev, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net, Web-Asp-Net45, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Mgmt-Tools, Web-Mgmt-Console, Web-Mgmt-Compat, Web-Metabase, Web-Lgcy-Scripting, Web-WMI, Web-Scripting-Tools, NET-Framework-Features, NET-Framework-Core, NET-HTTP-Activation, NET-Non-HTTP-Activ, NET-Framework-45-ASPNET, NET-WCF-HTTP-Activation45, NET-WCF-Pipe-Activation45, NET-WCF-TCP-Activation45, Windows-Identity-Foundation, PowerShell-V2, WAS, WAS-Process-Model, WAS-NET-Environment, WAS-Config-APIs
PrerequisiteInstaller.exe /SQLNCli:C:\Temp\sqlncli.msi /ChartControl:C:\Temp\MSChart.exe /Sync:C:\Temp\Synchronization.msi /FilterPack:D:\PrerequisiteInstallerFiles\FilterPack\FilterPack.msi /ADOMD:C:\Temp\SQLSERVER2008_ASADOMD10.msi /ReportingServices:C:\Temp\rsSharePoint.msi /Speech:C:\Temp\SpeechPlatformRuntime.msi /SpeechLPK:C:\Temp\MSSpeech_SR_en-US_TELE.msi /unattended
Setup.exe /config C:\Temp\config.xml



-- On APP Machine

$IP = "192.168.1.202"
$MaskBits = 24 # This means subnet mask = 255.255.255.0
$Gateway = "192.168.1.1"
$Dns = "192.168.1.200"
$IPType = "IPv4"

# Retrieve the network adapter that you want to configure
$adapter = Get-NetAdapter | ? {$_.Status -eq "up"}

# Remove any existing IP, gateway from our ipv4 adapter
If (($adapter | Get-NetIPConfiguration).IPv4Address.IPAddress) {
    $adapter | Remove-NetIPAddress -AddressFamily $IPType -Confirm:$false
}

If (($adapter | Get-NetIPConfiguration).Ipv4DefaultGateway) {
    $adapter | Remove-NetRoute -AddressFamily $IPType -Confirm:$false
}

 # Configure the IP address and default gateway
$adapter | New-NetIPAddress `
    -AddressFamily $IPType `
    -IPAddress $IP `
    -PrefixLength $MaskBits `
    -DefaultGateway $Gateway

# Configure the DNS client server IP addresses
$adapter | Set-DnsClientServerAddress -ServerAddresses $DNS


cscript c:\windows\system32\scregedit.wsf /AU 4
Wuauclt /detectnow

Rename-Computer "AZEROTH-APP"

shutdown /r -t 3

Add-Computer -DomainName AZEROTH -Restart

Install-WindowsFeature Application-Server, AS-NET-Framework, AS-TCP-Port-Sharing, AS-Web-Support, AS-WAS-Support, AS-HTTP-Activation, AS-Named-Pipes, AS-TCP-Activation, Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Http-Redirect, Web-Health, Web-Http-Logging, Web-Log-Libraries, Web-Request-Monitor, Web-Http-Tracing, Web-Performance, Web-Stat-Compression, Web-Dyn-Compression, Web-Security, Web-Filtering, Web-Basic-Auth, Web-Client-Auth, Web-Digest-Auth, Web-Cert-Auth, Web-IP-Security, Web-Url-Auth, Web-Windows-Auth, Web-App-Dev, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net, Web-Asp-Net45, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Mgmt-Tools, Web-Mgmt-Console, Web-Mgmt-Compat, Web-Metabase, Web-Lgcy-Scripting, Web-WMI, Web-Scripting-Tools, NET-Framework-Features, NET-Framework-Core, NET-HTTP-Activation, NET-Non-HTTP-Activ, NET-Framework-45-ASPNET, NET-WCF-HTTP-Activation45, NET-WCF-Pipe-Activation45, NET-WCF-TCP-Activation45, Windows-Identity-Foundation, PowerShell-V2, WAS, WAS-Process-Model, WAS-NET-Environment, WAS-Config-APIs

D:\PrerequisiteInstaller.exe /SQLNCli:C:\Temp\sqlncli.msi /ChartControl:C:\Temp\MSChart.exe /Sync:C:\Temp\Synchronization.msi /FilterPack:D:\PrerequisiteInstallerFiles\FilterPack\FilterPack.msi /ADOMD:C:\Temp\SQLSERVER2008_ASADOMD10.msi /ReportingServices:C:\Temp\rsSharePoint.msi /Speech:C:\Temp\SpeechPlatformRuntime.msi /SpeechLPK:C:\Temp\MSSpeech_SR_en-US_TELE.msi /unattended

D:\Setup.exe /config C:\Temp\config.xml



-- On SQL Machine

$IP = "192.168.1.203"
$MaskBits = 24 # This means subnet mask = 255.255.255.0
$Gateway = "192.168.1.1"
$Dns = "192.168.1.200"
$IPType = "IPv4"

# Retrieve the network adapter that you want to configure
$adapter = Get-NetAdapter | ? {$_.Status -eq "up"}

# Remove any existing IP, gateway from our ipv4 adapter
If (($adapter | Get-NetIPConfiguration).IPv4Address.IPAddress) {
    $adapter | Remove-NetIPAddress -AddressFamily $IPType -Confirm:$false
}

If (($adapter | Get-NetIPConfiguration).Ipv4DefaultGateway) {
    $adapter | Remove-NetRoute -AddressFamily $IPType -Confirm:$false
}

 # Configure the IP address and default gateway
$adapter | New-NetIPAddress `
    -AddressFamily $IPType `
    -IPAddress $IP `
    -PrefixLength $MaskBits `
    -DefaultGateway $Gateway

# Configure the DNS client server IP addresses
$adapter | Set-DnsClientServerAddress -ServerAddresses $DNS


cscript c:\windows\system32\scregedit.wsf /AU 4
Wuauclt /detectnow

Rename-Computer "AZEROTH-SQL"

shutdown /r -t 3

Add-Computer -DomainName AZEROTH -Restart




--- On Client Machine
Set-Item wsman:\localhost\Client\TrustedHosts AZEROTH-DC -Concatenate -Force
Set-Item wsman:\localhost\Client\TrustedHosts AZEROTH-WFE -Concatenate -Force
```