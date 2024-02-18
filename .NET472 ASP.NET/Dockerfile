# escape=`

#========================================================================================================================#
# Begin common commands                                                                                                  #
#========================================================================================================================#

# Using application web base image
FROM applicationwebbase

#========================================================================================================================#
# End common commands                                                                                                    #
#========================================================================================================================#

#========================================================================================================================#
# Begin application installation commands                                                                                #
#========================================================================================================================#

# Enable IIS remote administration
RUN net user Application 'abc@123' /ADD; `
	net localgroup Administrators Application /ADD;
	
# Setup IIS server
RUN New-WebAppPool "Application";

# Update proces model identity type to 0 (LocalSystem)
RUN Import-Module WebAdministration; `
	CD IIS:; `
	Set-ItemProperty `
		-Path "IIS:\AppPools\Application" `
		-Name "processModel" `
		-Value @{identitytype=0}; 

# Set periodic recycling
RUN Import-Module WebAdministration; `
	CD IIS:; `
	Set-ItemProperty `
		-Path "IIS:\AppPools\Application" `
		-Name Recycling.periodicRestart.schedule ` 
		-Value @{format='00:00:00'};

# Setting the working directory
WORKDIR '/Program Files/Application/'
		
# Copying files from host machine to container
COPY [".", "/Program Files/Application/"]

# Create new webapplication
RUN	New-WebApplication `
		-Name "Application" `
		-Site 'Default Web Site' `
		-PhysicalPath 'C:\Program Files\Application' `
		-ApplicationPool "DataManager"; 
		
# Add permission to site directory
RUN $accessRule = New-Object `
	System.Security.AccessControl.FileSystemAccessRule `
	'IIS_IUSRS', 'FullControl', 'ContainerInherit,ObjectInherit', 'None', 'Allow'; `
    $acl = Get-ACL 'C:\Program Files\Application'; `
    $acl.AddAccessRule($accessRule); `
    Set-ACL `
		-Path 'C:\Program Files\Application' `
		-ACLObject $acl;

#========================================================================================================================#
# End application installation commands                                                                                  #
#========================================================================================================================#

#========================================================================================================================#
# The container stays up as long as this process is running                                                              #
#========================================================================================================================#

# Health check to verify the web service is running
HEALTHCHECK --interval=30s --timeout=10s --retries=10 `
   CMD powershell -command `
   try { `
    $response = iwr https://localhost/Application -UseBasicParsing; `
    if ($response.StatusCode -eq 200) { return 0 } `
    else { return 1 }; `
   } catch { return 1 }

# Start IIS
ENTRYPOINT ["C:\\ServiceMonitor.exe", "w3svc"]

#========================================================================================================================#