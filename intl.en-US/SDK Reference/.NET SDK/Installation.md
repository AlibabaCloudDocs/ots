# Installation

This topic describes how to install Tablestore SDK for .NET.

## Version dependencies

Windows

-   .NET 4.0 or later is required.
-   Visual Studio 2010 or later is required.

## Install Tablestore SDK for .NET in Windows

-   Install by using NuGet
    1.  After you install NuGet, create a project or open an existing project in Visual Studio. Then, choose **Tools** \> **NuGet Package Manager** \> **Manage NuGet Packages for Solution**.

        **Note:** If Visual Studio does not have NuGet installed, download and install NuGet. For more information about the download path, visit [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

    2.  Enter aliyun.tablestore in the search box and click Search. Find Aliyun.TableStore.SDK in the search results.
    3.  Select the latest version, and click **Install**.

        After the installation is complete. Tablestore SDK for .NET is added to the project for application.

-   Install by importing a project
    1.  Download the source code from GitHub. For more information about the download path, visit [GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk.git).

        **Note:** If Git is not installed, download and install Git. For more information about the download path, visit [git](https://git-scm.com/downloads).

    2.  In Visual Studio, right-click and select **Solution** from the menu that appears, choose **Add** \> **Existing projects**.
    3.  In the dialog box that appears, select the aliyun-tablestore-sdk.csproj file. Click **Open**.
    4.  Right-click and select **Your project**. Choose **References** \> **Add Reference** from the shortcut menu. In the dialog box that appears, click the **Projects** tab, and select the aliyun-tablestore-sdk project.
    5.  Click **OK**.

