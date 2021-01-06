---
title: Razor ASP.NET Core 中的頁面路由和應用程式慣例
author: rick-anderson
description: 探索路由和應用程式模型提供者慣例如何協助您控制頁面路由、探索與處理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 2947bf0b697ca01f17d260b9f31aa3cc79d457b6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059867"
---
# <a name="no-locrazor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="55138-103">Razor ASP.NET Core 中的頁面路由和應用程式慣例</span><span class="sxs-lookup"><span data-stu-id="55138-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="55138-104">瞭解如何使用頁面 [路由和應用程式模型提供者慣例](xref:mvc/controllers/application-model#conventions) ，來控制頁面應用程式中的頁面路由、探索和處理 Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="55138-105">當您需要設定個別頁面的自訂頁面路由時，請使用本主題稍後所述的 [AddPageRoute 慣例](#configure-a-page-route)來設定路由至頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="55138-106">若要指定頁面路由、加入路由區段或將參數加入至路由，請使用頁面的指示詞 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="55138-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="55138-107">如需詳細資訊，請參閱 [自訂路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="55138-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="55138-108">有保留字無法用作路由區段或參數名稱。</span><span class="sxs-lookup"><span data-stu-id="55138-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="55138-109">如需詳細資訊，請參閱 [路由：保留的路由名稱](xref:mvc/controllers/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="55138-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="55138-110">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="55138-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="55138-111">案例</span><span class="sxs-lookup"><span data-stu-id="55138-111">Scenario</span></span> | <span data-ttu-id="55138-112">範例會示範 ...</span><span class="sxs-lookup"><span data-stu-id="55138-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="55138-113">模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="55138-114">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="55138-114">Conventions.Add</span></span><ul><li><span data-ttu-id="55138-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="55138-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="55138-118">將路由範本和標頭新增至應用程式的頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="55138-119">頁面路由動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="55138-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="55138-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="55138-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="55138-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="55138-123">將路由範本新增至資料夾中的頁面，以及新增至單一頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="55138-124">頁面模型動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="55138-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-127">ConfigureFilter (篩選類別、Lambda 運算式或篩選 Factory)</span><span class="sxs-lookup"><span data-stu-id="55138-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="55138-128">將標頭新增至資料夾中的頁面、將標頭新增至單一頁面，以及設定[篩選條件 Factory](xref:mvc/controllers/filters#ifilterfactory) 將標頭新增至應用程式的頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="55138-129">Razor 頁面慣例是使用 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 在中設定的多載來設定 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="55138-129">Razor Pages conventions are configured using an <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="55138-130">稍後在本主題會說明下列慣例範例：</span><span class="sxs-lookup"><span data-stu-id="55138-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a><span data-ttu-id="55138-131">路由順序</span><span class="sxs-lookup"><span data-stu-id="55138-131">Route order</span></span>

