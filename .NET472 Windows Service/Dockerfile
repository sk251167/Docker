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
HEALTHCHECK --interval=30s --timeout=10s --retries=10 `
   CMD powershell -command `
   try { `
       $service = Get-Service -Name 'Application Service'; `
       if ($service.Status -eq 'Running') { exit 0 } else { exit 1 }; `
   } catch { exit 1 }

# Set the entry point and command to start the service and monitor events
ENTRYPOINT ["powershell"]
CMD Start-Service 'Application Service'; `
    $idx = (Get-EventLog -LogName System -Newest 1).Index; `
    While ($true) `
    {; `
		Start-Sleep -Seconds 1; `
		$idx2  = (Get-EventLog -LogName System -Newest 1).Index; `
		Get-EventLog -LogName System -Newest ($idx2 - $idx) | Sort Index | Format-List; `
		$idx = $idx2; `
    }
	
#========================================================================================================================#