# Container Setup
If you install Windows Server with Desktop experience, you can install container with PowerShell. On the other hand, if you use install Server Core or SAC (e.g. Windows Server, version 1803), you need to install container manually.

## Container Setup with PowerShell
1. Install Docker-Microsoft PackageManagement Provider.
   ```powershell
   PS> Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
   ```
1. Install the latest Docker.
   ```powershell
   PS> Install-Package -Name docker -ProviderName DockerMsftProvider
   ```
1. Restart the server.
1. Get a container image to create a container service.
   ```bat
   C:\> docker pull microsoft/windowsservercore
   ```
## Manual Container Setup 
1. Install Container role.
   ```powershell
   PS> Install-WindowsFeature Containers
   ```
1. Restart the server.
1. Set system environment.
   ```powershell
   PS> [Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Program Files\Docker", [EnvironmentVariableTarget]::Machine)
   PS> [Environment]::SetEnvironmentVariable("HTTP_PROXY", "<your proxy server>:port", [EnvironmentVariableTarget]::Machine)
   ```
1. Get the latest Docker modules.
   ```powershell
   PS> Invoke-Webrequest -UseBasicparsing -Outfile docker-18.03.1-ee-3.zip https://download.docker.com/components/engine/windows-server/18.03/docker-18.03.1-ee-3.zip
   ```
