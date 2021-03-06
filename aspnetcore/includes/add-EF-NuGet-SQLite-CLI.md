---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 1a9e98a84093f89f3859454f482dfe59c8504d9b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552486"
---
執行下列 .NET Core CLI 命令：

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

上述命令會新增：

* [Aspnet codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)的樣板工具。
* .NET Core CLI 的 Entity Framework Core 工具。
* EF Core SQLite 提供者，該提供者會將 EF Core 套件作為相依性安裝。
* Scaffolding `Microsoft.VisualStudio.Web.CodeGeneration.Design` 和 `Microsoft.EntityFrameworkCore.SqlServer` 需要的套件。

如需多個環境設定的指引，以允許應用程式依環境設定其資料庫內容，請參閱 <xref:fundamentals/environments#environment-based-startup-class-and-methods> 。

[!INCLUDE[](~/includes/scaffoldTFM.md)]