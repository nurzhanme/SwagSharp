# SwagSharp

![Build Status](https://github.com/nurzhanme/SwagSharp/workflows/SwagGen/badge.svg)

**SwagSharp**  is a demonstration repository showcasing the process of extracting an OpenAPI (commonly known as Swagger) definition from an ASP.NET Web API and subsequently publishing it on GitHub Pages.

This facilitates seamless integration with your documentation platforms, such as Confluence Pages.

## GitHub Pages Configuration

To set up the hosting of database documentation using GitHub Pages, follow these steps to configure it:

- Navigate to your repository settings.
- Select the `Pages` tab.
- Under `Build and deployment` select `Deploy from a branch` as `Source`.
- Specify the branch name as `gh-pages` (ensure it's created beforehand) and set the folder to `(root)`.
- Click `Save` to apply your configuration.

## Sample Workflow Pipeline

Below is a sample GitHub Actions workflow designed to automate your database documentation process:

```yml

- uses: actions/checkout@v2

- name: Set up .NET Core
  uses: actions/setup-dotnet@v1
  with:
    dotnet-version: '7.0.x'
    include-prerelease: true

- name: Build with dotnet
  run: dotnet build --configuration Release

- name: dotnet publish
  run: dotnet publish -c Release -o ${{env.DOTNET_ROOT}}/myapp

- name: Install Swashbuckle CLI
  run: |
    dotnet tool install --global Swashbuckle.AspNetCore.Cli --version 6.5.0
    dotnet restore --packages $GITHUB_WORKSPACE/packages # Specify the path to the packages directory
    export PATH="$PATH:$RUNNER_TOOL_CACHE/dotnet"

- name: Generate OpenAPI JSON
  run: |
    swagger tofile --output $GITHUB_WORKSPACE/swagger.json ${{env.DOTNET_ROOT}}/myapp/SwagSharp.dll v1

- name: Deploy to GitHub Pages
  uses: peaceiris/actions-gh-pages@v3
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: .
```

`GITHUB_TOKEN` secret is automatically created by a A GitHub Actions runner. It required to authenticate in a workflow run.

## Pipeline Output

Upon successful execution of this workflow, database documentation will be available on the [GitHub Pages](https://nurzhanme.github.io/SwagSharp/swagger.json) of the repository.
