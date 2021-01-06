---
title: 使用 JavaScript 服務在 ASP.NET Core 中建立單一頁面應用程式
author: scottaddie
description: 瞭解使用 JavaScript 服務建立單一頁面應用程式 (SPA) ASP.NET Core 支援的優點。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 1b9f5b4b4e066cdd3dd5fbfa666c7a087949979f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054641"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="931db-103">使用 JavaScript 服務在 ASP.NET Core 中建立單一頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="931db-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="931db-104">由 [Scott Addie](https://github.com/scottaddie) 和 [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="931db-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="931db-105">單一頁面應用程式 (SPA) 是一種熱門的 web 應用程式，因為其固有的豐富使用者體驗。</span><span class="sxs-lookup"><span data-stu-id="931db-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="931db-106">將用戶端 SPA 架構或程式庫（例如 [角度](https://angular.io/) 或 [回應](https://facebook.github.io/react/)）與伺服器端架構（例如 ASP.NET Core）相整合可能很困難。</span><span class="sxs-lookup"><span data-stu-id="931db-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="931db-107">JavaScript 服務的開發目的是要減少整合過程中的麻煩。</span><span class="sxs-lookup"><span data-stu-id="931db-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="931db-108">它能夠在不同的用戶端和伺服器技術堆疊之間進行順暢的操作。</span><span class="sxs-lookup"><span data-stu-id="931db-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="931db-109">本文所述的功能已于 ASP.NET Core 3.0 淘汰。</span><span class="sxs-lookup"><span data-stu-id="931db-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="931db-110">更簡單的 SPA 架構整合機制可在 [AspNetCore. SpaServices （延伸](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) ） NuGet 套件中取得。</span><span class="sxs-lookup"><span data-stu-id="931db-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="931db-111">如需詳細資訊，請參閱 [[公告] Obsoleting AspNetCore. SpaServices 和 AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890)。</span><span class="sxs-lookup"><span data-stu-id="931db-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="931db-112">什麼是 JavaScript 服務</span><span class="sxs-lookup"><span data-stu-id="931db-112">What is JavaScript Services</span></span>

<span data-ttu-id="931db-113">JavaScript 服務是 ASP.NET Core 用戶端技術的集合。</span><span class="sxs-lookup"><span data-stu-id="931db-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="931db-114">其目標是要將 ASP.NET Core 定位為開發人員慣用的伺服器端平臺以建立 Spa。</span><span class="sxs-lookup"><span data-stu-id="931db-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="931db-115">JavaScript 服務是由兩個不同的 NuGet 套件所組成：</span><span class="sxs-lookup"><span data-stu-id="931db-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="931db-116">[AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices) </span><span class="sxs-lookup"><span data-stu-id="931db-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="931db-117">[AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices) </span><span class="sxs-lookup"><span data-stu-id="931db-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="931db-118">這些套件在下列案例中很有用：</span><span class="sxs-lookup"><span data-stu-id="931db-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="931db-119">在伺服器上執行 JavaScript</span><span class="sxs-lookup"><span data-stu-id="931db-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="931db-120">使用 SPA 架構或程式庫</span><span class="sxs-lookup"><span data-stu-id="931db-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="931db-121">使用 Webpack 建立用戶端資產</span><span class="sxs-lookup"><span data-stu-id="931db-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="931db-122">本文中的大部分焦點都放在使用 SpaServices 套件。</span><span class="sxs-lookup"><span data-stu-id="931db-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="931db-123">什麼是 SpaServices</span><span class="sxs-lookup"><span data-stu-id="931db-123">What is SpaServices</span></span>

<span data-ttu-id="931db-124">建立 SpaServices 是為了將 ASP.NET Core 定位為開發人員慣用的伺服器端平臺，以建立 Spa。</span><span class="sxs-lookup"><span data-stu-id="931db-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="931db-125">SpaServices 不需要使用 ASP.NET Core 開發 Spa，也不會鎖定開發人員進入特定的用戶端架構。</span><span class="sxs-lookup"><span data-stu-id="931db-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="931db-126">SpaServices 提供實用的基礎結構，例如：</span><span class="sxs-lookup"><span data-stu-id="931db-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="931db-127">伺服器端預先呈現</span><span class="sxs-lookup"><span data-stu-id="931db-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="931db-128">Webpack Dev 中介軟體</span><span class="sxs-lookup"><span data-stu-id="931db-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="931db-129">熱模組更換</span><span class="sxs-lookup"><span data-stu-id="931db-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="931db-130">路由協助程式</span><span class="sxs-lookup"><span data-stu-id="931db-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="931db-131">這些基礎結構元件共同強化了開發工作流程和執行時間體驗。</span><span class="sxs-lookup"><span data-stu-id="931db-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="931db-132">元件可以個別採用。</span><span class="sxs-lookup"><span data-stu-id="931db-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="931db-133">使用 SpaServices 的必要條件</span><span class="sxs-lookup"><span data-stu-id="931db-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="931db-134">若要使用 SpaServices，請安裝下列各項：</span><span class="sxs-lookup"><span data-stu-id="931db-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="931db-135">使用 npm (6 版或更新版本) 的[Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="931db-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="931db-136">若要確認已安裝且可找到這些元件，請從命令列執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="931db-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="931db-137">如果部署至 Azure 網站，則不需要採取任何動作， &mdash;Node.js 已安裝且可在伺服器環境中使用。</span><span class="sxs-lookup"><span data-stu-id="931db-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="931db-138">在使用 Visual Studio 2017 的 Windows 上，您可以藉由選取 **.Net Core 跨平臺開發** 工作負載來安裝 SDK。</span><span class="sxs-lookup"><span data-stu-id="931db-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="931db-139">[AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="931db-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="931db-140">伺服器端預先呈現</span><span class="sxs-lookup"><span data-stu-id="931db-140">Server-side prerendering</span></span>

<span data-ttu-id="931db-141">通用 (也稱為 isomorphic) 應用程式是一種 JavaScript 應用程式，可以在伺服器和用戶端上執行。</span><span class="sxs-lookup"><span data-stu-id="931db-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="931db-142">角度、反應和其他熱門架構都提供此應用程式開發樣式的通用平臺。</span><span class="sxs-lookup"><span data-stu-id="931db-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="931db-143">其概念是先透過 Node.js 在伺服器上轉譯架構元件，再將進一步的執行委派給用戶端。</span><span class="sxs-lookup"><span data-stu-id="931db-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="931db-144">SpaServices [提供的](xref:mvc/views/tag-helpers/intro) ASP.NET Core 標籤協助程式可透過叫用伺服器上的 JavaScript 函式，簡化伺服器端預先呈現的執行。</span><span class="sxs-lookup"><span data-stu-id="931db-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="931db-145">伺服器端預先呈現必要條件</span><span class="sxs-lookup"><span data-stu-id="931db-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="931db-146">安裝 [aspnet 預先呈現](https://www.npmjs.com/package/aspnet-prerendering) 的 npm 套件：</span><span class="sxs-lookup"><span data-stu-id="931db-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="931db-147">伺服器端預先呈現設定</span><span class="sxs-lookup"><span data-stu-id="931db-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="931db-148">標記協助程式可透過專案的 *_ViewImports cshtml* 檔案中的命名空間註冊來進行探索：</span><span class="sxs-lookup"><span data-stu-id="931db-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="931db-149">這些標籤協助程式會在視圖內利用類似 HTML 的語法，以簡化與低層級 Api 直接通訊的複雜性 Razor ：</span><span class="sxs-lookup"><span data-stu-id="931db-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="931db-150">asp-的模組標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="931db-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="931db-151">上述程式碼範例中使用的標籤協助程式會透過 `asp-prerender-module` Node.js，在伺服器上執行 *ClientApp/dist/main-server.js* 。</span><span class="sxs-lookup"><span data-stu-id="931db-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="931db-152">為了清楚起見， *main-server.js* 檔案是 [Webpack](https://webpack.github.io/) 組建程式中 TypeScript 到 JavaScript 轉譯工作的成品。</span><span class="sxs-lookup"><span data-stu-id="931db-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="931db-153">Webpack 會定義的進入點別名 `main-server` ; 而且，此別名的相依性圖形的遍歷會從 *ClientApp/boot.ini* 檔案開始：</span><span class="sxs-lookup"><span data-stu-id="931db-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="931db-154">在下列角度範例中， *ClientApp/boot.ini* 檔案會利用 npm 封裝的函式 `createServerRenderer` 和類型，透過 `RenderResult` `aspnet-prerendering` Node.js 設定伺服器轉譯。</span><span class="sxs-lookup"><span data-stu-id="931db-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="931db-155">以伺服器端轉譯為目標的 HTML 標籤會傳遞至解析函式呼叫，此呼叫會包裝在強型別 JavaScript `Promise` 物件中。</span><span class="sxs-lookup"><span data-stu-id="931db-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="931db-156">`Promise`物件的重要性是，它會以非同步方式將 HTML 標籤提供給頁面，以便在 DOM 的預留位置專案中插入。</span><span class="sxs-lookup"><span data-stu-id="931db-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="931db-157">asp-預先呈現-資料標記協助程式</span><span class="sxs-lookup"><span data-stu-id="931db-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="931db-158">加上標籤協助程式時，標籤協助 `asp-prerender-module` `asp-prerender-data` 程式可以用來將內容資訊從 Razor 視圖傳遞到伺服器端 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="931db-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="931db-159">例如，下列標記會將使用者資料傳遞給 `main-server` 模組：</span><span class="sxs-lookup"><span data-stu-id="931db-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="931db-160">接收的 `UserName` 引數會使用內建的 JSON 序列化程式進行序列化，並儲存在 `params.data` 物件中。</span><span class="sxs-lookup"><span data-stu-id="931db-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="931db-161">在下列角度範例中，資料會用來在元素內建立個人化問候語 `h1` ：</span><span class="sxs-lookup"><span data-stu-id="931db-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="931db-162">以標記協助程式傳遞的屬性名稱會以 **PascalCase** 標記法來表示。</span><span class="sxs-lookup"><span data-stu-id="931db-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="931db-163">相較于 JavaScript，相同的屬性名稱會以 **camelCase** 表示。</span><span class="sxs-lookup"><span data-stu-id="931db-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="931db-164">預設的 JSON 序列化設定負責這項差異。</span><span class="sxs-lookup"><span data-stu-id="931db-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="931db-165">若要展開上述的程式碼範例，您可以藉由 hydrating 提供給函數的屬性，將資料從伺服器傳遞到視圖 `globals` `resolve` ：</span><span class="sxs-lookup"><span data-stu-id="931db-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="931db-166">在 `postList` 物件內定義的陣列 `globals` 會附加至瀏覽器的全域 `window` 物件。</span><span class="sxs-lookup"><span data-stu-id="931db-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="931db-167">此變數 hoisting 至全域範圍會排除重複的工作，特別是在伺服器上，以及在用戶端上再次載入相同的資料。</span><span class="sxs-lookup"><span data-stu-id="931db-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![附加至 window 物件的全域 postList 變數](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="931db-169">Webpack Dev 中介軟體</span><span class="sxs-lookup"><span data-stu-id="931db-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="931db-170">[Webpack Dev 中介軟體](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) 引進了簡化的開發工作流程，讓 Webpack 依需求建立資源。</span><span class="sxs-lookup"><span data-stu-id="931db-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="931db-171">當瀏覽器中重載頁面時，中介軟體會自動編譯並服務用戶端資源。</span><span class="sxs-lookup"><span data-stu-id="931db-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="931db-172">替代方法是在協力廠商相依性或自訂程式碼變更時，透過專案的 npm 組建腳本來手動叫用 Webpack。</span><span class="sxs-lookup"><span data-stu-id="931db-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="931db-173">下列範例會顯示 *package.json* 檔案中的 npm 組建腳本：</span><span class="sxs-lookup"><span data-stu-id="931db-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="931db-174">Webpack Dev 中介軟體必要條件</span><span class="sxs-lookup"><span data-stu-id="931db-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="931db-175">安裝 [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm 套件：</span><span class="sxs-lookup"><span data-stu-id="931db-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="931db-176">Webpack Dev 中介軟體設定</span><span class="sxs-lookup"><span data-stu-id="931db-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="931db-177">Webpack Dev 中介軟體會透過 *Startup.cs* 檔的方法中的下列程式碼註冊至 HTTP 要求管線 `Configure` ：</span><span class="sxs-lookup"><span data-stu-id="931db-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="931db-178">`UseWebpackDevMiddleware`必須先呼叫擴充方法，才能透過擴充方法[註冊靜態檔案裝載](xref:fundamentals/static-files) `UseStaticFiles` 。</span><span class="sxs-lookup"><span data-stu-id="931db-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="931db-179">基於安全性理由，只有在應用程式在開發模式中執行時，才註冊中介軟體。</span><span class="sxs-lookup"><span data-stu-id="931db-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="931db-180">*webpack.config.js* 檔案的 `output.publicPath` 屬性會告知中介軟體監看 `dist` 資料夾的變更：</span><span class="sxs-lookup"><span data-stu-id="931db-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="931db-181">熱模組更換</span><span class="sxs-lookup"><span data-stu-id="931db-181">Hot Module Replacement</span></span>

<span data-ttu-id="931db-182">考慮 Webpack 的 [熱模組取代](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) 功能，作為 [Webpack 開發中介軟體](#webpack-dev-middleware)的演進。</span><span class="sxs-lookup"><span data-stu-id="931db-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="931db-183">HMR 引進了所有相同的優點，但它會在編譯變更後自動更新頁面內容，藉此簡化開發工作流程。</span><span class="sxs-lookup"><span data-stu-id="931db-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="931db-184">請勿將這項功能與重新整理瀏覽器混淆，這會干擾目前的記憶體內部狀態和 SPA 的偵測會話。</span><span class="sxs-lookup"><span data-stu-id="931db-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="931db-185">Webpack Dev 中介軟體服務和瀏覽器之間有即時連結，這表示會將變更推送至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="931db-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="931db-186">熱模組更換必要條件</span><span class="sxs-lookup"><span data-stu-id="931db-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="931db-187">安裝 [webpack-熱中介軟體](https://www.npmjs.com/package/webpack-hot-middleware) npm 套件：</span><span class="sxs-lookup"><span data-stu-id="931db-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="931db-188">熱模組更換設定</span><span class="sxs-lookup"><span data-stu-id="931db-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="931db-189">HMR 元件必須在方法中註冊至 MVC 的 HTTP 要求管線 `Configure` ：</span><span class="sxs-lookup"><span data-stu-id="931db-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="931db-190">如同 [Webpack Dev 中介軟體](#webpack-dev-middleware)的情況一樣，您 `UseWebpackDevMiddleware` 必須在擴充方法之前呼叫擴充方法 `UseStaticFiles` 。</span><span class="sxs-lookup"><span data-stu-id="931db-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="931db-191">基於安全性理由，只有在應用程式在開發模式中執行時，才註冊中介軟體。</span><span class="sxs-lookup"><span data-stu-id="931db-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="931db-192">*webpack.config.js* 檔案必須定義 `plugins` 陣列，即使它是空的也一樣：</span><span class="sxs-lookup"><span data-stu-id="931db-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="931db-193">在瀏覽器中載入應用程式之後，[開發人員工具] 的 [主控台] 索引標籤會提供 HMR 啟用的確認：</span><span class="sxs-lookup"><span data-stu-id="931db-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![熱模組取代連接的訊息](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="931db-195">路由協助程式</span><span class="sxs-lookup"><span data-stu-id="931db-195">Routing helpers</span></span>

<span data-ttu-id="931db-196">在大部分以 ASP.NET Core 為基礎的 Spa 中，除了伺服器端路由，通常還需要用戶端路由。</span><span class="sxs-lookup"><span data-stu-id="931db-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="931db-197">SPA 和 MVC 路由系統可以獨立運作，而不會受到干擾。</span><span class="sxs-lookup"><span data-stu-id="931db-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="931db-198">不過，有一個邊緣案例面臨挑戰：識別 404 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="931db-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="931db-199">請考慮使用無副檔名路由的案例 `/some/page` 。</span><span class="sxs-lookup"><span data-stu-id="931db-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="931db-200">假設要求不會與伺服器端路由進行模式比對，但其模式與用戶端路由相符。</span><span class="sxs-lookup"><span data-stu-id="931db-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="931db-201">現在請考慮的連入要求 `/images/user-512.png` ，這通常預期會在伺服器上找到影像檔案。</span><span class="sxs-lookup"><span data-stu-id="931db-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="931db-202">如果要求的資源路徑不符合任何伺服器端路由或靜態檔案，用戶端應用程式不太可能會處理此問題， &mdash; 通常會傳回所需的 404 HTTP 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="931db-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="931db-203">路由協助程式必要條件</span><span class="sxs-lookup"><span data-stu-id="931db-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="931db-204">安裝用戶端路由 npm 套件。</span><span class="sxs-lookup"><span data-stu-id="931db-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="931db-205">使用角度作為範例：</span><span class="sxs-lookup"><span data-stu-id="931db-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="931db-206">路由協助程式設定</span><span class="sxs-lookup"><span data-stu-id="931db-206">Routing helpers configuration</span></span>

<span data-ttu-id="931db-207">`MapSpaFallbackRoute`方法中會使用名為的擴充方法 `Configure` ：</span><span class="sxs-lookup"><span data-stu-id="931db-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="931db-208">路由會依照其設定順序進行評估。</span><span class="sxs-lookup"><span data-stu-id="931db-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="931db-209">因此， `default` 會先使用上述程式碼範例中的路由進行模式比對。</span><span class="sxs-lookup"><span data-stu-id="931db-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="931db-210">建立新專案</span><span class="sxs-lookup"><span data-stu-id="931db-210">Create a new project</span></span>

<span data-ttu-id="931db-211">JavaScript 服務提供預先設定的應用程式範本。</span><span class="sxs-lookup"><span data-stu-id="931db-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="931db-212">SpaServices 可用於這些範本，並搭配不同的架構和程式庫，例如角度、反應和 Redux。</span><span class="sxs-lookup"><span data-stu-id="931db-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="931db-213">您可以藉由執行下列命令，透過 .NET Core CLI 安裝這些範本：</span><span class="sxs-lookup"><span data-stu-id="931db-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="931db-214">會顯示可用的 SPA 範本清單：</span><span class="sxs-lookup"><span data-stu-id="931db-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="931db-215">範本</span><span class="sxs-lookup"><span data-stu-id="931db-215">Templates</span></span>                                 | <span data-ttu-id="931db-216">簡短名稱</span><span class="sxs-lookup"><span data-stu-id="931db-216">Short Name</span></span> | <span data-ttu-id="931db-217">語言</span><span class="sxs-lookup"><span data-stu-id="931db-217">Language</span></span> | <span data-ttu-id="931db-218">標籤</span><span class="sxs-lookup"><span data-stu-id="931db-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="931db-219">具有角度的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="931db-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="931db-220">angular</span><span class="sxs-lookup"><span data-stu-id="931db-220">angular</span></span>    | <span data-ttu-id="931db-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="931db-221">[C#]</span></span>     | <span data-ttu-id="931db-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="931db-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="931db-223">使用 React.js 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="931db-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="931db-224">react</span><span class="sxs-lookup"><span data-stu-id="931db-224">react</span></span>      | <span data-ttu-id="931db-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="931db-225">[C#]</span></span>     | <span data-ttu-id="931db-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="931db-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="931db-227">使用 React.js 和 Redux 的 MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="931db-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="931db-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="931db-228">reactredux</span></span> | <span data-ttu-id="931db-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="931db-229">[C#]</span></span>     | <span data-ttu-id="931db-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="931db-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="931db-231">若要使用其中一個 SPA 範本建立新的專案，請在 [dotnet new](/dotnet/core/tools/dotnet-new)命令中包含範本的 **簡短名稱**。</span><span class="sxs-lookup"><span data-stu-id="931db-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="931db-232">下列命令會建立一個具有針對伺服器端設定 ASP.NET Core MVC 的「角」應用程式：</span><span class="sxs-lookup"><span data-stu-id="931db-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="931db-233">設定執行時間設定模式</span><span class="sxs-lookup"><span data-stu-id="931db-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="931db-234">有兩個主要執行時間設定模式：</span><span class="sxs-lookup"><span data-stu-id="931db-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="931db-235">**開發**：</span><span class="sxs-lookup"><span data-stu-id="931db-235">**Development**:</span></span>
  * <span data-ttu-id="931db-236">包含來源對應以簡化調試。</span><span class="sxs-lookup"><span data-stu-id="931db-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="931db-237">不會優化用戶端程式代碼的效能。</span><span class="sxs-lookup"><span data-stu-id="931db-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="931db-238">**生產環境**：</span><span class="sxs-lookup"><span data-stu-id="931db-238">**Production**:</span></span>
  * <span data-ttu-id="931db-239">排除來源對應。</span><span class="sxs-lookup"><span data-stu-id="931db-239">Excludes source maps.</span></span>
  * <span data-ttu-id="931db-240">透過組合和縮制優化用戶端程式代碼。</span><span class="sxs-lookup"><span data-stu-id="931db-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="931db-241">ASP.NET Core 會使用名為的環境變數 `ASPNETCORE_ENVIRONMENT` 來儲存設定模式。</span><span class="sxs-lookup"><span data-stu-id="931db-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="931db-242">如需詳細資訊，請參閱 [設定環境](xref:fundamentals/environments#set-the-environment)。</span><span class="sxs-lookup"><span data-stu-id="931db-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="931db-243">使用 .NET Core CLI 執行</span><span class="sxs-lookup"><span data-stu-id="931db-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="931db-244">在專案根目錄中執行下列命令，以還原必要的 NuGet 和 npm 套件：</span><span class="sxs-lookup"><span data-stu-id="931db-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="931db-245">建立並執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="931db-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="931db-246">應用程式會根據執行時間設定 [模式](#set-the-runtime-configuration-mode)在 localhost 上啟動。</span><span class="sxs-lookup"><span data-stu-id="931db-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="931db-247">`http://localhost:5000`在瀏覽器中流覽至會顯示登陸頁面。</span><span class="sxs-lookup"><span data-stu-id="931db-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="931db-248">使用 Visual Studio 2017 執行</span><span class="sxs-lookup"><span data-stu-id="931db-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="931db-249">開啟 [dotnet new](/dotnet/core/tools/dotnet-new)命令所產生的 *.csproj* 檔案。</span><span class="sxs-lookup"><span data-stu-id="931db-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="931db-250">當專案開啟時，會自動還原必要的 NuGet 和 npm 套件。</span><span class="sxs-lookup"><span data-stu-id="931db-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="931db-251">此還原程式最多可能需要幾分鐘的時間，而且應用程式會在完成時準備好執行。</span><span class="sxs-lookup"><span data-stu-id="931db-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="931db-252">按一下綠色的 [執行] 按鈕或按下 `Ctrl + F5` ，瀏覽器會開啟至應用程式的登陸頁面。</span><span class="sxs-lookup"><span data-stu-id="931db-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="931db-253">應用程式會根據執行時間設定 [模式](#set-the-runtime-configuration-mode)在 localhost 上執行。</span><span class="sxs-lookup"><span data-stu-id="931db-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="931db-254">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="931db-254">Test the app</span></span>

<span data-ttu-id="931db-255">SpaServices 範本已預先設定為使用 [Karma](https://karma-runner.github.io/1.0/index.html) 和 [Jasmine](https://jasmine.github.io/)執行用戶端測試。</span><span class="sxs-lookup"><span data-stu-id="931db-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="931db-256">Jasmine 是適用于 JavaScript 的熱門單元測試架構，而 Karma 則是適用于這些測試的測試執行器。</span><span class="sxs-lookup"><span data-stu-id="931db-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="931db-257">Karma 已設定為使用 [Webpack Dev 中介軟體](#webpack-dev-middleware) ，因此開發人員不需要在每次進行變更時停止和執行測試。</span><span class="sxs-lookup"><span data-stu-id="931db-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="931db-258">無論是針對測試案例或測試案例本身執行的程式碼，測試都會自動執行。</span><span class="sxs-lookup"><span data-stu-id="931db-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="931db-259">使用「角度」應用程式做為範例中，已為 Jasmine 測試案例提供的範例檔案中有兩個 `CounterComponent` ： </span><span class="sxs-lookup"><span data-stu-id="931db-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="931db-260">在 *ClientApp* 目錄中開啟命令提示字元。</span><span class="sxs-lookup"><span data-stu-id="931db-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="931db-261">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="931db-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="931db-262">腳本會啟動 Karma 測試執行器，以讀取 *karma.conf.js* 檔案中定義的設定。</span><span class="sxs-lookup"><span data-stu-id="931db-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="931db-263">在其他設定中， *karma.conf.js* 會透過其陣列來識別要執行的測試檔案 `files` ：</span><span class="sxs-lookup"><span data-stu-id="931db-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="931db-264">發佈應用程式</span><span class="sxs-lookup"><span data-stu-id="931db-264">Publish the app</span></span>

<span data-ttu-id="931db-265">如需發佈至 Azure 的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/12474) 。</span><span class="sxs-lookup"><span data-stu-id="931db-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="931db-266">將產生的用戶端資產和已發佈的 ASP.NET Core 成品合併為立即部署的套件可能會很麻煩。</span><span class="sxs-lookup"><span data-stu-id="931db-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="931db-267">幸好，SpaServices 會使用名為的自訂 MSBuild 目標來協調整個發行流程 `RunWebpack` ：</span><span class="sxs-lookup"><span data-stu-id="931db-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="931db-268">MSBuild 目標有下列責任：</span><span class="sxs-lookup"><span data-stu-id="931db-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="931db-269">還原 npm 封裝。</span><span class="sxs-lookup"><span data-stu-id="931db-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="931db-270">建立協力廠商用戶端資產的生產層級組建。</span><span class="sxs-lookup"><span data-stu-id="931db-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="931db-271">建立自訂用戶端資產的生產層級組建。</span><span class="sxs-lookup"><span data-stu-id="931db-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="931db-272">將 Webpack 產生的資產複製到 [發佈] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="931db-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="931db-273">執行下列動作時，會叫用 MSBuild 目標：</span><span class="sxs-lookup"><span data-stu-id="931db-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="931db-274">其他資源</span><span class="sxs-lookup"><span data-stu-id="931db-274">Additional resources</span></span>

* [<span data-ttu-id="931db-275">角度檔</span><span class="sxs-lookup"><span data-stu-id="931db-275">Angular Docs</span></span>](https://angular.io/docs)
