# escape=`

#========================================================================================================================#
# Begin common commands                                                                                                  #
#========================================================================================================================#

# Using mcr.microsoft.com/windows/servercore:ltsc2022 base image
FROM mcr.microsoft.com/windows/servercore:ltsc2022

# Set default as powershell
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Image description
LABEL description="This base image required to run windows legacy applications"

# Author
LABEL author="Srinivas Kujala <srinivas.kujala@gamil.com>"

# Add user to container administrator
RUN net localgroup Administrators 'NT AUTHORITY\NETWORK SERVICE' /ADD;

# Set container user
USER "NT AUTHORITY\NETWORK SERVICE"

# Setting the working directory
WORKDIR '/Program Files/Application/'

# Install chocolatey 
RUN Set-ExecutionPolicy Bypass -Scope Process -Force; `
	[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]'Tls,Tls11,Tls12,Tls13'; `
	IEX ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'));

# Install chocolatey vim
RUN choco install vim -y;

#========================================================================================================================#
# End common commands                                                                                                    #
#========================================================================================================================#