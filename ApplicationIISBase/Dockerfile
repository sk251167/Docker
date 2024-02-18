# escape=`

#========================================================================================================================#
# Begin common commands                                                                                                  #
#========================================================================================================================#

# Using Application base image
FROM applicationbase

# Download service monitor
RUN Invoke-WebRequest `
		-UseBasicParsing `
		-Uri 'https://dotnetbinaries.blob.core.windows.net/servicemonitor/2.0.1.10/ServiceMonitor.exe' `
		-OutFile 'C:\ServiceMonitor.exe';

# Add web-server
RUN Add-WindowsFeature Web-Server;
 
# Setup web administration system
RUN Import-Module 'WebAdministration'; `
	Install-WindowsFeature 'Web-Windows-Auth';

# Configure authentication for default web site `
RUN Set-WebConfigurationProperty `
		-Location 'Default Web Site' `
		-PSPath IIS:\ `
		-Filter "system.webServer/security/authentication/anonymousAuthentication" `
		-Name "enabled" `
		-Value $true; `
	# Enable Windows authentication `
	Set-WebConfigurationProperty `
		-Location 'Default Web Site' `
		-PSPath IIS:\ `
		-Filter "/system.webserver/security/authentication/windowsAuthentication" `
		-Name "enabled" `
		-Value $true;

# Create certificate and binding
RUN Import-Module WebAdministration; `
    CD CERT:; `
	$cert = New-SelfSignedCertificate `
				-DnsName localhost `
				-Friendlyname MyCert `
				-CertStoreLocation Cert:\LocalMachine\My; `
	$rootStore = New-Object `
					System.Security.Cryptography.X509Certificates.X509Store `
					-ArgumentList Root, LocalMachine; `
	$rootStore.Open('MaxAllowed'); `
	$rootStore.Add($cert); `
	$rootStore.Close(); `
	CD IIS:; `
	New-Item `
		-Path IIS:\SslBindings\0.0.0.0!443 `
		-value $cert; `
	New-WebBinding `
		-Name 'Default Web Site' `
		-IP '*' `
		-Port 443 `
		-Protocol https;
		
# Enable verbose logging for kerberos in the Windows Event Viewer
RUN New-ItemProperty `
	-Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters `
	-Name LogLevel `
	-PropertyType DWord `
	-Value 1 `
	-Force;

# Expose ports
EXPOSE 80 443 
	
#========================================================================================================================#
# End common commands                                                                                                    #
#========================================================================================================================#