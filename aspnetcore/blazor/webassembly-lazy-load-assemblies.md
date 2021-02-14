---
title: ASP.NET Core 中的延遲載入元件 Blazor WebAssembly
author: guardrex
description: 探索如何在 ASP.NET Core 應用程式中延遲載入元件 Blazor WebAssembly 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: e8589a1e288c39b487673fafc04c59fa07916335
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280667"
---
# <a name="lazy-load-assemblies-in-aspnet-core-blazor-webassembly"></a><span data-ttu-id="3a541-103">ASP.NET Core 中的延遲載入元件 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3a541-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="3a541-104">Blazor WebAssembly 應用程式啟動效能可透過延後載入部分應用程式元件來改善，直到需要它們為止，這稱為「消極式 *載入*」。</span><span class="sxs-lookup"><span data-stu-id="3a541-104">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="3a541-105">例如，只有在使用者流覽至該元件時，才可以設定僅用來呈現單一元件的元件。</span><span class="sxs-lookup"><span data-stu-id="3a541-105">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="3a541-106">載入之後，元件會快取用戶端，並可供所有未來的導覽使用。</span><span class="sxs-lookup"><span data-stu-id="3a541-106">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="3a541-107">Blazor的消極式載入功能可讓您將應用程式元件標記為消極式載入，這會在使用者流覽至特定路由時，于執行時間載入元件。</span><span class="sxs-lookup"><span data-stu-id="3a541-107">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="3a541-108">此功能包含對專案檔的變更，以及對應用程式路由器的變更。</span><span class="sxs-lookup"><span data-stu-id="3a541-108">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="3a541-109">元件消極式載入不會讓應用程式受益， Blazor Server 因為元件不會下載到應用程式中的用戶端 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="3a541-109">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="3a541-110">專案檔</span><span class="sxs-lookup"><span data-stu-id="3a541-110">Project file</span></span>

<span data-ttu-id="3a541-111">使用專案將應用程式專案檔中的消極式載入的元件標記 (`.csproj`) `BlazorWebAssemblyLazyLoad` 。</span><span class="sxs-lookup"><span data-stu-id="3a541-111">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="3a541-112">使用具有副檔名的元件名稱 `.dll` 。</span><span class="sxs-lookup"><span data-stu-id="3a541-112">Use the assembly name with the `.dll` extension.</span></span> <span data-ttu-id="3a541-113">Blazor架構會防止此專案群組所指定的元件在應用程式啟動時載入。</span><span class="sxs-lookup"><span data-stu-id="3a541-113">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="3a541-114">下列範例會將大型自訂群組件標示 `GrantImaharaRobotControls.dll` 為延遲載入 () 。</span><span class="sxs-lookup"><span data-stu-id="3a541-114">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="3a541-115">如果標示為消極式載入的元件有相依性，則也必須在專案檔中將它們標示為消極式載入。</span><span class="sxs-lookup"><span data-stu-id="3a541-115">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a><span data-ttu-id="3a541-116">`Router` 元件</span><span class="sxs-lookup"><span data-stu-id="3a541-116">`Router` component</span></span>

