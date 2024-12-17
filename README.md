# Learn .NET Aspire in 2025

I am learning .NET Aspire from different Video Courses, Books, and Websites

## Prepare Aspire Project for 

```powershell
D:\GitHub\learn-aspire-2025\src\AspireStarterApp\AspireStarterApp.AppHost>azd init

#Places the file in the root folder.
D:\GitHub\learn-aspire-2025\src\AspireStarterApp\AspireStarterApp.AppHost> dotnet run --publisher manifest --output-path ./aspire-manifest.json

D:\GitHub\learn-aspire-2025\src\AspireStarterApp\AspireStarterApp.AppHost> azd config set alpha.infraSynth on
D:\GitHub\learn-aspire-2025\src\AspireStarterApp\AspireStarterApp.AppHost> azd infra synth

azd auth login --scope https://management.azure.com//.default

azd config set alpha.resourceGroupDeployments on

azd up
```
