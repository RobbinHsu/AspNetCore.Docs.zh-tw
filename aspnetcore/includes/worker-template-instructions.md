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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552099"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 建立新專案。
1. 選取 [背景 **工作服務**]。 選取 [下一步] 。
1. 在 [專案名稱] 欄位中提供專案名稱，或接受預設專案名稱。 選取 [建立]  。
1. 在 [ **建立新的背景工作服務** ] 對話方塊中，選取 [ **建立**]。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 建立新專案。
1. 在提要欄位中選取 [ **.Net Core** ] 下的 [**應用程式**]。
1. 選取 [ **ASP.NET Core**] 下的 [背景 **工作**]。 選取 [下一步] 。
1. 選取 **目標 Framework** 的 **.net Core 3.0** 或更新版本。 選取 [下一步] 。
1. 在 [ **專案名稱** ] 欄位中提供名稱。 選取 [建立]  。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

從命令殼層以 [dotnet new](/dotnet/core/tools/dotnet-new) 命令使用背景工作服務 (`worker`) 範本。 在下列範例中，已建立名為 `ContosoWorker` 的背景工作服務應用程式。 當命令執行時，會自動建立 `ContosoWorker` 應用程式的資料夾。

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
