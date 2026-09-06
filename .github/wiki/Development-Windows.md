Want to help contribute to RePlays? Great! This page will help you get started on how to setup your dev environment and debug the RePlays app.

Feel free to reach out on Discord if you have issues or questions about the setup or general development.

Prerequisites:

*   Visual Studio 17 2022
    *   [.NET SDK 8.0.100](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-8.0.100-windows-x64-installer)
*   Node 18+
*   Knowledge in C# and Typescript (React.js)

Optional:

*   Visual Studio Code
*   OBS Studio 30.0.0
*   Cmake, git, & 7zip (if you are planning to build libobs yourself)
    *   Make sure 7z is in your system environment PATH

Visual Studio will be the main IDE for this guide, however feel free to follow along using Visual Studio Code, Jetbrains Rider, etc.

# 1. Clone the repository

Using git cli or using your preferred method of cloning. Make sure to include submodules.

*   `git clone --recursive https://github.com/lulzsun/RePlays.git`

# 2. Open the Visual Studio project

Open `RePlays.sln` using Visual Studio

# 3. (Optional) Run npm ci

*   In the project's ClientApp folder, run `npm ci` (from cmd/powershell)
    *   This will download the necessary node modules for the React.js portion of the app

This step can be skipped and is optional because when you start debugging, it will automatically run this command for you if you haven't already

# 4. Copy libobs to Debug folder

libobs is necessary for debugging and is not included with the project, the required files must be placed here:

`~/bin/Debug/net8.0-windows/win-x64/`

At the time of writing this guide, you have a few options for where to get libobs:

1.  Download the build that RePlays releases use (**RECOMMENDED**)
2.  Build it yourself

### 1. Download the build that RePlays releases use

The GitHub workflows do not build libobs on every commit. It is built once per version of `build-libobs.cmd` and published as a zip on the [libobs release](https://github.com/lulzsun/RePlays/releases/tag/libobs), and the nightly and stable builds download it from there. Using the same zip gives you exactly what production runs.

1.  Download the newest `libobs-<obs version>-<script>-windows-x64.zip` from the [libobs release](https://github.com/lulzsun/RePlays/releases/tag/libobs)
2.  Extract it into `~/obs-studio-build/obs-studio-<obs version>/build/rundir/Release/bin/64bit/` (create the folders; `<obs version>` must match the `OBSVersion` in `RePlays.csproj`), so that `obs.dll` ends up directly in that folder

The msbuild pre-build copies the files from there into the Debug folder the first time you debug.

### 2. Build libobs yourself

This gives the same result as the download, since the workflows run the same script, and is what you need when changing the script or the obs version.

This requires you to have Cmake, git, 7zip, and Visual Studio 17 2022 installed on your system.

Make sure Cmake, git and 7zip are in your system environmental variables (so they can be accessed through cmd)

Provided that you have all this, the build script is a one-click solution.

1.  Run `build-libobs.cmd` in cmd/powershell from root folder
2.  Build should be successful if the file `~/obs-studio-build/obs-studio-<obs version>/build/rundir/Release/bin/64bit/obs.dll` exists

The script takes care of everything (cloning, building, downloading and copying certain third party obs plugins if necessary) for you. When a change to the script lands on `main`, the `Build LibObs` workflow builds it and publishes the new zip on the libobs release, so the app builds keep downloading instead of building.

# 5. Start debugging!

You are now ready to start debugging! Upon initial debugging, the msbuild pre-build will take care of any missing files and provide warnings/errors if those files cannot be found (libobs files, React app files).

# 6. Building a release

To manually build a release on your machine, run the following command at the root of the project

`dotnet publish /p:Configuration=Release /p:Version=X.X.X /p:PublishProfile=FolderProfile`

You must specify a version in the numeral format of X.X.X (e.g. 1.69.420). Version numbering does not really matter for local release builds.

The build will make use of Velopack and create deltas or full packages, including a Setup file. You can find these files in `/bin/Deployment/Releases/`. A delta package is only created when the previous version's full package is present in that folder (the GitHub workflows download it with `dotnet vpk download github`).

To try the update flow of a local build before publishing it, start an installed RePlays with the environment variable `REPLAYS_UPDATE_FEED` set to the `/bin/Deployment/Releases/` folder. It is then used as the update feed instead of the GitHub releases.

You can learn more about the Velopack deployment process [here](https://docs.velopack.io/).

# 7. (Optional) Using Visual Studio Code

Visual Studio Code may be preferred when working with the React.js portion of the app. The React.js is the interface or front-end of the application.

Open `~/ClientApp` in Visual Studio Code and begin working on the interface like any normal React.js workflow!

You can also use VSCode to debug and make changes to C# related files, however the VSCode debug tools may not be as powerful and convenient compared to Visual Studio.
