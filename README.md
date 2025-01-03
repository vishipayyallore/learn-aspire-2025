# Learn .NET Aspire in 2025

I am learning .NET Aspire from different Video Courses, Books, and Websites

## Badges

### Build

[![Deploy AspireStarterApp solution to Azure](https://github.com/vishipayyallore/learn-aspire-2025/actions/workflows/aspirestarterapp-deploy.yml/badge.svg)](https://github.com/vishipayyallore/learn-aspire-2025/actions/workflows/aspirestarterapp-deploy.yml)

### Code Coverage

[![codecov](https://codecov.io/github/vishipayyallore/learn-aspire-2025/graph/badge.svg?token=ZIBO5V36ZQ)](https://codecov.io/github/vishipayyallore/learn-aspire-2025)

## Few Commands

```powershell
dotnet workload list
```

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

---

## To be reviewed

This GitHub Workflow file defines a set of steps to build, test, and generate code coverage reports for a .NET project within a GitHub Actions environment. Let's break it down:

**1. Name:**

- `Build, Test, and Generate Code Coverage for a .NET project`: This is the descriptive name of the workflow.

**2. On:**

- `workflow_call`: This workflow is designed to be triggered by other workflows or manually.
- `inputs`:
  - `project-name`: A required input string that defines the name of the project.
  - `solution-path`: A required input string that specifies the path to the solution file (.sln).
  - `dotnet-version`: An optional input string that specifies the .NET SDK version to use. Defaults to "8.0.x".

**3. Jobs:**

- `run-test`: This is the single job defined in the workflow.
  - `runs-on: ubuntu-latest`: Specifies that the job should run on the latest Ubuntu operating system image provided by GitHub Actions.

**4. Steps:**

- `Checkout to the branch`:

  - Uses the `actions/checkout@v4` action to check out the code from the repository into the runner's workspace.

- `Setup .NET`:

  - Uses the `actions/setup-dotnet@v4` action to install the specified .NET SDK version on the runner.

- `Restore NuGet Packages`:

  - Executes `dotnet restore` to restore all the NuGet packages required for the project.

- `Build the solution`:

  - Executes `dotnet build` to build the entire solution. The `--no-restore` flag is used to avoid redundant package restoration since it was already performed in a previous step.

- `Run unit tests and generate code coverage`:

  - This is the core step of the workflow.
    - It uses `find . -name "*.Tests.csproj"` to locate all test projects within the repository.
    - For each test project:
      - Creates directories for storing test results and code coverage reports.
      - Executes `dotnet test` with the following key options:
        - `--logger trx`: Generates TRX test results files.
        - `--results-directory`: Specifies the directory to store the test results.
        - `--no-restore`: Prevents redundant package restoration at the test project level.
        - `--no-build`: Prevents unnecessary rebuilding of the test project.
        - `--collect:"XPlat Code Coverage"`: Enables code coverage collection.
        - `/p:CollectCoverage=true`: Enables code coverage collection within the MSBuild environment.
        - `/p:CoverletOutputFormat`: Specifies the desired output formats for code coverage reports (cobertura, opencover, json).
        - `/p:CoverletOutput`: Specifies the base path for the generated coverage reports.
      - Captures the exit code of the `dotnet test` command.
      - Checks for test failures and logs errors if any.
      - Exits the workflow if any test fails.

- `Upload the test results`:

  - Uses the `actions/upload-artifact@v4` action to upload the generated test results (stored in the `TestResults-${{ inputs.project-name }}` directory) as an artifact.

- `Upload code coverage reports`:

  - Uses the `actions/upload-artifact@v4` action to upload the generated code coverage reports (stored in the `TestResults-${{ inputs.project-name }}/**/CoverageResults/**/*.xml` path) as an artifact.

- `Upload coverage reports to Codecov`:
  - Uses the `codecov/codecov-action@v5` action to upload the code coverage reports to Codecov for further analysis. This requires a Codecov personal access token to be configured as a secret in the GitHub repository settings.

**In summary:**

This workflow provides a robust and efficient way to automate the build, test, and code coverage generation process for .NET projects within a GitHub Actions environment. It incorporates best practices like:

- **Parameterization:** Allows for flexible input parameters like project name, solution path, and .NET SDK version.
- **Code coverage generation:** Supports multiple code coverage report formats (cobertura, opencover, json).
- **Error handling:** Checks for test failures and provides informative error messages.
- **Artifact upload:** Uploads test results and code coverage reports as artifacts for easy access and analysis.
- **Codecov integration:** Allows for seamless integration with Codecov for advanced code coverage analysis and tracking.

This workflow can be further customized and extended based on specific project needs and requirements.
