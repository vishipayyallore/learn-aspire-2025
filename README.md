# Learn .NET Aspire in 2025

I am learning .NET Aspire from different Video Courses, Books, and Websites

## Prepare Aspire Project for deployment

```powershell
D:\GitHub\learn-aspire-2025\src\AspireStarterApp\AspireStarterApp.AppHost> azd init

#Places the file in the root folder.
D:\GitHub\learn-aspire-2025\src\AspireStarterApp\AspireStarterApp.AppHost> dotnet run --publisher manifest --output-path ./aspire-manifest.json

D:\GitHub\learn-aspire-2025\src\AspireStarterApp\AspireStarterApp.AppHost> azd config set alpha.infraSynth on
D:\GitHub\learn-aspire-2025\src\AspireStarterApp\AspireStarterApp.AppHost> azd infra synth

azd auth login --scope https://management.azure.com//.default

azd config set alpha.resourceGroupDeployments on

azd up
```

## GitHub Actions

This GitHub Actions workflow is designed to build and test a .NET project. Here's a breakdown of its key features and considerations:

**1. Workflow Definition:**

- **Name:** "Build and Test a .NET project"
- **Trigger:** `workflow_call`: This workflow is intended to be called by other workflows or manually triggered. It accepts the following inputs:
  - `project-name`: The name of the project (required).
  - `solution-path`: The path to the solution file (required).
  - `dotnet-version`: The .NET SDK version to use (optional, defaults to "8.0.x").

**2. Job Definition:**

- **Name:** `run-test`
- **Runs on:** `ubuntu-latest`: Specifies that the job should run on the latest Ubuntu image.

**3. Job Steps:**

- **Checkout:**

  - `actions/checkout@v4`: Checks out the code from the repository into the runner's workspace.

- **Setup .NET:**

  - `actions/setup-dotnet@v4`: Installs the specified .NET SDK version on the runner.

- **Restore NuGet Packages:**

  - `dotnet restore ${{ inputs.solution-path }}`: Restores all NuGet packages for the solution.

- **Build the Solution:**

  - `dotnet build ${{ inputs.solution-path }} --no-restore`: Builds the solution without restoring packages (as they were already restored in the previous step).

- **Run Unit Tests:**

  - `find . -name "*.Tests.csproj" | while read -r testProject; do ... done`:
    - Finds all test projects within the repository.
    - For each test project:
      - Creates a directory to store the test results.
      - Runs `dotnet test` with the following options:
        - `--logger trx`: Generates TRX test results files, which can be easily analyzed.
        - `--results-directory`: Specifies the directory to store the test results.
        - `--no-restore`: Prevents redundant package restoration at the test project level.
        - `--no-build`: Prevents unnecessary rebuilding of the test project (since the solution was already built).

- **Upload Test Results:**
  - `actions/upload-artifact@v4`: Uploads the generated test results to the workflow run's artifacts. This allows you to easily access and review the test results later.

**Key Improvements:**

- **Efficiency:** The workflow is optimized by avoiding redundant steps:
  - Packages are restored only once.
  - Solutions are built only once.
  - Test projects are not rebuilt if the solution build was successful.
- **Flexibility:** The workflow can be easily reused for different .NET projects by simply adjusting the `project-name` and `solution-path` inputs.
- **Clarity:** The workflow is well-structured and easy to understand, with clear comments explaining each step.

This workflow provides a solid foundation for building and testing .NET projects within a GitHub Actions environment. You can further customize it by adding steps for code analysis, code coverage reports, or deploying the built application.
