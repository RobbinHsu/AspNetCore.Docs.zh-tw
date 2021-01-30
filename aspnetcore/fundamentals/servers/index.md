---
title: ASP.NET Core 中的網頁伺服器實作
author: rick-anderson
description: 探索 ASP.NET Core 的網頁伺服器 Kestrel 與 HTTP.sys。 了解如何選擇伺服器，以及何時使用反向 Proxy 伺服器。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 91d1373d764644820d1fac6064ee503e1ef4455c
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057339"
---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core 中的網頁伺服器實作

由 [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)、[Stephen Halter](https://twitter.com/halter73) 和 [Chris Ross](https://github.com/Tratcher) 提供

ASP.NET Core 應用程式執行時，需使用內含式 HTTP 伺服器實作。 伺服器實作會接聽 HTTP 要求，並以組成 <xref:Microsoft.AspNetCore.Http.HttpContext> 的一組[要求功能](xref:fundamentals/request-features)形式向應用程式呈現。

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core 隨附下列項目：

* [Kestrel 伺服器](xref:fundamentals/servers/kestrel)是預設、跨平台的 HTTP 伺服器實作。 Kestrel 可提供最佳的效能和記憶體使用量，但是它沒有一些像 `Http.Sys` 是埠共用的先進功能。
* IIS HTTP 伺服器是 IIS 的[同處理序伺服器](#hosting-models)。
* [HTTP.sys 伺服器](xref:fundamentals/servers/httpsys)是以 [HTTP.sys 核心驅動程式與 HTTP 伺服器 API](/windows/desktop/Http/http-api-start-page) 為基礎的僅限 Windows HTTP 伺服器。

使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 時，應用程式可能會執行於：

* 在與 IIS 背景工作進程相同的程式中 (同 [進程裝載模型](#hosting-models)) 與 Iis HTTP 伺服器一起使用。 「同處理序」是建議的設定。
* 從 IIS 背景工作處理序中分離出的處理序 ([跨處理序裝載模型](#hosting-models))，並搭配 [Kestrel 伺服器](#kestrel)。

[ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)是一種原生 IIS 模組，可處理 IIS 與同處理序 IIS HTTP 伺服器或 Kestrel 之間的原生 IIS 要求。 如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module>。

## <a name="hosting-models"></a>裝載模型

使用同處理序裝載，ASP.NET Core 應用程式會在與其 IIS 工作者處理序相同的處理序中執行。 因為要求未透過回送介面卡 (將連出網路流量傳回同一部電腦的網路介面) 進行 proxy 處理，所以同處理序裝載會提供優於跨處理序裝載的效能。 IIS 透過 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 來執行處理程序管理。

使用非同處理序代管，ASP.NET Core 應用程式可執行於與 IIS 背景工作處理序不同的處理序中，且此模組會進行處理序的管理。 此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。 此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。

如需詳細資訊與組態指南，請參閱下列主題：

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，這是預設、跨平台的 HTTP 伺服器。

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，這是預設、跨平台的 HTTP 伺服器。

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)是預設、跨平台的 HTTP 伺服器實作。 Kestrel 可提供最佳的效能和記憶體使用量，但是它沒有一些像 `Http.Sys` 是埠共用的先進功能。
 
使用 Kestrel：

* 供本身當作直接從網路 (包括網際網路) 處理要求的邊緣伺服器。

  ![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

* 搭配「反向 Proxy 伺服器」使用，例如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)。 反向 Proxy 伺服器會從網際網路接收 HTTP 要求，然後轉送到 Kestrel。

  ![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

支援裝載設定 &mdash; ，不論是否使用反向 proxy 伺服器 &mdash; 。

如需 Kestrel 設定指南及資訊，以了解在反向 Proxy 設定中使用 Kestrel 的時機，請參閱 <xref:fundamentals/servers/kestrel>。

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core 隨附下列項目：

* [Kestrel 伺服器](xref:fundamentals/servers/kestrel)是預設、跨平台的 HTTP 伺服器。
* [HTTP.sys 伺服器](xref:fundamentals/servers/httpsys)是以 [HTTP.sys 核心驅動程式與 HTTP 伺服器 API](/windows/desktop/Http/http-api-start-page) 為基礎的僅限 Windows HTTP 伺服器。

在使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 時，應用程式會執行於從 IIS 背景工作處理序中分離出的處理序 (跨處理序)，並搭配 [Kestrel 伺服器](#kestrel)。

因為 ASP.NET Core 應用程式執行所在的處理序會與 IIS 工作者處理序分開，所以此模組會執行處理程序管理。 此模組會在第一個要求到達時啟動 ASP.NET Core 應用程式的處理序，並在應用程式關閉或損毀時將它重新啟動。 此行為基本上與執行同處理序，並由 [Windows 處理序啟用服務 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 所管理的應用程式相同。

下圖說明 IIS、ASP.NET Core 模組和跨處理序裝載應用程式之間的關聯性：

![ASP.NET Core 模組](_static/ancm-outofprocess.png)

要求會從 Web 到達核心模式的 HTTP.sys 驅動程式。 驅動程式會在網站設定的通訊埠上將要求路由至 IIS，此通訊埠通常是 80 (HTTP) 或 443 (HTTPS)。 此模組會在應用程式的隨機通訊埠上將要求轉送至 Kestrel，而且不會是通訊埠 80 或 443。

模組會在啟動時透過環境變數指定埠，而 [IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 會設定要接聽的伺服器 `http://localhost:{port}` 。 將會執行額外檢查，不是源自模組的要求都會遭到拒絕。 此模組不支援 HTTPS 轉送，因此即使由 IIS 透過 HTTPS 接收，要求還是會透過 HTTP 轉送。

Kestrel 收取來自模組的要求之後，要求會被推送至 ASP.NET Core 中介軟體管線。 中介軟體管線會處理要求，並將其作為 `HttpContext` 執行個體傳遞至應用程式的邏輯。 IIS Integration 新增的中介軟體會更新配置、遠端 IP 和帳戶路徑基底，以將要求轉送至 Kestrel。 應用程式的回應會傳回 IIS，而 IIS 會將其推送回起始要求的 HTTP 用戶端。

如需 IIS 和 ASP.NET Core 模組的設定指南，請參閱下列主題：

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，這是預設、跨平台的 HTTP 伺服器。

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core 隨附 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)，這是預設、跨平台的 HTTP 伺服器。

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx 與 Kestrel

如需如何在 Linux 上使用 Nginx 作為 Kestrel 反向 Proxy 伺服器的資訊，請參閱 <xref:host-and-deploy/linux-nginx>。

### <a name="apache-with-kestrel"></a>Apache 與 Kestrel

如需如何在 Linux 上使用 Apache 作為 Kestrel 反向 Proxy 伺服器的資訊，請參閱 <xref:host-and-deploy/linux-apache>。

## <a name="httpsys"></a>HTTP.sys

如果您在 Windows 上執行 ASP.NET Core 應用程式，則 HTTP.sys 是 Kestrel 的替代方案。 除非應用程式需要 Kestrel 中未提供的功能，否則建議使用 Kestrel 來進行 HTTP.sys。 如需詳細資訊，請參閱<xref:fundamentals/servers/httpsys>。

![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

HTTP.sys 也可用於只公開到內部網路的應用程式。

![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

如需 HTTP.sys 設定指南，請參閱 <xref:fundamentals/servers/httpsys>。

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core 伺服器基礎結構

`Startup.Configure` 方法提供的 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 會公開類型<xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 的 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> 屬性。 Kestrel 與 HTTP.sys 只會公開 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 功能，但不同的伺服器實作則可能會公開更多的功能。

`IServerAddressesFeature` 可用來找出伺服器實作在執行階段已繫結的連接埠。

## <a name="custom-servers"></a>自訂伺服器

如果內建伺服器不符合應用程式的需求，則可以建立自訂伺服器實作。 [Open Web Interface for .NET (OWIN) 指南](xref:fundamentals/owin)示範如何撰寫採用 [Nowin](https://github.com/Bobris/Nowin) 的 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 實作。 只有該應用程式使用的功能介面才需要實作，但至少須支援 <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> 及 <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>。

## <a name="server-startup"></a>伺服器啟動

伺服器會在整合式開發環境 (IDE) 或編輯器啟動應用程式時啟動：

* [Visual Studio](https://visualstudio.microsoft.com)：啟動設定檔可用來透過[IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)或主控台來啟動應用程式和伺服器。
* [Visual Studio Code](https://code.visualstudio.com/)：應用程式和伺服器是由 [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)啟動，這會啟用 CoreCLR 偵錯工具。
* [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)： [Mono Soft-Mode 調試](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)程式會啟動應用程式和伺服器。

當您在專案資料夾中使用命令提示字元啟動應用程式時，[dotnet run](/dotnet/core/tools/dotnet-run) 會啟動應用程式和伺服器 (僅限 Kestrel 和 HTTP.sys)。 組態是由 `-c|--configuration` 選項指定，會設為 `Debug` (預設值) 或 `Release`。

當使用或工具內建的偵錯工具（例如 Visual Studio）來啟動應用程式時，檔案 *上的launchSettings.js* 會提供設定 `dotnet run` 。 如果啟動設定檔存在於檔案的 *launchSettings.js* 中，請使用 `--launch-profile {PROFILE NAME}` 選項搭配 `dotnet run` 命令或選取 Visual Studio 中的設定檔。 如需詳細資訊，請參閱 [dotnet run](/dotnet/core/tools/dotnet-run) 和 [.NET Core 發佈封裝](/dotnet/core/build/distribution-packaging)。

## <a name="http2-support"></a>HTTP/2 支援

在下列部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：

::: moniker range=">= aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel/http2)
  * 作業系統
    * Windows Server 2016/Windows 10 或更新版本&dagger;
    * Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)
    * 未來版本的 macOS 將會支援 HTTP/2。
  * 目標 Framework：.NET Core 2.2 或更新版本
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 或更新版本
  * 目標 Framework：不適用於 HTTP.sys 部署。
* [IIS (同處理序)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本
  * 目標 Framework：.NET Core 2.2 或更新版本
* [IIS (跨處理序)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本
  * 公眾對應 Edge Server 連線使用 HTTP/2，但是對 Kestrel 的反向 Proxy 連線使用 HTTP/1.1。
  * 目標 Framework：不適用於 IIS 跨處理序部署。

&dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。 支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。 可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * 作業系統
    * Windows Server 2016/Windows 10 或更新版本&dagger;
    * Linux 含 OpenSSL 1.0.2 或更新版本 (例如 Ubuntu 16.04 或更新版本)
    * 未來版本的 macOS 將會支援 HTTP/2。
  * 目標 Framework：.NET Core 2.2 或更新版本
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 或更新版本
  * 目標 Framework：不適用於 HTTP.sys 部署。
* [IIS (同處理序)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本
  * 目標 Framework：.NET Core 2.2 或更新版本
* [IIS (跨處理序)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本
  * 公眾對應 Edge Server 連線使用 HTTP/2，但是對 Kestrel 的反向 Proxy 連線使用 HTTP/1.1。
  * 目標 Framework：不適用於 IIS 跨處理序部署。

&dagger;Kestrel 在 Windows Server 2012 R2 與 Windows 8.1 對 HTTP/2 的支援有限。 支援有限的原因是這些作業系統上的支援 TLS 密碼編譯套件清單有限。 可能需要使用橢圓曲線數位簽章演算法 (ECDSA) 產生的憑證來保護 TLS 連線。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 或更新版本
  * 目標 Framework：不適用於 HTTP.sys 部署。
* [IIS (跨處理序)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本
  * 公眾對應 Edge Server 連線使用 HTTP/2，但是對 Kestrel 的反向 Proxy 連線使用 HTTP/1.1。
  * 目標 Framework：不適用於 IIS 跨處理序部署。

::: moniker-end

HTTP/2 連線必須使用 [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 和 TLS 1.2 或更新版本。 如需詳細資訊，請參閱與伺服器部署案例相關的主題。

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