<span data-ttu-id="3a541-117">Blazor的 `Router` 元件會指定哪些元件會 Blazor 搜尋可路由傳送的元件。</span><span class="sxs-lookup"><span data-stu-id="3a541-117">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="3a541-118">`Router`元件也負責轉譯使用者導覽之路由的元件。</span><span class="sxs-lookup"><span data-stu-id="3a541-118">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="3a541-119">`Router`元件支援 `OnNavigateAsync` 可與消極式載入搭配使用的功能。</span><span class="sxs-lookup"><span data-stu-id="3a541-119">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="3a541-120">在應用程式的 `Router` 元件中 (`App.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="3a541-120">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="3a541-121">新增 `OnNavigateAsync` 回呼。</span><span class="sxs-lookup"><span data-stu-id="3a541-121">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="3a541-122">`OnNavigateAsync`當使用者執行下列動作時，就會叫用處理程式：</span><span class="sxs-lookup"><span data-stu-id="3a541-122">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="3a541-123">第一次造訪路由，方法是直接從瀏覽器流覽至。</span><span class="sxs-lookup"><span data-stu-id="3a541-123">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="3a541-124">使用連結或調用來流覽至新的路由 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="3a541-124">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="3a541-125">如果延遲載入的元件包含可路由傳送的元件，請將[清單](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (例如，將) 命名為 `lazyLoadedAssemblies` 元件。</span><span class="sxs-lookup"><span data-stu-id="3a541-125">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="3a541-126">如果元件包含可路由傳送的元件，這些元件就會傳回 <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> 集合。</span><span class="sxs-lookup"><span data-stu-id="3a541-126">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="3a541-127">架構會在元件中搜尋路由，並在找到任何新的路由時更新路由集合。</span><span class="sxs-lookup"><span data-stu-id="3a541-127">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="3a541-128">如果 `OnNavigateAsync` 回呼擲回未處理的例外狀況，則會叫用[ Blazor 錯誤 UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) 。</span><span class="sxs-lookup"><span data-stu-id="3a541-128">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="3a541-129">中的元件載入邏輯 `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="3a541-129">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="3a541-130">`OnNavigateAsync` 具有 `NavigationContext` 提供目前非同步導覽事件相關資訊的參數，包括目標路徑 (`Path`) ，以及 () 的解除標記 `CancellationToken` ：</span><span class="sxs-lookup"><span data-stu-id="3a541-130">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="3a541-131">`Path`屬性是相對於應用程式基底路徑的使用者目的地路徑，例如 `/robot` 。</span><span class="sxs-lookup"><span data-stu-id="3a541-131">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="3a541-132">`CancellationToken`可以用來觀察非同步工作的取消。</span><span class="sxs-lookup"><span data-stu-id="3a541-132">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="3a541-133">`OnNavigateAsync` 當使用者流覽至另一個頁面時，會自動取消目前正在執行的流覽工作。</span><span class="sxs-lookup"><span data-stu-id="3a541-133">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="3a541-134">在內部 `OnNavigateAsync` ，會執行邏輯來判斷要載入的元件。</span><span class="sxs-lookup"><span data-stu-id="3a541-134">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="3a541-135">這些選項包括：</span><span class="sxs-lookup"><span data-stu-id="3a541-135">Options include:</span></span>

* <span data-ttu-id="3a541-136">方法內的條件式檢查 `OnNavigateAsync` 。</span><span class="sxs-lookup"><span data-stu-id="3a541-136">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="3a541-137">對應至元件名稱之路由的查閱資料表，可插入元件或在區塊內執行 [`@code`](xref:mvc/views/razor#code) 。</span><span class="sxs-lookup"><span data-stu-id="3a541-137">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="3a541-138">`LazyAssemblyLoader` 是用於載入元件的架構提供的單一服務。</span><span class="sxs-lookup"><span data-stu-id="3a541-138">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="3a541-139">插入 `LazyAssemblyLoader` `Router` 元件：</span><span class="sxs-lookup"><span data-stu-id="3a541-139">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="3a541-140">`LazyAssemblyLoader`提供 `LoadAssembliesAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="3a541-140">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="3a541-141">使用 JS interop 透過網路呼叫來提取元件。</span><span class="sxs-lookup"><span data-stu-id="3a541-141">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="3a541-142">將元件載入至在瀏覽器中于 WebAssembly 上執行的執行時間。</span><span class="sxs-lookup"><span data-stu-id="3a541-142">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

<span data-ttu-id="3a541-143">架構的消極式載入實行支援在裝載的解決方案中，使用可進行的延遲式載入 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="3a541-143">The framework's lazy loading implementation supports lazy loading with prerendering in a hosted Blazor solution.</span></span> <span data-ttu-id="3a541-144">在預進行期間，會假設載入所有元件，包括標示為消極式載入的元件。</span><span class="sxs-lookup"><span data-stu-id="3a541-144">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span> <span data-ttu-id="3a541-145">以手動 `LazyAssemblyLoader` 方式在 *伺服器* 專案的 `Startup.ConfigureServices` 方法 (`Startup.cs`) 中註冊：</span><span class="sxs-lookup"><span data-stu-id="3a541-145">Manually register `LazyAssemblyLoader` in the *Server* project's `Startup.ConfigureServices` method (`Startup.cs`):</span></span>

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="3a541-146">使用者與內容的互動 `<Navigating>`</span><span class="sxs-lookup"><span data-stu-id="3a541-146">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="3a541-147">載入元件（可能需要幾秒鐘的時間）時， `Router` 元件可能會向使用者表示頁面轉換發生：</span><span class="sxs-lookup"><span data-stu-id="3a541-147">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="3a541-148">新增命名空間的指示詞 [`@using`](xref:mvc/views/razor#using) <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 。</span><span class="sxs-lookup"><span data-stu-id="3a541-148">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="3a541-149">將 `<Navigating>` 標記新增至元件，並在頁面轉換事件期間顯示標記。</span><span class="sxs-lookup"><span data-stu-id="3a541-149">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="3a541-150">處理取消 `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="3a541-150">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="3a541-151">`NavigationContext`傳遞至回呼的物件 `OnNavigateAsync` 包含新的 `CancellationToken` 流覽事件發生時所設定的。</span><span class="sxs-lookup"><span data-stu-id="3a541-151">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="3a541-152">`OnNavigateAsync`回呼必須在設定此解除標記時擲回，以避免 `OnNavigateAsync` 在過期的導覽上繼續執行回呼。</span><span class="sxs-lookup"><span data-stu-id="3a541-152">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="3a541-153">如果使用者流覽至路由 A，然後立即路由 B，則應用程式不應該繼續執行 `OnNavigateAsync` 路由 a 的回呼：</span><span class="sxs-lookup"><span data-stu-id="3a541-153">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

> [!NOTE]
> <span data-ttu-id="3a541-154">如果取消中的解除標記 `NavigationContext` 可能會導致非預期的行為，例如從先前的導覽呈現元件，則不會擲回。</span><span class="sxs-lookup"><span data-stu-id="3a541-154">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="3a541-155">`OnNavigateAsync` 事件和重新命名的元件檔案</span><span class="sxs-lookup"><span data-stu-id="3a541-155">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="3a541-156">資源載入器會依賴檔案中定義的元件名稱 `blazor.boot.json` 。</span><span class="sxs-lookup"><span data-stu-id="3a541-156">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="3a541-157">如果重新 [命名元件](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files)，則方法中使用的元件名稱 `OnNavigateAsync` 和檔案中的元件名稱 `blazor.boot.json` 將不會同步。</span><span class="sxs-lookup"><span data-stu-id="3a541-157">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="3a541-158">若要修正此情況：</span><span class="sxs-lookup"><span data-stu-id="3a541-158">To rectify this:</span></span>

* <span data-ttu-id="3a541-159">檢查應用程式是否正在生產環境中執行，以判斷要使用的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="3a541-159">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="3a541-160">將重新命名的元件名稱儲存在另一個檔案中，並從該檔案讀取，以判斷要在和方法中使用的元件名稱 `LazyLoadAssemblyService` `OnNavigateAsync` 。</span><span class="sxs-lookup"><span data-stu-id="3a541-160">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="3a541-161">完整範例</span><span class="sxs-lookup"><span data-stu-id="3a541-161">Complete example</span></span>

<span data-ttu-id="3a541-162">下列完整 `Router` 元件示範在 `GrantImaharaRobotControls.dll` 使用者流覽至時載入元件 `/robot` 。</span><span class="sxs-lookup"><span data-stu-id="3a541-162">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="3a541-163">在頁面轉換期間，會向使用者顯示樣式的訊息。</span><span class="sxs-lookup"><span data-stu-id="3a541-163">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="troubleshoot"></a><span data-ttu-id="3a541-164">疑難排解</span><span class="sxs-lookup"><span data-stu-id="3a541-164">Troubleshoot</span></span>

* <span data-ttu-id="3a541-165">如果發生非預期的轉譯 (例如，先前導覽中的元件會轉譯) ，確認程式碼會在已設定解除標記時擲回。</span><span class="sxs-lookup"><span data-stu-id="3a541-165">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="3a541-166">如果元件仍在應用程式啟動時載入，請確認元件在專案檔中已標示為延遲載入。</span><span class="sxs-lookup"><span data-stu-id="3a541-166">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a541-167">其他資源</span><span class="sxs-lookup"><span data-stu-id="3a541-167">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
