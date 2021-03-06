---
title: SignalR將 ASP.NET Core 應用程式發佈至 Azure App Service
author: bradygaster
description: 瞭解如何將 ASP.NET Core SignalR 應用程式發佈到 Azure App Service。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234502"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a>SignalR將 ASP.NET Core 應用程式發佈至 Azure App Service

依 [Brady Gaster](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) 是用來裝載 web 應用程式的 [Microsoft 雲端](https://azure.microsoft.com/) 運算平臺服務，包括 ASP.NET Core。

> [!NOTE]
> 本文是指從 Visual Studio 發佈 ASP.NET Core SignalR 應用程式。 如需詳細資訊，請參閱[ SignalR Azure 服務](https://azure.microsoft.com/services/signalr-service)。

## <a name="publish-the-app"></a>發佈應用程式

本文涵蓋使用 Visual Studio 中的工具進行發佈。 Visual Studio Code 使用者可以使用 [Azure CLI](/cli/azure) 命令，將應用程式發佈至 Azure。 如需詳細資訊，請參閱 [使用命令列工具將 ASP.NET Core 應用程式發佈至 Azure](/azure/app-service/app-service-web-get-started-dotnet)。

1. 以滑鼠右鍵按一下 **方案總管** 中的專案，然後選取 [ **發行** ]。

1. 確認在 [ **挑選發佈目標** ] 對話方塊中選取了 [ **App Service** 和 **建立新** 的]。

1. 從 [ **發行** ] 按鈕下拉式清單中選取 [ **建立設定檔** ]。

   在 [ **建立 App Service** ] 對話方塊中，輸入下表所述的資訊，然後選取 [ **建立** ]。

   | 項目               | 描述 |
   | ------------------ | ----------- |
   | **名稱**           | 應用程式的唯一名稱。 |
   | **訂用帳戶**   | 應用程式使用的 Azure 訂用帳戶。 |
   | **資源群組** | 應用程式所屬的相關資源群組。 |
   | **主控方案**   | Web 應用程式的定價方案。 |

1. 在 [服務相依性 **]** 區段中選取 [ **Azure SignalR 服務** ]。 選取 **+** 按鈕：

   ![[相依性] 區域會顯示在 [新增] 下拉式清單中選取的 Azure：：： no-loc (SignalR) ：：： Service](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. 在 [ **azure SignalR 服務** ] 對話方塊中，選取 [ **建立新的 azure SignalR 服務實例** ]。

1. 提供 **名稱** 、 **資源群組** 和 **位置** 。 返回 [ **Azure SignalR 服務** ] 對話方塊，然後選取 [ **新增** ]。

Visual Studio 完成下列工作：

* 建立包含發行設定的發行設定檔。
* 使用提供的詳細資料建立 *Azure Web 應用程式* 。
* 發佈應用程式。
* 啟動載入 web 應用程式的瀏覽器。

應用程式 URL 的格式為 `{APP SERVICE NAME}.azurewebsites.net` 。 例如，名為的應用程式 `SignalRChatApp` 具有的 URL `https://signalrchatapp.azurewebsites.net` 。

如果部署以預覽 .NET Core 版本為目標的應用程式時發生 HTTP *502.2-不正確的閘道* 錯誤，請參閱將 [ASP.NET Core 預覽版本部署至 Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) 以解決此問題。

## <a name="configure-the-app-in-azure-app-service"></a>在 Azure App Service 中設定應用程式

> [!NOTE]
> *本節僅適用于不使用 Azure 服務的應用程式 SignalR 。*
>
> 如果應用程式使用 Azure SignalR 服務，App Service 不需要設定應用程式要求路由 (ARR) 親和性和 Web 通訊端（本節所述）。 用戶端會將其 Web 通訊端連接至 Azure SignalR 服務，而不是直接連線到應用程式。

針對沒有 Azure 服務所裝載的應用程式 SignalR ，請啟用：

* [ARR 親和性] (https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie - (ARR- cookie) # B0 l) 將使用者的要求路由傳送回相同的 App Service 實例。 預設設定為 [ **開啟** ]。
* [Web 通訊端](xref:fundamentals/websockets) ，以允許 Web 通訊端傳輸運作。 預設設定為 [ **關閉** ]。

1. 在 Azure 入口網站中，流覽至 **應用程式服務** 中的 web 應用程式。
1. 開啟 **Configuration**  >  **[設定一般設定** ]。
1. 將 **Web 通訊端** 設定為 **開啟** 。
1. 確認 [ **ARR 親和性** ] 設為 [ **開啟** ]。

## <a name="app-service-plan-limits"></a>App Service 方案限制

Web 通訊端和其他傳輸會根據選取的 App Service 方案而受到限制。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額和限制](/azure/azure-subscription-service-limits#app-service-limits)一文的 *Azure 雲端服務限制* 和 *App Service 限制* 章節。

## <a name="additional-resources"></a>其他資源

* [什麼是 Azure SignalR 服務？](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [使用命令列工具將 ASP.NET Core 應用程式發行到 Azure](/azure/app-service/app-service-web-get-started-dotnet)
* [在 Azure 上裝載和部署 ASP.NET Core 預覽版應用程式](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
