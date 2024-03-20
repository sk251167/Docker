# escape=`

#========================================================================================================================#
# Begin common commands                                                                                                  #
#========================================================================================================================#

# Using application base image 
FROM applicationbase

#========================================================================================================================#
# End common commands                                                                                                    #
#========================================================================================================================#

#========================================================================================================================#
# Begin application installation commands                                                                                #
#========================================================================================================================#

# Setting the working directory
WORKDIR '/Program Files/Application/'

# Copying files from host machine to container
COPY [".", "/Program Files/Application/"]

# Run install service without credentials
RUN $service_name = 'Application Service'; `
    $service_path = 'C:\Program Files\Application\Application.exe'; `
	New-Service `
		-Name $service_name `
		-BinaryPathName $service_path `
		-StartupType "\"Manual"\";

#========================================================================================================================#
# End application installation commands                                                                                  #
#========================================================================================================================#

#========================================================================================================================#
# The container stays up as long as this process is running                                                              #
#========================================================================================================================#

# Health check to verify the service is running
HEALTHCHECK --interval=30s --timeout=30s --retries=3 `
   CMD powershell -command `
   try { `
       $service = Get-Service -Name 'Application Service Name'; `
       if ($service.Status -eq 'Running') { exit 0 } else { exit 1 }; `
   } catch { exit 1 }

# Set the entry point and command to start the service and wait till process is running.
ENTRYPOINT ["powershell", `
			"-Command" , `
			"Start-Service 'Application Service Name';", `
			"Write-Host Service started;", `
			"Wait-Process -Name 'Application Process Name';", `
			"Write-Host Service stopped;"]

#========================================================================================================================#