<span data-ttu-id="55138-132">路由會指定 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 處理 (路由符合) 的。</span><span class="sxs-lookup"><span data-stu-id="55138-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="55138-133">單</span><span class="sxs-lookup"><span data-stu-id="55138-133">Order</span></span>            | <span data-ttu-id="55138-134">行為</span><span class="sxs-lookup"><span data-stu-id="55138-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="55138-135">-1</span><span class="sxs-lookup"><span data-stu-id="55138-135">-1</span></span>               | <span data-ttu-id="55138-136">路由會在處理其他路由之前處理。</span><span class="sxs-lookup"><span data-stu-id="55138-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="55138-137">0</span><span class="sxs-lookup"><span data-stu-id="55138-137">0</span></span>                | <span data-ttu-id="55138-138"> (預設值) 不會指定順序。</span><span class="sxs-lookup"><span data-stu-id="55138-138">Order isn't specified (default value).</span></span> <span data-ttu-id="55138-139">若未指派 `Order` (`Order = null`) 會將路由預設為 `Order` 0 (零) 以進行處理。</span><span class="sxs-lookup"><span data-stu-id="55138-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="55138-140">1、2、 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="55138-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="55138-141">指定路由處理順序。</span><span class="sxs-lookup"><span data-stu-id="55138-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="55138-142">路由處理是依照慣例來建立：</span><span class="sxs-lookup"><span data-stu-id="55138-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="55138-143">路由會依照連續處理 (-1、0、1、2、 &hellip; n) 。</span><span class="sxs-lookup"><span data-stu-id="55138-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="55138-144">當路由相同時 `Order` ，會先比對最特定的路由，再接著較不特定的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="55138-145">當具有相同 `Order` 和相同數目之參數的路由符合要求 URL 時，會依其新增至的順序來處理路由 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 。</span><span class="sxs-lookup"><span data-stu-id="55138-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="55138-146">可能的話，請避免根據已建立的路由處理順序。</span><span class="sxs-lookup"><span data-stu-id="55138-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="55138-147">一般而言，路由會選取 URL 相符的正確路由。</span><span class="sxs-lookup"><span data-stu-id="55138-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="55138-148">如果您必須設定路由 `Order` 屬性以正確地路由傳送要求，則應用程式的路由配置可能會讓用戶端和脆弱的維護產生混淆。</span><span class="sxs-lookup"><span data-stu-id="55138-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="55138-149">搜尋以簡化應用程式的路由配置。</span><span class="sxs-lookup"><span data-stu-id="55138-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="55138-150">範例應用程式需要明確的路由處理順序，以使用單一應用程式來示範數個路由案例。</span><span class="sxs-lookup"><span data-stu-id="55138-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="55138-151">不過，您應該嘗試避免在 `Order` 生產應用程式中設定路由的做法。</span><span class="sxs-lookup"><span data-stu-id="55138-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="55138-152">Razor 頁面路由和 MVC 控制器路由會共用執行。</span><span class="sxs-lookup"><span data-stu-id="55138-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="55138-153">您可以在 [路由傳送至控制器動作：排序屬性路由](xref:mvc/controllers/routing#ordering-attribute-routes)時，取得 MVC 主題中路由順序的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="55138-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="55138-154">模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-154">Model conventions</span></span>

<span data-ttu-id="55138-155">加入的委派， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 以新增適用于頁面的 [模型慣例](xref:mvc/controllers/application-model#conventions) Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="55138-156">將路由模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="55138-157">您 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 可以使用來建立，並將加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面路由模型結構中套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="55138-158">範例應用程式將 `{globalTemplate?}` 路由範本新增至應用程式中的所有頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-159"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `1`。</span><span class="sxs-lookup"><span data-stu-id="55138-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="55138-160">這可確保範例應用程式中的下列路由符合行為：</span><span class="sxs-lookup"><span data-stu-id="55138-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="55138-161">的路由範本 `TheContactPage/{text?}` 稍後會在主題中新增。</span><span class="sxs-lookup"><span data-stu-id="55138-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="55138-162">連絡人頁面路由的預設順序是 `null` (`Order = 0`) ，因此它會符合 `{globalTemplate?}` 路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="55138-163">`{aboutTemplate?}`稍後會在主題中新增路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="55138-164">`{aboutTemplate?}` 範本會指定 `Order` 為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="55138-165">在 `/About/RouteDataValue` 上要求 About 頁面時，由於設定 `Order` 屬性之故，因此 "RouteDataValue" 會載入至 `RouteData.Values["globalTemplate"]` (`Order = 1`)，而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`)。</span><span class="sxs-lookup"><span data-stu-id="55138-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="55138-166">`{otherPagesTemplate?}`稍後會在主題中新增路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="55138-167">`{otherPagesTemplate?}` 範本會指定 `Order` 為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="55138-168">使用路由參數要求 *Pages/OtherPages* 資料夾中的任何頁面時 (例如， `/OtherPages/Page1/RouteDataValue`) ，"因此 routedatavalue" 會載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不是 `RouteData.Values["otherPagesTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-169">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-170">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-171">Razor<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>當 Razor 頁面加入至中的服務集合時，會加入頁面選項，例如加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="55138-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when Razor Pages is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="55138-172">如需範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="55138-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="55138-173">在 `localhost:5000/About/GlobalRouteValue` 上要求範例的 About 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用路由區段 GlobalRouteValue 要求 About 頁面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="55138-176">將應用程式模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="55138-177">用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 來建立和新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面應用程式模型結構期間套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="55138-178">為了示範這個慣例和本主題稍後的其他慣例，範例應用程式包含了 `AddHeaderAttribute` 類別。</span><span class="sxs-lookup"><span data-stu-id="55138-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="55138-179">類別建構函式接受 `name` 字串和 `values` 字串陣列。</span><span class="sxs-lookup"><span data-stu-id="55138-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="55138-180">在其 `OnResultExecuting` 方法中使用這些值來設定回應標頭。</span><span class="sxs-lookup"><span data-stu-id="55138-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="55138-181">完整的類別顯示在本主題稍後的[頁面模型動作慣例](#page-model-action-conventions)一節中。</span><span class="sxs-lookup"><span data-stu-id="55138-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="55138-182">範例應用程式會使用 `AddHeaderAttribute` 類別，將標頭 `GlobalHeader` 新增至應用程式中的所有頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-183">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="55138-184">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="55138-186">將處理常式模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="55138-187">您 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 可以使用來建立，並將加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面處理常式模型結構中套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-188">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="55138-189">頁面路由動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-189">Page route action conventions</span></span>

<span data-ttu-id="55138-190">衍生自的預設路由模型提供者， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 其設計目的是為了提供設定頁面路由的擴充點。</span><span class="sxs-lookup"><span data-stu-id="55138-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="55138-191">資料夾路由模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-191">Folder route model convention</span></span>

<span data-ttu-id="55138-192">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 針對指定資料夾下的所有頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="55138-193">範例應用程式會使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>，將 `{otherPagesTemplate?}` 路由範本新增至 *OtherPages* 資料夾中的頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="55138-194"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="55138-195">這可確保 `{globalTemplate?}` `1` 當提供單一路由值時，會針對第一個路由資料值的位置，指定在主題中稍早設定) 的 (範本。</span><span class="sxs-lookup"><span data-stu-id="55138-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="55138-196">如果以路由參數值要求 *Pages/OtherPages* 資料夾中的頁面 (例如， `/OtherPages/Page1/RouteDataValue`) ，就會將 "因此 routedatavalue" 載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不是 `RouteData.Values["otherPagesTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-197">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-198">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-199">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 上要求範例的 Page1 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![以 GlobalRouteValue 和 OtherPagesRouteValue 的路由區段要求 OtherPages 資料夾中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="55138-202">頁面路由模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-202">Page route model convention</span></span>

<span data-ttu-id="55138-203">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 針對具有指定名稱的頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="55138-204">範例應用程式會使用 `AddPageRouteModelConvention`，將 `{aboutTemplate?}` 路由範本新增至 About 頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="55138-205"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="55138-206">這可確保 `{globalTemplate?}` `1` 當提供單一路由值時，會針對第一個路由資料值的位置，指定在主題中稍早設定) 的 (範本。</span><span class="sxs-lookup"><span data-stu-id="55138-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="55138-207">如果以路由參數值（位於）要求 [關於 `/About/RouteDataValue` ] 頁面，則會將 "因此 routedatavalue" 載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不會 `RouteData.Values["aboutTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-208">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-209">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-210">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 上要求範例的 About 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![以 GlobalRouteValue 和 AboutRouteValue 的路由區段要求 About 頁面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="55138-213">使用參數轉換器自訂頁面路由</span><span class="sxs-lookup"><span data-stu-id="55138-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="55138-214">您可以使用參數轉換程式自訂 ASP.NET Core 所產生的頁面路由。</span><span class="sxs-lookup"><span data-stu-id="55138-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="55138-215">參數轉換程式會實作 `IOutboundParameterTransformer` 並轉換參數值。</span><span class="sxs-lookup"><span data-stu-id="55138-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="55138-216">例如，自訂 `SlugifyParameterTransformer` 參數轉換器會將 `SubscriptionManagement` 路由值變更為 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="55138-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="55138-217">`PageRouteTransformerConvention`頁面路由模型慣例會將參數轉換器套用至應用程式中自動產生之頁面路由的資料夾和檔案名區段。</span><span class="sxs-lookup"><span data-stu-id="55138-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="55138-218">例如， Razor 在 */Pages/SubscriptionManagement/ViewAll.cshtml* 的頁面檔中，會將其路由從重寫 `/SubscriptionManagement/ViewAll` 到 `/subscription-management/view-all` 。</span><span class="sxs-lookup"><span data-stu-id="55138-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="55138-219">`PageRouteTransformerConvention` 只會轉換來自 Razor Pages 資料夾和檔案名的頁面路由自動產生區段。</span><span class="sxs-lookup"><span data-stu-id="55138-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="55138-220">它不會轉換使用指示詞加入的路由區段 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="55138-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="55138-221">慣例也不會轉換所加入的路由 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 。</span><span class="sxs-lookup"><span data-stu-id="55138-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="55138-222">`PageRouteTransformerConvention`註冊為中的選項 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="55138-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="55138-223">設定頁面路由</span><span class="sxs-lookup"><span data-stu-id="55138-223">Configure a page route</span></span>

<span data-ttu-id="55138-224">您 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 可以使用，在指定的頁面路徑上設定頁面的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="55138-225">頁面的所產生連結會使用您指定的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="55138-226">`AddPageRoute` 使用 `AddPageRouteModelConvention` 來建立路由。</span><span class="sxs-lookup"><span data-stu-id="55138-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="55138-227">範例應用程式為 *Contact.cshtml* 建立 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="55138-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="55138-228">Contact 頁面也可以透過其預設路由在 `/Contact` 上連線。</span><span class="sxs-lookup"><span data-stu-id="55138-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="55138-229">範例應用程式的 Contact 頁面自訂路由允許使用選擇性的 `text` 路由區段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="55138-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="55138-230">該頁面也會在其 `@page` 指示詞中包含這個選擇性區段，以防訪客在其 `/Contact` 路由上存取頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="55138-231">請注意，呈現頁面中針對 **Contact** 連結所產生的 URL 會反映更新的路由：</span><span class="sxs-lookup"><span data-stu-id="55138-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![導覽列中的範例應用程式 Contact 連結](razor-pages-conventions/_static/contact-link.png)

![檢查所呈現 HTML 中的 Contact 連結會指出 href 設定為 '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="55138-234">請在其一般路由 `/Contact` 或自訂路由 `/TheContactPage` 上瀏覽 Contact 頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="55138-235">如果您提供額外的 `text` 路由區段，該頁面會顯示您提供的 HTML 編碼區段：</span><span class="sxs-lookup"><span data-stu-id="55138-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供 'TextValue' 的選擇性 'text' 路由區段的 Edge 瀏覽器範例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="55138-238">頁面模型動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-238">Page model action conventions</span></span>

<span data-ttu-id="55138-239">實值的預設頁面模型提供者， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 其設計目的是要提供設定頁面模型的擴充點。</span><span class="sxs-lookup"><span data-stu-id="55138-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="55138-240">在建置和修改頁面探索與處理案例時，這些慣例很有用。</span><span class="sxs-lookup"><span data-stu-id="55138-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="55138-241">針對本節中的範例，範例應用程式 `AddHeaderAttribute` 會使用類別，也就是 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> 套用回應標頭的類別：</span><span class="sxs-lookup"><span data-stu-id="55138-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="55138-242">透過慣例，此範例示範如何將屬性套用至資料夾中的所有頁面，以及套用至單一頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="55138-243">**資料夾應用程式模型慣例**</span><span class="sxs-lookup"><span data-stu-id="55138-243">**Folder app model convention**</span></span>

<span data-ttu-id="55138-244">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可以使用來建立並加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 針對指定資料夾下的所有頁面，在實例上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="55138-245">此範例示範如何將標頭 `OtherPagesHeader` 新增至應用程式的 *OtherPages* 資料夾內的頁面來使用 `AddFolderApplicationModelConvention`：</span><span class="sxs-lookup"><span data-stu-id="55138-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="55138-246">在 `localhost:5000/OtherPages/Page1` 上要求範例的 Page1 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 頁面的回應標頭會顯示已新增 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="55138-248">**頁面應用程式模型慣例**</span><span class="sxs-lookup"><span data-stu-id="55138-248">**Page app model convention**</span></span>

<span data-ttu-id="55138-249">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 針對具有指定名稱的頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="55138-250">此範例示範如何將標頭 `AboutHeader` 新增至 About 頁面來使用 `AddPageApplicationModelConvention`：</span><span class="sxs-lookup"><span data-stu-id="55138-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="55138-251">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="55138-253">**設定篩選條件**</span><span class="sxs-lookup"><span data-stu-id="55138-253">**Configure a filter**</span></span>

<span data-ttu-id="55138-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 設定要套用的指定篩選。</span><span class="sxs-lookup"><span data-stu-id="55138-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="55138-255">您可以實作篩選條件類別，但範例應用程式是示範如何在 Lambda 運算式中實作篩選條件，該運算式會在幕後實作為 Factory 以傳回篩選條件：</span><span class="sxs-lookup"><span data-stu-id="55138-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="55138-256">使用頁面應用程式模型，可針對指向 *OtherPages* 資料夾內 Page2 頁面的區段檢查相對路徑。</span><span class="sxs-lookup"><span data-stu-id="55138-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="55138-257">如果通過條件，則會新增標頭。</span><span class="sxs-lookup"><span data-stu-id="55138-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="55138-258">如果沒有，則會套用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="55138-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="55138-259">`EmptyFilter` 是[動作篩選條件](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="55138-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="55138-260">由於頁面會忽略動作篩選準則 Razor ，因此 `EmptyFilter` 如果路徑未包含，就不會有任何作用 `OtherPages/Page2` 。</span><span class="sxs-lookup"><span data-stu-id="55138-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="55138-261">在 `localhost:5000/OtherPages/Page2` 上要求範例的 Page2 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 會新增至 Page2 的回應。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="55138-263">**設定篩選條件 Factory**</span><span class="sxs-lookup"><span data-stu-id="55138-263">**Configure a filter factory**</span></span>

<span data-ttu-id="55138-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 設定指定的 factory 將 [篩選](xref:mvc/controllers/filters) 套用至所有 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="55138-265">範例應用程式示範如何以應用程式頁面的兩個值新增標頭 `FilterFactoryHeader` 來使用[篩選條件 Factory](xref:mvc/controllers/filters#ifilterfactory)：</span><span class="sxs-lookup"><span data-stu-id="55138-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="55138-266">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="55138-267">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增兩個 FilterFactoryHeader 標頭。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="55138-269">MVC 篩選條件和頁面篩選條件 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="55138-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="55138-270">頁面會忽略 MVC [動作篩選](xref:mvc/controllers/filters#action-filters) Razor ，因為 Razor 頁面會使用處理程式方法。</span><span class="sxs-lookup"><span data-stu-id="55138-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="55138-271">其他類型的 MVC 篩選條件可供您使用：[Authorization](xref:mvc/controllers/filters#authorization-filters)、[Exception](xref:mvc/controllers/filters#exception-filters)、[Resource](xref:mvc/controllers/filters#resource-filters) 和 [Result](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="55138-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="55138-272">如需詳細資訊，請參閱[篩選條件](xref:mvc/controllers/filters)主題。</span><span class="sxs-lookup"><span data-stu-id="55138-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="55138-273">頁面篩選 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是適用于頁面的篩選準則 Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="55138-274">如需詳細資訊，請參閱 [ Razor 頁面的篩選方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="55138-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="55138-275">其他資源</span><span class="sxs-lookup"><span data-stu-id="55138-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="55138-276">瞭解如何使用頁面 [路由和應用程式模型提供者慣例](xref:mvc/controllers/application-model#conventions) ，來控制頁面應用程式中的頁面路由、探索和處理 Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="55138-277">當您需要設定個別頁面的自訂頁面路由時，請使用本主題稍後所述的 [AddPageRoute 慣例](#configure-a-page-route)來設定路由至頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="55138-278">若要指定頁面路由、加入路由區段或將參數加入至路由，請使用頁面的指示詞 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="55138-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="55138-279">如需詳細資訊，請參閱 [自訂路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="55138-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="55138-280">有保留字無法用作路由區段或參數名稱。</span><span class="sxs-lookup"><span data-stu-id="55138-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="55138-281">如需詳細資訊，請參閱 [路由：保留的路由名稱](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="55138-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="55138-282">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="55138-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="55138-283">案例</span><span class="sxs-lookup"><span data-stu-id="55138-283">Scenario</span></span> | <span data-ttu-id="55138-284">範例會示範 ...</span><span class="sxs-lookup"><span data-stu-id="55138-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="55138-285">模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="55138-286">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="55138-286">Conventions.Add</span></span><ul><li><span data-ttu-id="55138-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="55138-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="55138-290">將路由範本和標頭新增至應用程式的頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="55138-291">頁面路由動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="55138-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="55138-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="55138-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="55138-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="55138-295">將路由範本新增至資料夾中的頁面，以及新增至單一頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="55138-296">頁面模型動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="55138-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-299">ConfigureFilter (篩選類別、Lambda 運算式或篩選 Factory)</span><span class="sxs-lookup"><span data-stu-id="55138-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="55138-300">將標頭新增至資料夾中的頁面、將標頭新增至單一頁面，以及設定[篩選條件 Factory](xref:mvc/controllers/filters#ifilterfactory) 將標頭新增至應用程式的頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="55138-301">Razor 頁面慣例會在 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 類別中的服務集合上使用擴充方法來新增和設定 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="55138-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="55138-302">稍後在本主題會說明下列慣例範例：</span><span class="sxs-lookup"><span data-stu-id="55138-302">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="55138-303">路由順序</span><span class="sxs-lookup"><span data-stu-id="55138-303">Route order</span></span>

<span data-ttu-id="55138-304">路由會指定 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 處理 (路由符合) 的。</span><span class="sxs-lookup"><span data-stu-id="55138-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="55138-305">單</span><span class="sxs-lookup"><span data-stu-id="55138-305">Order</span></span>            | <span data-ttu-id="55138-306">行為</span><span class="sxs-lookup"><span data-stu-id="55138-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="55138-307">-1</span><span class="sxs-lookup"><span data-stu-id="55138-307">-1</span></span>               | <span data-ttu-id="55138-308">路由會在處理其他路由之前處理。</span><span class="sxs-lookup"><span data-stu-id="55138-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="55138-309">0</span><span class="sxs-lookup"><span data-stu-id="55138-309">0</span></span>                | <span data-ttu-id="55138-310"> (預設值) 不會指定順序。</span><span class="sxs-lookup"><span data-stu-id="55138-310">Order isn't specified (default value).</span></span> <span data-ttu-id="55138-311">若未指派 `Order` (`Order = null`) 會將路由預設為 `Order` 0 (零) 以進行處理。</span><span class="sxs-lookup"><span data-stu-id="55138-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="55138-312">1、2、 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="55138-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="55138-313">指定路由處理順序。</span><span class="sxs-lookup"><span data-stu-id="55138-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="55138-314">路由處理是依照慣例來建立：</span><span class="sxs-lookup"><span data-stu-id="55138-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="55138-315">路由會依照連續處理 (-1、0、1、2、 &hellip; n) 。</span><span class="sxs-lookup"><span data-stu-id="55138-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="55138-316">當路由相同時 `Order` ，會先比對最特定的路由，再接著較不特定的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="55138-317">當具有相同 `Order` 和相同數目之參數的路由符合要求 URL 時，會依其新增至的順序來處理路由 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 。</span><span class="sxs-lookup"><span data-stu-id="55138-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="55138-318">可能的話，請避免根據已建立的路由處理順序。</span><span class="sxs-lookup"><span data-stu-id="55138-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="55138-319">一般而言，路由會選取 URL 相符的正確路由。</span><span class="sxs-lookup"><span data-stu-id="55138-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="55138-320">如果您必須設定路由 `Order` 屬性以正確地路由傳送要求，則應用程式的路由配置可能會讓用戶端和脆弱的維護產生混淆。</span><span class="sxs-lookup"><span data-stu-id="55138-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="55138-321">搜尋以簡化應用程式的路由配置。</span><span class="sxs-lookup"><span data-stu-id="55138-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="55138-322">範例應用程式需要明確的路由處理順序，以使用單一應用程式來示範數個路由案例。</span><span class="sxs-lookup"><span data-stu-id="55138-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="55138-323">不過，您應該嘗試避免在 `Order` 生產應用程式中設定路由的做法。</span><span class="sxs-lookup"><span data-stu-id="55138-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="55138-324">Razor 頁面路由和 MVC 控制器路由會共用執行。</span><span class="sxs-lookup"><span data-stu-id="55138-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="55138-325">您可以在 [路由傳送至控制器動作：排序屬性路由](xref:mvc/controllers/routing#ordering-attribute-routes)時，取得 MVC 主題中路由順序的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="55138-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="55138-326">模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-326">Model conventions</span></span>

<span data-ttu-id="55138-327">加入的委派， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 以新增適用于頁面的 [模型慣例](xref:mvc/controllers/application-model#conventions) Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="55138-328">將路由模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="55138-329">您 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 可以使用來建立，並將加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面路由模型結構中套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="55138-330">範例應用程式將 `{globalTemplate?}` 路由範本新增至應用程式中的所有頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-331"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `1`。</span><span class="sxs-lookup"><span data-stu-id="55138-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="55138-332">這可確保範例應用程式中的下列路由符合行為：</span><span class="sxs-lookup"><span data-stu-id="55138-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="55138-333">的路由範本 `TheContactPage/{text?}` 稍後會在主題中新增。</span><span class="sxs-lookup"><span data-stu-id="55138-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="55138-334">連絡人頁面路由的預設順序是 `null` (`Order = 0`) ，因此它會符合 `{globalTemplate?}` 路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="55138-335">`{aboutTemplate?}`稍後會在主題中新增路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="55138-336">`{aboutTemplate?}` 範本會指定 `Order` 為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="55138-337">在 `/About/RouteDataValue` 上要求 About 頁面時，由於設定 `Order` 屬性之故，因此 "RouteDataValue" 會載入至 `RouteData.Values["globalTemplate"]` (`Order = 1`)，而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`)。</span><span class="sxs-lookup"><span data-stu-id="55138-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="55138-338">`{otherPagesTemplate?}`稍後會在主題中新增路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="55138-339">`{otherPagesTemplate?}` 範本會指定 `Order` 為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="55138-340">使用路由參數要求 *Pages/OtherPages* 資料夾中的任何頁面時 (例如， `/OtherPages/Page1/RouteDataValue`) ，"因此 routedatavalue" 會載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不是 `RouteData.Values["otherPagesTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-341">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-342">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-343">Razor<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>當 MVC 新增至中的服務集合時，會加入頁面選項，例如加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="55138-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="55138-344">如需範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="55138-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="55138-345">在 `localhost:5000/About/GlobalRouteValue` 上要求範例的 About 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用路由區段 GlobalRouteValue 要求 About 頁面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="55138-348">將應用程式模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="55138-349">用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 來建立和新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面應用程式模型結構期間套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="55138-350">為了示範這個慣例和本主題稍後的其他慣例，範例應用程式包含了 `AddHeaderAttribute` 類別。</span><span class="sxs-lookup"><span data-stu-id="55138-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="55138-351">類別建構函式接受 `name` 字串和 `values` 字串陣列。</span><span class="sxs-lookup"><span data-stu-id="55138-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="55138-352">在其 `OnResultExecuting` 方法中使用這些值來設定回應標頭。</span><span class="sxs-lookup"><span data-stu-id="55138-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="55138-353">完整的類別顯示在本主題稍後的[頁面模型動作慣例](#page-model-action-conventions)一節中。</span><span class="sxs-lookup"><span data-stu-id="55138-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="55138-354">範例應用程式會使用 `AddHeaderAttribute` 類別，將標頭 `GlobalHeader` 新增至應用程式中的所有頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-355">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="55138-356">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="55138-358">將處理常式模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="55138-359">您 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 可以使用來建立，並將加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面處理常式模型結構中套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-360">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="55138-361">頁面路由動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-361">Page route action conventions</span></span>

<span data-ttu-id="55138-362">衍生自的預設路由模型提供者， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 其設計目的是為了提供設定頁面路由的擴充點。</span><span class="sxs-lookup"><span data-stu-id="55138-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="55138-363">資料夾路由模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-363">Folder route model convention</span></span>

<span data-ttu-id="55138-364">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 針對指定資料夾下的所有頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="55138-365">範例應用程式會使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>，將 `{otherPagesTemplate?}` 路由範本新增至 *OtherPages* 資料夾中的頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="55138-366"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="55138-367">這可確保 `{globalTemplate?}` `1` 當提供單一路由值時，會針對第一個路由資料值的位置，指定在主題中稍早設定) 的 (範本。</span><span class="sxs-lookup"><span data-stu-id="55138-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="55138-368">如果以路由參數值要求 *Pages/OtherPages* 資料夾中的頁面 (例如， `/OtherPages/Page1/RouteDataValue`) ，就會將 "因此 routedatavalue" 載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不是 `RouteData.Values["otherPagesTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-369">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-370">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-371">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 上要求範例的 Page1 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![以 GlobalRouteValue 和 OtherPagesRouteValue 的路由區段要求 OtherPages 資料夾中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="55138-374">頁面路由模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-374">Page route model convention</span></span>

<span data-ttu-id="55138-375">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 針對具有指定名稱的頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="55138-376">範例應用程式會使用 `AddPageRouteModelConvention`，將 `{aboutTemplate?}` 路由範本新增至 About 頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="55138-377"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="55138-378">這可確保 `{globalTemplate?}` `1` 當提供單一路由值時，會針對第一個路由資料值的位置，指定在主題中稍早設定) 的 (範本。</span><span class="sxs-lookup"><span data-stu-id="55138-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="55138-379">如果以路由參數值（位於）要求 [關於 `/About/RouteDataValue` ] 頁面，則會將 "因此 routedatavalue" 載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不會 `RouteData.Values["aboutTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-380">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-381">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-382">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 上要求範例的 About 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![以 GlobalRouteValue 和 AboutRouteValue 的路由區段要求 About 頁面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="55138-385">使用參數轉換器自訂頁面路由</span><span class="sxs-lookup"><span data-stu-id="55138-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="55138-386">您可以使用參數轉換程式自訂 ASP.NET Core 所產生的頁面路由。</span><span class="sxs-lookup"><span data-stu-id="55138-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="55138-387">參數轉換程式會實作 `IOutboundParameterTransformer` 並轉換參數值。</span><span class="sxs-lookup"><span data-stu-id="55138-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="55138-388">例如，自訂 `SlugifyParameterTransformer` 參數轉換器會將 `SubscriptionManagement` 路由值變更為 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="55138-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="55138-389">`PageRouteTransformerConvention`頁面路由模型慣例會將參數轉換器套用至應用程式中自動產生之頁面路由的資料夾和檔案名區段。</span><span class="sxs-lookup"><span data-stu-id="55138-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="55138-390">例如， Razor 在 */Pages/SubscriptionManagement/ViewAll.cshtml* 的頁面檔中，會將其路由從重寫 `/SubscriptionManagement/ViewAll` 到 `/subscription-management/view-all` 。</span><span class="sxs-lookup"><span data-stu-id="55138-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="55138-391">`PageRouteTransformerConvention` 只會轉換來自 Razor Pages 資料夾和檔案名的頁面路由自動產生區段。</span><span class="sxs-lookup"><span data-stu-id="55138-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="55138-392">它不會轉換使用指示詞加入的路由區段 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="55138-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="55138-393">慣例也不會轉換所加入的路由 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 。</span><span class="sxs-lookup"><span data-stu-id="55138-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="55138-394">`PageRouteTransformerConvention`註冊為中的選項 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="55138-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a><span data-ttu-id="55138-395">設定頁面路由</span><span class="sxs-lookup"><span data-stu-id="55138-395">Configure a page route</span></span>

<span data-ttu-id="55138-396">您 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 可以使用，在指定的頁面路徑上設定頁面的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="55138-397">頁面的所產生連結會使用您指定的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="55138-398">`AddPageRoute` 使用 `AddPageRouteModelConvention` 來建立路由。</span><span class="sxs-lookup"><span data-stu-id="55138-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="55138-399">範例應用程式為 *Contact.cshtml* 建立 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="55138-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="55138-400">Contact 頁面也可以透過其預設路由在 `/Contact` 上連線。</span><span class="sxs-lookup"><span data-stu-id="55138-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="55138-401">範例應用程式的 Contact 頁面自訂路由允許使用選擇性的 `text` 路由區段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="55138-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="55138-402">該頁面也會在其 `@page` 指示詞中包含這個選擇性區段，以防訪客在其 `/Contact` 路由上存取頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="55138-403">請注意，呈現頁面中針對 **Contact** 連結所產生的 URL 會反映更新的路由：</span><span class="sxs-lookup"><span data-stu-id="55138-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![導覽列中的範例應用程式 Contact 連結](razor-pages-conventions/_static/contact-link.png)

![檢查所呈現 HTML 中的 Contact 連結會指出 href 設定為 '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="55138-406">請在其一般路由 `/Contact` 或自訂路由 `/TheContactPage` 上瀏覽 Contact 頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="55138-407">如果您提供額外的 `text` 路由區段，該頁面會顯示您提供的 HTML 編碼區段：</span><span class="sxs-lookup"><span data-stu-id="55138-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供 'TextValue' 的選擇性 'text' 路由區段的 Edge 瀏覽器範例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="55138-410">頁面模型動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-410">Page model action conventions</span></span>

<span data-ttu-id="55138-411">實值的預設頁面模型提供者， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 其設計目的是要提供設定頁面模型的擴充點。</span><span class="sxs-lookup"><span data-stu-id="55138-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="55138-412">在建置和修改頁面探索與處理案例時，這些慣例很有用。</span><span class="sxs-lookup"><span data-stu-id="55138-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="55138-413">針對本節中的範例，範例應用程式 `AddHeaderAttribute` 會使用類別，也就是 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> 套用回應標頭的類別：</span><span class="sxs-lookup"><span data-stu-id="55138-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="55138-414">透過慣例，此範例示範如何將屬性套用至資料夾中的所有頁面，以及套用至單一頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="55138-415">**資料夾應用程式模型慣例**</span><span class="sxs-lookup"><span data-stu-id="55138-415">**Folder app model convention**</span></span>

<span data-ttu-id="55138-416">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可以使用來建立並加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 針對指定資料夾下的所有頁面，在實例上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="55138-417">此範例示範如何將標頭 `OtherPagesHeader` 新增至應用程式的 *OtherPages* 資料夾內的頁面來使用 `AddFolderApplicationModelConvention`：</span><span class="sxs-lookup"><span data-stu-id="55138-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="55138-418">在 `localhost:5000/OtherPages/Page1` 上要求範例的 Page1 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 頁面的回應標頭會顯示已新增 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="55138-420">**頁面應用程式模型慣例**</span><span class="sxs-lookup"><span data-stu-id="55138-420">**Page app model convention**</span></span>

<span data-ttu-id="55138-421">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 針對具有指定名稱的頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="55138-422">此範例示範如何將標頭 `AboutHeader` 新增至 About 頁面來使用 `AddPageApplicationModelConvention`：</span><span class="sxs-lookup"><span data-stu-id="55138-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="55138-423">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="55138-425">**設定篩選條件**</span><span class="sxs-lookup"><span data-stu-id="55138-425">**Configure a filter**</span></span>

<span data-ttu-id="55138-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 設定要套用的指定篩選。</span><span class="sxs-lookup"><span data-stu-id="55138-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="55138-427">您可以實作篩選條件類別，但範例應用程式是示範如何在 Lambda 運算式中實作篩選條件，該運算式會在幕後實作為 Factory 以傳回篩選條件：</span><span class="sxs-lookup"><span data-stu-id="55138-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="55138-428">使用頁面應用程式模型，可針對指向 *OtherPages* 資料夾內 Page2 頁面的區段檢查相對路徑。</span><span class="sxs-lookup"><span data-stu-id="55138-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="55138-429">如果通過條件，則會新增標頭。</span><span class="sxs-lookup"><span data-stu-id="55138-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="55138-430">如果沒有，則會套用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="55138-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="55138-431">`EmptyFilter` 是[動作篩選條件](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="55138-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="55138-432">由於頁面會忽略動作篩選準則 Razor ，因此 `EmptyFilter` 如果路徑未包含，就不會有任何作用 `OtherPages/Page2` 。</span><span class="sxs-lookup"><span data-stu-id="55138-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="55138-433">在 `localhost:5000/OtherPages/Page2` 上要求範例的 Page2 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 會新增至 Page2 的回應。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="55138-435">**設定篩選條件 Factory**</span><span class="sxs-lookup"><span data-stu-id="55138-435">**Configure a filter factory**</span></span>

<span data-ttu-id="55138-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 設定指定的 factory 將 [篩選](xref:mvc/controllers/filters) 套用至所有 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="55138-437">範例應用程式示範如何以應用程式頁面的兩個值新增標頭 `FilterFactoryHeader` 來使用[篩選條件 Factory](xref:mvc/controllers/filters#ifilterfactory)：</span><span class="sxs-lookup"><span data-stu-id="55138-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="55138-438">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="55138-439">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增兩個 FilterFactoryHeader 標頭。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="55138-441">MVC 篩選條件和頁面篩選條件 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="55138-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="55138-442">頁面會忽略 MVC [動作篩選](xref:mvc/controllers/filters#action-filters) Razor ，因為 Razor 頁面會使用處理程式方法。</span><span class="sxs-lookup"><span data-stu-id="55138-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="55138-443">其他類型的 MVC 篩選條件可供您使用：[Authorization](xref:mvc/controllers/filters#authorization-filters)、[Exception](xref:mvc/controllers/filters#exception-filters)、[Resource](xref:mvc/controllers/filters#resource-filters) 和 [Result](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="55138-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="55138-444">如需詳細資訊，請參閱[篩選條件](xref:mvc/controllers/filters)主題。</span><span class="sxs-lookup"><span data-stu-id="55138-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="55138-445">頁面篩選 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是適用于頁面的篩選準則 Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="55138-446">如需詳細資訊，請參閱 [ Razor 頁面的篩選方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="55138-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="55138-447">其他資源</span><span class="sxs-lookup"><span data-stu-id="55138-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="55138-448">瞭解如何使用頁面 [路由和應用程式模型提供者慣例](xref:mvc/controllers/application-model#conventions) ，來控制頁面應用程式中的頁面路由、探索和處理 Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="55138-449">當您需要設定個別頁面的自訂頁面路由時，請使用本主題稍後所述的 [AddPageRoute 慣例](#configure-a-page-route)來設定路由至頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="55138-450">若要指定頁面路由、加入路由區段或將參數加入至路由，請使用頁面的指示詞 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="55138-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="55138-451">如需詳細資訊，請參閱 [自訂路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="55138-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="55138-452">有保留字無法用作路由區段或參數名稱。</span><span class="sxs-lookup"><span data-stu-id="55138-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="55138-453">如需詳細資訊，請參閱 [路由：保留的路由名稱](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="55138-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="55138-454">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="55138-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="55138-455">案例</span><span class="sxs-lookup"><span data-stu-id="55138-455">Scenario</span></span> | <span data-ttu-id="55138-456">範例會示範 ...</span><span class="sxs-lookup"><span data-stu-id="55138-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="55138-457">模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="55138-458">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="55138-458">Conventions.Add</span></span><ul><li><span data-ttu-id="55138-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="55138-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="55138-462">將路由範本和標頭新增至應用程式的頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="55138-463">頁面路由動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="55138-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="55138-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="55138-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="55138-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="55138-467">將路由範本新增至資料夾中的頁面，以及新增至單一頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="55138-468">頁面模型動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="55138-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="55138-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="55138-471">ConfigureFilter (篩選類別、Lambda 運算式或篩選 Factory)</span><span class="sxs-lookup"><span data-stu-id="55138-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="55138-472">將標頭新增至資料夾中的頁面、將標頭新增至單一頁面，以及設定[篩選條件 Factory](xref:mvc/controllers/filters#ifilterfactory) 將標頭新增至應用程式的頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="55138-473">Razor 頁面慣例會在 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 類別中的服務集合上使用擴充方法來新增和設定 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="55138-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="55138-474">稍後在本主題會說明下列慣例範例：</span><span class="sxs-lookup"><span data-stu-id="55138-474">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="55138-475">路由順序</span><span class="sxs-lookup"><span data-stu-id="55138-475">Route order</span></span>

<span data-ttu-id="55138-476">路由會指定 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 處理 (路由符合) 的。</span><span class="sxs-lookup"><span data-stu-id="55138-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="55138-477">單</span><span class="sxs-lookup"><span data-stu-id="55138-477">Order</span></span>            | <span data-ttu-id="55138-478">行為</span><span class="sxs-lookup"><span data-stu-id="55138-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="55138-479">-1</span><span class="sxs-lookup"><span data-stu-id="55138-479">-1</span></span>               | <span data-ttu-id="55138-480">路由會在處理其他路由之前處理。</span><span class="sxs-lookup"><span data-stu-id="55138-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="55138-481">0</span><span class="sxs-lookup"><span data-stu-id="55138-481">0</span></span>                | <span data-ttu-id="55138-482"> (預設值) 不會指定順序。</span><span class="sxs-lookup"><span data-stu-id="55138-482">Order isn't specified (default value).</span></span> <span data-ttu-id="55138-483">若未指派 `Order` (`Order = null`) 會將路由預設為 `Order` 0 (零) 以進行處理。</span><span class="sxs-lookup"><span data-stu-id="55138-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="55138-484">1、2、 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="55138-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="55138-485">指定路由處理順序。</span><span class="sxs-lookup"><span data-stu-id="55138-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="55138-486">路由處理是依照慣例來建立：</span><span class="sxs-lookup"><span data-stu-id="55138-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="55138-487">路由會依照連續處理 (-1、0、1、2、 &hellip; n) 。</span><span class="sxs-lookup"><span data-stu-id="55138-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="55138-488">當路由相同時 `Order` ，會先比對最特定的路由，再接著較不特定的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="55138-489">當具有相同 `Order` 和相同數目之參數的路由符合要求 URL 時，會依其新增至的順序來處理路由 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 。</span><span class="sxs-lookup"><span data-stu-id="55138-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="55138-490">可能的話，請避免根據已建立的路由處理順序。</span><span class="sxs-lookup"><span data-stu-id="55138-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="55138-491">一般而言，路由會選取 URL 相符的正確路由。</span><span class="sxs-lookup"><span data-stu-id="55138-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="55138-492">如果您必須設定路由 `Order` 屬性以正確地路由傳送要求，則應用程式的路由配置可能會讓用戶端和脆弱的維護產生混淆。</span><span class="sxs-lookup"><span data-stu-id="55138-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="55138-493">搜尋以簡化應用程式的路由配置。</span><span class="sxs-lookup"><span data-stu-id="55138-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="55138-494">範例應用程式需要明確的路由處理順序，以使用單一應用程式來示範數個路由案例。</span><span class="sxs-lookup"><span data-stu-id="55138-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="55138-495">不過，您應該嘗試避免在 `Order` 生產應用程式中設定路由的做法。</span><span class="sxs-lookup"><span data-stu-id="55138-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="55138-496">Razor 頁面路由和 MVC 控制器路由會共用執行。</span><span class="sxs-lookup"><span data-stu-id="55138-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="55138-497">您可以在 [路由傳送至控制器動作：排序屬性路由](xref:mvc/controllers/routing#ordering-attribute-routes)時，取得 MVC 主題中路由順序的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="55138-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="55138-498">模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-498">Model conventions</span></span>

<span data-ttu-id="55138-499">加入的委派， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 以新增適用于頁面的 [模型慣例](xref:mvc/controllers/application-model#conventions) Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="55138-500">將路由模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="55138-501">您 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 可以使用來建立，並將加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面路由模型結構中套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="55138-502">範例應用程式將 `{globalTemplate?}` 路由範本新增至應用程式中的所有頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-503"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `1`。</span><span class="sxs-lookup"><span data-stu-id="55138-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="55138-504">這可確保範例應用程式中的下列路由符合行為：</span><span class="sxs-lookup"><span data-stu-id="55138-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="55138-505">的路由範本 `TheContactPage/{text?}` 稍後會在主題中新增。</span><span class="sxs-lookup"><span data-stu-id="55138-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="55138-506">連絡人頁面路由的預設順序是 `null` (`Order = 0`) ，因此它會符合 `{globalTemplate?}` 路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="55138-507">`{aboutTemplate?}`稍後會在主題中新增路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="55138-508">`{aboutTemplate?}` 範本會指定 `Order` 為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="55138-509">在 `/About/RouteDataValue` 上要求 About 頁面時，由於設定 `Order` 屬性之故，因此 "RouteDataValue" 會載入至 `RouteData.Values["globalTemplate"]` (`Order = 1`)，而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`)。</span><span class="sxs-lookup"><span data-stu-id="55138-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="55138-510">`{otherPagesTemplate?}`稍後會在主題中新增路由範本。</span><span class="sxs-lookup"><span data-stu-id="55138-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="55138-511">`{otherPagesTemplate?}` 範本會指定 `Order` 為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="55138-512">使用路由參數要求 *Pages/OtherPages* 資料夾中的任何頁面時 (例如， `/OtherPages/Page1/RouteDataValue`) ，"因此 routedatavalue" 會載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不是 `RouteData.Values["otherPagesTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-513">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-514">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-515">Razor<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>當 MVC 新增至中的服務集合時，會加入頁面選項，例如加入 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="55138-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="55138-516">如需範例，請參閱[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="55138-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="55138-517">在 `localhost:5000/About/GlobalRouteValue` 上要求範例的 About 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用路由區段 GlobalRouteValue 要求 About 頁面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="55138-520">將應用程式模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="55138-521">用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 來建立和新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面應用程式模型結構期間套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="55138-522">為了示範這個慣例和本主題稍後的其他慣例，範例應用程式包含了 `AddHeaderAttribute` 類別。</span><span class="sxs-lookup"><span data-stu-id="55138-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="55138-523">類別建構函式接受 `name` 字串和 `values` 字串陣列。</span><span class="sxs-lookup"><span data-stu-id="55138-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="55138-524">在其 `OnResultExecuting` 方法中使用這些值來設定回應標頭。</span><span class="sxs-lookup"><span data-stu-id="55138-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="55138-525">完整的類別顯示在本主題稍後的[頁面模型動作慣例](#page-model-action-conventions)一節中。</span><span class="sxs-lookup"><span data-stu-id="55138-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="55138-526">範例應用程式會使用 `AddHeaderAttribute` 類別，將標頭 `GlobalHeader` 新增至應用程式中的所有頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-527">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="55138-528">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="55138-530">將處理常式模型慣例新增至所有頁面</span><span class="sxs-lookup"><span data-stu-id="55138-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="55138-531">您 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 可以使用來建立，並將加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 至在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 頁面處理常式模型結構中套用的實例集合。</span><span class="sxs-lookup"><span data-stu-id="55138-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="55138-532">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="55138-533">頁面路由動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-533">Page route action conventions</span></span>

<span data-ttu-id="55138-534">衍生自的預設路由模型提供者， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 其設計目的是為了提供設定頁面路由的擴充點。</span><span class="sxs-lookup"><span data-stu-id="55138-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="55138-535">資料夾路由模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-535">Folder route model convention</span></span>

<span data-ttu-id="55138-536">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 針對指定資料夾下的所有頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="55138-537">範例應用程式會使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>，將 `{otherPagesTemplate?}` 路由範本新增至 *OtherPages* 資料夾中的頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="55138-538"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="55138-539">這可確保 `{globalTemplate?}` `1` 當提供單一路由值時，會針對第一個路由資料值的位置，指定在主題中稍早設定) 的 (範本。</span><span class="sxs-lookup"><span data-stu-id="55138-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="55138-540">如果以路由參數值要求 *Pages/OtherPages* 資料夾中的頁面 (例如， `/OtherPages/Page1/RouteDataValue`) ，就會將 "因此 routedatavalue" 載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不是 `RouteData.Values["otherPagesTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-541">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-542">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-543">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 上要求範例的 Page1 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![以 GlobalRouteValue 和 OtherPagesRouteValue 的路由區段要求 OtherPages 資料夾中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="55138-546">頁面路由模型慣例</span><span class="sxs-lookup"><span data-stu-id="55138-546">Page route model convention</span></span>

<span data-ttu-id="55138-547">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 針對具有指定名稱的頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="55138-548">範例應用程式會使用 `AddPageRouteModelConvention`，將 `{aboutTemplate?}` 路由範本新增至 About 頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="55138-549"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 屬性設定為 `2`。</span><span class="sxs-lookup"><span data-stu-id="55138-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="55138-550">這可確保 `{globalTemplate?}` `1` 當提供單一路由值時，會針對第一個路由資料值的位置，指定在主題中稍早設定) 的 (範本。</span><span class="sxs-lookup"><span data-stu-id="55138-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="55138-551">如果以路由參數值（位於）要求 [關於 `/About/RouteDataValue` ] 頁面，則會將 "因此 routedatavalue" 載入 `RouteData.Values["globalTemplate"]` (`Order = 1`) ，而不會 `RouteData.Values["aboutTemplate"]` () ， `Order = 2` 因為設定 `Order` 屬性。</span><span class="sxs-lookup"><span data-stu-id="55138-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="55138-552">可能的話，請不要設定 `Order` ，這會導致 `Order = 0` 。</span><span class="sxs-lookup"><span data-stu-id="55138-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="55138-553">依賴路由選取正確的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="55138-554">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 上要求範例的 About 頁面，並檢查結果：</span><span class="sxs-lookup"><span data-stu-id="55138-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![以 GlobalRouteValue 和 AboutRouteValue 的路由區段要求 About 頁面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="55138-557">設定頁面路由</span><span class="sxs-lookup"><span data-stu-id="55138-557">Configure a page route</span></span>

<span data-ttu-id="55138-558">您 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 可以使用，在指定的頁面路徑上設定頁面的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="55138-559">頁面的所產生連結會使用您指定的路由。</span><span class="sxs-lookup"><span data-stu-id="55138-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="55138-560">`AddPageRoute` 使用 `AddPageRouteModelConvention` 來建立路由。</span><span class="sxs-lookup"><span data-stu-id="55138-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="55138-561">範例應用程式為 *Contact.cshtml* 建立 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="55138-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="55138-562">Contact 頁面也可以透過其預設路由在 `/Contact` 上連線。</span><span class="sxs-lookup"><span data-stu-id="55138-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="55138-563">範例應用程式的 Contact 頁面自訂路由允許使用選擇性的 `text` 路由區段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="55138-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="55138-564">該頁面也會在其 `@page` 指示詞中包含這個選擇性區段，以防訪客在其 `/Contact` 路由上存取頁面：</span><span class="sxs-lookup"><span data-stu-id="55138-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="55138-565">請注意，呈現頁面中針對 **Contact** 連結所產生的 URL 會反映更新的路由：</span><span class="sxs-lookup"><span data-stu-id="55138-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![導覽列中的範例應用程式 Contact 連結](razor-pages-conventions/_static/contact-link.png)

![檢查所呈現 HTML 中的 Contact 連結會指出 href 設定為 '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="55138-568">請在其一般路由 `/Contact` 或自訂路由 `/TheContactPage` 上瀏覽 Contact 頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="55138-569">如果您提供額外的 `text` 路由區段，該頁面會顯示您提供的 HTML 編碼區段：</span><span class="sxs-lookup"><span data-stu-id="55138-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供 'TextValue' 的選擇性 'text' 路由區段的 Edge 瀏覽器範例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="55138-572">頁面模型動作慣例</span><span class="sxs-lookup"><span data-stu-id="55138-572">Page model action conventions</span></span>

<span data-ttu-id="55138-573">實值的預設頁面模型提供者， <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 其設計目的是要提供設定頁面模型的擴充點。</span><span class="sxs-lookup"><span data-stu-id="55138-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="55138-574">在建置和修改頁面探索與處理案例時，這些慣例很有用。</span><span class="sxs-lookup"><span data-stu-id="55138-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="55138-575">針對本節中的範例，範例應用程式 `AddHeaderAttribute` 會使用類別，也就是 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> 套用回應標頭的類別：</span><span class="sxs-lookup"><span data-stu-id="55138-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="55138-576">透過慣例，此範例示範如何將屬性套用至資料夾中的所有頁面，以及套用至單一頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="55138-577">**資料夾應用程式模型慣例**</span><span class="sxs-lookup"><span data-stu-id="55138-577">**Folder app model convention**</span></span>

<span data-ttu-id="55138-578">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可以使用來建立並加入 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 針對指定資料夾下的所有頁面，在實例上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="55138-579">此範例示範如何將標頭 `OtherPagesHeader` 新增至應用程式的 *OtherPages* 資料夾內的頁面來使用 `AddFolderApplicationModelConvention`：</span><span class="sxs-lookup"><span data-stu-id="55138-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="55138-580">在 `localhost:5000/OtherPages/Page1` 上要求範例的 Page1 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 頁面的回應標頭會顯示已新增 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="55138-582">**頁面應用程式模型慣例**</span><span class="sxs-lookup"><span data-stu-id="55138-582">**Page app model convention**</span></span>

<span data-ttu-id="55138-583">您 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可以使用來建立並新增 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> ，它會 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 針對具有指定名稱的頁面，在上叫用動作。</span><span class="sxs-lookup"><span data-stu-id="55138-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="55138-584">此範例示範如何將標頭 `AboutHeader` 新增至 About 頁面來使用 `AddPageApplicationModelConvention`：</span><span class="sxs-lookup"><span data-stu-id="55138-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="55138-585">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="55138-587">**設定篩選條件**</span><span class="sxs-lookup"><span data-stu-id="55138-587">**Configure a filter**</span></span>

<span data-ttu-id="55138-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 設定要套用的指定篩選。</span><span class="sxs-lookup"><span data-stu-id="55138-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="55138-589">您可以實作篩選條件類別，但範例應用程式是示範如何在 Lambda 運算式中實作篩選條件，該運算式會在幕後實作為 Factory 以傳回篩選條件：</span><span class="sxs-lookup"><span data-stu-id="55138-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="55138-590">使用頁面應用程式模型，可針對指向 *OtherPages* 資料夾內 Page2 頁面的區段檢查相對路徑。</span><span class="sxs-lookup"><span data-stu-id="55138-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="55138-591">如果通過條件，則會新增標頭。</span><span class="sxs-lookup"><span data-stu-id="55138-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="55138-592">如果沒有，則會套用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="55138-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="55138-593">`EmptyFilter` 是[動作篩選條件](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="55138-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="55138-594">由於頁面會忽略動作篩選準則 Razor ，因此 `EmptyFilter` 如果路徑未包含，就不會有任何作用 `OtherPages/Page2` 。</span><span class="sxs-lookup"><span data-stu-id="55138-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="55138-595">在 `localhost:5000/OtherPages/Page2` 上要求範例的 Page2 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 會新增至 Page2 的回應。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="55138-597">**設定篩選條件 Factory**</span><span class="sxs-lookup"><span data-stu-id="55138-597">**Configure a filter factory**</span></span>

<span data-ttu-id="55138-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 設定指定的 factory 將 [篩選](xref:mvc/controllers/filters) 套用至所有 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="55138-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="55138-599">範例應用程式示範如何以應用程式頁面的兩個值新增標頭 `FilterFactoryHeader` 來使用[篩選條件 Factory](xref:mvc/controllers/filters#ifilterfactory)：</span><span class="sxs-lookup"><span data-stu-id="55138-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="55138-600">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="55138-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="55138-601">在 `localhost:5000/About` 上要求範例的 About 頁面，並檢查標頭來檢視結果：</span><span class="sxs-lookup"><span data-stu-id="55138-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![About 頁面的回應標頭會顯示已新增兩個 FilterFactoryHeader 標頭。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="55138-603">MVC 篩選條件和頁面篩選條件 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="55138-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="55138-604">頁面會忽略 MVC [動作篩選](xref:mvc/controllers/filters#action-filters) Razor ，因為 Razor 頁面會使用處理程式方法。</span><span class="sxs-lookup"><span data-stu-id="55138-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="55138-605">其他類型的 MVC 篩選條件可供您使用：[Authorization](xref:mvc/controllers/filters#authorization-filters)、[Exception](xref:mvc/controllers/filters#exception-filters)、[Resource](xref:mvc/controllers/filters#resource-filters) 和 [Result](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="55138-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="55138-606">如需詳細資訊，請參閱[篩選條件](xref:mvc/controllers/filters)主題。</span><span class="sxs-lookup"><span data-stu-id="55138-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="55138-607">頁面篩選 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是適用于頁面的篩選準則 Razor 。</span><span class="sxs-lookup"><span data-stu-id="55138-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="55138-608">如需詳細資訊，請參閱 [ Razor 頁面的篩選方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="55138-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="55138-609">其他資源</span><span class="sxs-lookup"><span data-stu-id="55138-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
