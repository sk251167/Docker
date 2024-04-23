# escape=`

#========================================================================================================================#
# Begin common commands                                                                                                  #
#========================================================================================================================#

# Using mcr.microsoft.com/windows/servercore:ltsc2022 base image
FROM mcr.microsoft.com/windows/servercore:ltsc2022

# Set default as powershell
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Image description
LABEL description="This base image required to run splunk Universal forwarder"

# Author
LABEL author="Srinivas Kujala <srinivas.kujala@gmail.com>"

# Add user to container administrator
RUN net localgroup Administrators 'NT AUTHORITY\NETWORK SERVICE' /ADD;

# Build arguments
ARG DEPLOY_SERVER
ARG RECEIVING_INDEXER
ARG SPLUNK_PASSWORD

# Print build arguements during build
#RUN echo "Deployment Server   : $env:DEPLOY_SERVER"
#RUN echo "Receiving Indexer   : $env:RECEIVING_INDEXER"
#RUN echo "Spunk Password      : $env:SPLUNK_PASSWORD"

#========================================================================================================================#
# End common commands                                                                                                    #
#========================================================================================================================#

#========================================================================================================================#
# Begin splunk installtion commands                                                                                      #
#========================================================================================================================#

# Copy Splunk Universal Forwarder installer
COPY splunkforwarder-9.2.1-78803f08aabb-x64-release.msi C:\\splunkforwarder.msi

# Install Splunk Universal Forwarder silently
RUN msiexec /i c:\splunkforwarder.msi AGREETOLICENSE=Yes /quiet;

# Expose necessary ports
EXPOSE 8089 9997

# Setting the working directory
WORKDIR '/Program Files/SplunkUniversalForwarder/'

# Set SSL/TLS configuration and deployment server
RUN .\\bin\\splunk.exe set web-port 8089 -auth splunk:$env:SPLUNK_PASSWORD -no-prompt; `
   .\\bin\\splunk.exe set deploy-poll $env:DEPLOY_SERVER:8089 -auth splunk:$env:SPLUNK_PASSWORD -no-prompt; `
   .\\bin\\splunk.exe add forward-server $env:RECEIVING_INDEXER:9997 -auth splunk:$env:SPLUNK_PASSWORD -no-prompt; `
   .\\bin\\splunk.exe restart

#========================================================================================================================#
# End splunk installtion commands                                                                                        #
#========================================================================================================================#

#========================================================================================================================#
# The container stays up as long as this process is running                                                              #
#========================================================================================================================#

# Health check to verify the service is running
HEALTHCHECK --interval=30s --timeout=10s --retries=10 `
   CMD powershell -command `
   try { `
   $service = Get-Service -Name 'SplunkForwarder'; `
   if ($service.Status -eq 'Running') { exit 0 } else { exit 1 }; `
   } catch { exit 1 }

# Set the entry point and command to start the service and wait till process is running.
ENTRYPOINT ["powershell", `
   "-Command" , `
   "Start-Service 'splunkd*';", `
   "Write-Host Service started;", `
   "Wait-Process -Name 'splunkd';", `
   "Write-Host Service stopped;"]

#========================================================================================================================#