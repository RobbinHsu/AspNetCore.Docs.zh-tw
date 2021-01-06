---
title: 第3部分：將視圖新增至 ASP.NET Core MVC 應用程式
author: rick-anderson
description: ASP.NET Core MVC 之教學課程系列的第3部分。
ms.author: riande
ms.date: 11/16/2019
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
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: ce2754d22b61578bdbe5e78a69e08bdc1e83190d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688344"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="91cb5-103">第3部分：將視圖新增至 ASP.NET Core MVC 應用程式</span><span class="sxs-lookup"><span data-stu-id="91cb5-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="91cb5-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91cb5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91cb5-105">在本節中，您會將 `HelloWorldController` 類別修改為使用 [Razor](xref:mvc/views/razor) view files，以將產生 HTML 回應的程式明確封裝到用戶端。</span><span class="sxs-lookup"><span data-stu-id="91cb5-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="91cb5-106">您可以使用建立視圖範本檔案 Razor 。</span><span class="sxs-lookup"><span data-stu-id="91cb5-106">You create a view template file using Razor.</span></span> <span data-ttu-id="91cb5-107">Razor型視圖範本的副檔名為 *cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="91cb5-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="91cb5-108">它們提供了一種使用 C# 建立 HTML 輸出的簡潔方式。</span><span class="sxs-lookup"><span data-stu-id="91cb5-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="91cb5-109">`Index` 方法目前會傳回字串，內含在控制器類別中已直接書寫好的固定訊息。</span><span class="sxs-lookup"><span data-stu-id="91cb5-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="91cb5-110">在 `HelloWorldController` 類別中，以下列程式碼取代 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="91cb5-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="91cb5-111">上述程式碼會呼叫控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。</span><span class="sxs-lookup"><span data-stu-id="91cb5-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="91cb5-112">它使用檢視範本來產生 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="91cb5-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="91cb5-113">上述 `Index` 方法等控制器方法 (也稱為「動作方法」) 通常會傳回 <xref:Microsoft.AspNetCore.Mvc.IActionResult> (或衍生自 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 的類別)，而不會傳回像是 `string` 的類型。</span><span class="sxs-lookup"><span data-stu-id="91cb5-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="91cb5-114">新增檢視</span><span class="sxs-lookup"><span data-stu-id="91cb5-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91cb5-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91cb5-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91cb5-116">依序以滑鼠右鍵按一下 *Views* 資料夾、[新增] > [新增資料夾]，然後將資料夾命名為 *HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="91cb5-117">依序以滑鼠右鍵按一下 *Views/HelloWorld* 資料夾、[新增] > [新增項目]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="91cb5-118">在 [新增項目 - MvcMovie] 對話方塊內</span><span class="sxs-lookup"><span data-stu-id="91cb5-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="91cb5-119">在右上角的搜尋方塊中，輸入 *view*</span><span class="sxs-lookup"><span data-stu-id="91cb5-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="91cb5-120">選取 **Razor 視圖**</span><span class="sxs-lookup"><span data-stu-id="91cb5-120">Select **Razor View**</span></span>

  * <span data-ttu-id="91cb5-121">保留 [名稱] 方塊值 *Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="91cb5-122">選取 [新增]</span><span class="sxs-lookup"><span data-stu-id="91cb5-122">Select **Add**</span></span>

![[新增項目] 對話方塊](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91cb5-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91cb5-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91cb5-125">為 `HelloWorldController` 新增 `Index` 檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="91cb5-126">新增資料夾，並命名為 *Views/HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="91cb5-127">將檔案新增至 *Views/HelloWorld* 資料夾，並命名為 *Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91cb5-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="91cb5-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91cb5-129">依序以滑鼠右鍵按一下 *Views* 資料夾、[新增] > [新增資料夾]，然後將資料夾命名為 *HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="91cb5-130">依序以滑鼠右鍵按一下 *Views/HelloWorld* 資料夾、[新增] > [新增檔案]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="91cb5-131">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="91cb5-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="91cb5-132">選取左窗格中的 [ **ASP .Net Core** ]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="91cb5-133">選取中央窗格中的 [ **Razor View** ]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-133">Select **Razor View** in the center pane.</span></span>
  * <span data-ttu-id="91cb5-134">在 [**名稱**] 方塊中輸入 *索引*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="91cb5-135">選取 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-135">Select **New**.</span></span>

![[新增項目] 對話方塊](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="91cb5-137">以下列內容取代 *Views/HelloWorld/Index. cshtml* view 檔案的內容 Razor ：</span><span class="sxs-lookup"><span data-stu-id="91cb5-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="91cb5-138">瀏覽至 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="91cb5-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="91cb5-139">`HelloWorldController` 中的 `Index` 方法不會執行什麼作業；它會執行陳述式 `return View();`，其指定方法應使用檢視範本檔案來呈現瀏覽器的回應。</span><span class="sxs-lookup"><span data-stu-id="91cb5-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="91cb5-140">因為未指定檢視範本檔案名稱，因此 MVC 預設為使用預設檢視檔案。</span><span class="sxs-lookup"><span data-stu-id="91cb5-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="91cb5-141">預設視圖檔的名稱與 () 的方法相同 `Index` ，因此會使用 */Views/HelloWorld/Index.cshtml* 中的 view 範本。</span><span class="sxs-lookup"><span data-stu-id="91cb5-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="91cb5-142">下列影像顯示檢視中硬式編碼的字串 "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="91cb5-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="91cb5-143">。</span><span class="sxs-lookup"><span data-stu-id="91cb5-143">hard-coded in the view.</span></span>

![瀏覽器視窗](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="91cb5-145">變更檢視和版面配置頁</span><span class="sxs-lookup"><span data-stu-id="91cb5-145">Change views and layout pages</span></span>

<span data-ttu-id="91cb5-146">選取功能表連結 (**MvcMovie**、**Home** 和 **Privacy**)。</span><span class="sxs-lookup"><span data-stu-id="91cb5-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="91cb5-147">每個頁面會顯示相同的功能表配置。</span><span class="sxs-lookup"><span data-stu-id="91cb5-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="91cb5-148">功能表配置是在 *Views/Shared/_Layout.cshtml* 檔案中實作。</span><span class="sxs-lookup"><span data-stu-id="91cb5-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="91cb5-149">開啟 *Views/Shared/_Layout.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="91cb5-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="91cb5-150">[版面配置](xref:mvc/views/layout)範本可讓您在某個位置指定網站的 HTML 容器配置，然後將它套用到網站中的多個頁面。</span><span class="sxs-lookup"><span data-stu-id="91cb5-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="91cb5-151">找到 `@RenderBody()` 這行。</span><span class="sxs-lookup"><span data-stu-id="91cb5-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="91cb5-152">`RenderBody` 是顯示您建立之所有檢視特定頁面的預留位置，「包裝」在版面配置頁中。</span><span class="sxs-lookup"><span data-stu-id="91cb5-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="91cb5-153">例如，如果您選取 **Privacy** 連結，**Views/Home/Privacy.cshtml** 檢視就會呈現在 `RenderBody` 方法內。</span><span class="sxs-lookup"><span data-stu-id="91cb5-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="91cb5-154">變更配置檔案中的標題、頁尾及功能表連結</span><span class="sxs-lookup"><span data-stu-id="91cb5-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="91cb5-155">以下列標記取代 *Views/Shared/_Layout. cshtml* 檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="91cb5-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="91cb5-156">所做的變更已醒目提示：</span><span class="sxs-lookup"><span data-stu-id="91cb5-156">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91cb5-157">上述標記進行了下列變更：</span><span class="sxs-lookup"><span data-stu-id="91cb5-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="91cb5-158">出現 `MvcMovie` 的 3 處改為 `Movie App`。</span><span class="sxs-lookup"><span data-stu-id="91cb5-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="91cb5-159">錨點元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 改為 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。</span><span class="sxs-lookup"><span data-stu-id="91cb5-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="91cb5-160">在上述標記中，由於此應用程式未使用[區域](xref:mvc/controllers/areas)，因此省略了 `asp-area=""` [錨點標籤協助程式屬性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)和屬性值。</span><span class="sxs-lookup"><span data-stu-id="91cb5-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="91cb5-161">**注意**：尚未 `Movies` 執行控制器。</span><span class="sxs-lookup"><span data-stu-id="91cb5-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="91cb5-162">此時，`Movie App` 連結無法運作。</span><span class="sxs-lookup"><span data-stu-id="91cb5-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="91cb5-163">儲存您的變更並選取 **Privacy** 連結。</span><span class="sxs-lookup"><span data-stu-id="91cb5-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="91cb5-164">請注意，瀏覽器索引標籤上的標題會顯示 **Privacy Policy - Movie App**，而不是 **Privacy Policy - Mvc Movie**：</span><span class="sxs-lookup"><span data-stu-id="91cb5-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Privacy 索引標籤](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="91cb5-166">選取 **Home** 連結，並注意標題和錨點文字也要顯示 **Movie App**。</span><span class="sxs-lookup"><span data-stu-id="91cb5-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="91cb5-167">我們能夠在版面配置範本中一次進行變更，並讓網站上的所有頁面反映新的連結文字和新的標題。</span><span class="sxs-lookup"><span data-stu-id="91cb5-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="91cb5-168">檢查 *Views/_ViewStart.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="91cb5-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="91cb5-169">*Views/_ViewStart.cshtml* 檔案會將 *Views/Shared/_Layout.cshtml* 檔案引入每一個檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="91cb5-170">`Layout` 屬性可用來設定不同的版面配置檢視，或將它設定為 `null`，因此不會使用任何版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="91cb5-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="91cb5-171">變更 *Views/HelloWorld/Index.cshtml* 檢視檔案的標題和 `<h2>` 元素：</span><span class="sxs-lookup"><span data-stu-id="91cb5-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="91cb5-172">標題和 `<h2>` 元素略有不同，因此可看出哪一段程式碼變更了顯示。</span><span class="sxs-lookup"><span data-stu-id="91cb5-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="91cb5-173">上述程式碼中的 `ViewData["Title"] = "Movie List";` 會將 `ViewData` 字典的 `Title` 屬性設定為 "Movie List"。</span><span class="sxs-lookup"><span data-stu-id="91cb5-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="91cb5-174">`Title` 屬性則用於版面配置頁中的 `<title>` HTML 元素：</span><span class="sxs-lookup"><span data-stu-id="91cb5-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="91cb5-175">儲存變更並巡覽至 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="91cb5-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="91cb5-176">請注意，瀏覽器標題、主要標題和次要標題已變更</span><span class="sxs-lookup"><span data-stu-id="91cb5-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="91cb5-177">(如果您在瀏覽器中沒有看到變更，可能檢視的是快取的內容。</span><span class="sxs-lookup"><span data-stu-id="91cb5-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="91cb5-178">在您的瀏覽器中按 Ctrl + F5，以強制載入來自伺服器的回應。 ) 瀏覽器標題是使用 `ViewData["Title"]` 我們在 *Index. cshtml* view 範本中設定的，以及在版面配置檔案中新增的其他 "-Movie App" 所建立。</span><span class="sxs-lookup"><span data-stu-id="91cb5-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="91cb5-179">*Index.cshtml* 檢視範本中的內容已與 *Views/Shared/_Layout.cshtml* 檢視範本合併。</span><span class="sxs-lookup"><span data-stu-id="91cb5-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="91cb5-180">單一 HTML 回應會傳送到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="91cb5-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="91cb5-181">版面配置範本可讓您輕鬆進行變更，然後套用到應用程式中的所有頁面。</span><span class="sxs-lookup"><span data-stu-id="91cb5-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="91cb5-182">若要深入了解，請參閱[版面配置](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="91cb5-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![電影清單檢視](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="91cb5-184">然而，我們的這一點點「資料」(在此案例中為 "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="91cb5-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="91cb5-185">訊息) 是硬式編碼的資料。</span><span class="sxs-lookup"><span data-stu-id="91cb5-185">message) is hard-coded, though.</span></span> <span data-ttu-id="91cb5-186">MVC 應用程式具有 "V" (檢視)，並已取得 "C" (控制器)，但還沒有 "M" (模型)。</span><span class="sxs-lookup"><span data-stu-id="91cb5-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="91cb5-187">將資料從控制器傳遞至檢視</span><span class="sxs-lookup"><span data-stu-id="91cb5-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="91cb5-188">回應傳入的 URL 要求時會叫用控制器動作。</span><span class="sxs-lookup"><span data-stu-id="91cb5-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="91cb5-189">控制器類別是撰寫處理傳入瀏覽器要求之程式碼的位置。</span><span class="sxs-lookup"><span data-stu-id="91cb5-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="91cb5-190">控制器會從資料來源擷取資料，並決定要將哪一種回應傳回瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="91cb5-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="91cb5-191">您可以從控制器使用檢視範本來產生並格式化瀏覽器的 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="91cb5-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="91cb5-192">控制器負責提供為了讓檢視範本呈現回應所需的資料。</span><span class="sxs-lookup"><span data-stu-id="91cb5-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="91cb5-193">最佳做法：檢視範本 **不** 應該執行商務邏輯，或直接與資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="91cb5-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="91cb5-194">相反地，檢視範本應該只使用由控制器提供給它的資料。</span><span class="sxs-lookup"><span data-stu-id="91cb5-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="91cb5-195">維護這項「關注點分離」，有助於保持程式碼整潔、可測試且可維護。</span><span class="sxs-lookup"><span data-stu-id="91cb5-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="91cb5-196">目前，`HelloWorldController` 類別中的 `Welcome` 方法會採用 `name` 和 `ID` 參數，然後將值直接輸出到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="91cb5-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="91cb5-197">與其讓控制器以字串方式呈現這個回應，不如變更控制器以改為使用檢視範本。</span><span class="sxs-lookup"><span data-stu-id="91cb5-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="91cb5-198">檢視範本會產生動態回應，這表示必須將適當數量的資料從控制器傳遞至檢視，以便產生回應。</span><span class="sxs-lookup"><span data-stu-id="91cb5-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="91cb5-199">透過讓控制器將檢視範本需要的動態資料 (參數) 放置在檢視範本之後可以存取的 `ViewData` 字典，即可完成這項操作。</span><span class="sxs-lookup"><span data-stu-id="91cb5-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="91cb5-200">在 *HelloWorldController.cs* 中變更 `Welcome` 方法，將 `Message` 和 `NumTimes` 值新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="91cb5-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="91cb5-201">`ViewData` 字典是動態物件，這表示您可以使用任何類型；`ViewData` 物件則要在您於其中放入某個項目之後，才會有定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="91cb5-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="91cb5-202">MVC [模型繫結](xref:mvc/models/model-binding)系統會自動將網址列上查詢字串中的具名參數 (`name` 和 `numTimes`) 對應至方法中的參數。</span><span class="sxs-lookup"><span data-stu-id="91cb5-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="91cb5-203">完整的 *HelloWorldController.cs* 檔案如下所示：</span><span class="sxs-lookup"><span data-stu-id="91cb5-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="91cb5-204">`ViewData` 字典物件包含將傳遞至檢視的資料。</span><span class="sxs-lookup"><span data-stu-id="91cb5-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="91cb5-205">建立名為 *Views/HelloWorld/Welcome.cshtml* 的 Welcome 檢視範本。</span><span class="sxs-lookup"><span data-stu-id="91cb5-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="91cb5-206">您將在 *Welcome.cshtml* 檢視範本中建立迴圈，以顯示 "Hello" `NumTimes` 次。</span><span class="sxs-lookup"><span data-stu-id="91cb5-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="91cb5-207">使用下列內容取代 *Views/HelloWorld/Welcome.cshtml* 的內容：</span><span class="sxs-lookup"><span data-stu-id="91cb5-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="91cb5-208">儲存變更並瀏覽至下列 URL：</span><span class="sxs-lookup"><span data-stu-id="91cb5-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="91cb5-209">資料是使用 [MVC 模型繫結器](xref:mvc/models/model-binding)從 URL 取得並傳遞至控制站。</span><span class="sxs-lookup"><span data-stu-id="91cb5-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="91cb5-210">控制器會將資料封裝成 `ViewData` 字典，並將該物件傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="91cb5-211">接著，檢視會以 HTML 將資料呈現到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="91cb5-211">The view then renders the data as HTML to the browser.</span></span>

![顯示一個 Welcome 標籤和顯示四次的片語 Hello Rick 的 Privacy 檢視](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="91cb5-213">在上述範例中，使用了 `ViewData` 字典將資料從控制器傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="91cb5-214">稍後在教學課程中，會使用檢視模型將控制器中的資料傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="91cb5-215">傳遞資料的檢視模型方法通常比 `ViewData` 字典方法較為慣用。</span><span class="sxs-lookup"><span data-stu-id="91cb5-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="91cb5-216">如需詳細資訊，請參閱[何時應該使用 ViewBag、ViewData 或 TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="91cb5-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="91cb5-217">在下一個教學課程中，將會建立電影資料庫。</span><span class="sxs-lookup"><span data-stu-id="91cb5-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="91cb5-218">[上一個](adding-controller.md) 
> [下一步](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="91cb5-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91cb5-219">在本節中，您會將 `HelloWorldController` 類別修改為使用 [Razor](xref:mvc/views/razor) view files，以將產生 HTML 回應的程式明確封裝到用戶端。</span><span class="sxs-lookup"><span data-stu-id="91cb5-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="91cb5-220">您可以使用建立視圖範本檔案 Razor 。</span><span class="sxs-lookup"><span data-stu-id="91cb5-220">You create a view template file using Razor.</span></span> <span data-ttu-id="91cb5-221">Razor型視圖範本的副檔名為 *cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="91cb5-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="91cb5-222">它們提供了一種使用 C# 建立 HTML 輸出的簡潔方式。</span><span class="sxs-lookup"><span data-stu-id="91cb5-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="91cb5-223">`Index` 方法目前會傳回字串，內含在控制器類別中已直接書寫好的固定訊息。</span><span class="sxs-lookup"><span data-stu-id="91cb5-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="91cb5-224">在 `HelloWorldController` 類別中，以下列程式碼取代 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="91cb5-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="91cb5-225">上述程式碼會呼叫控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。</span><span class="sxs-lookup"><span data-stu-id="91cb5-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="91cb5-226">它使用檢視範本來產生 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="91cb5-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="91cb5-227">上述 `Index` 方法等控制器方法 (也稱為「動作方法」) 通常會傳回 <xref:Microsoft.AspNetCore.Mvc.IActionResult> (或衍生自 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 的類別)，而不會傳回像是 `string` 的類型。</span><span class="sxs-lookup"><span data-stu-id="91cb5-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="91cb5-228">新增檢視</span><span class="sxs-lookup"><span data-stu-id="91cb5-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="91cb5-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91cb5-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91cb5-230">依序以滑鼠右鍵按一下 *Views* 資料夾、[新增] > [新增資料夾]，然後將資料夾命名為 *HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="91cb5-231">依序以滑鼠右鍵按一下 *Views/HelloWorld* 資料夾、[新增] > [新增項目]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="91cb5-232">在 [新增項目 - MvcMovie] 對話方塊內</span><span class="sxs-lookup"><span data-stu-id="91cb5-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="91cb5-233">在右上角的搜尋方塊中，輸入 *view*</span><span class="sxs-lookup"><span data-stu-id="91cb5-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="91cb5-234">選取 **Razor 視圖**</span><span class="sxs-lookup"><span data-stu-id="91cb5-234">Select **Razor View**</span></span>

  * <span data-ttu-id="91cb5-235">保留 [名稱] 方塊值 *Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="91cb5-236">選取 [新增]</span><span class="sxs-lookup"><span data-stu-id="91cb5-236">Select **Add**</span></span>

![[新增項目] 對話方塊](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="91cb5-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="91cb5-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="91cb5-239">為 `HelloWorldController` 新增 `Index` 檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="91cb5-240">新增資料夾，並命名為 *Views/HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="91cb5-241">將檔案新增至 *Views/HelloWorld* 資料夾，並命名為 *Index.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="91cb5-242">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="91cb5-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="91cb5-243">依序以滑鼠右鍵按一下 *Views* 資料夾、[新增] > [新增資料夾]，然後將資料夾命名為 *HelloWorld*。</span><span class="sxs-lookup"><span data-stu-id="91cb5-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="91cb5-244">依序以滑鼠右鍵按一下 *Views/HelloWorld* 資料夾、[新增] > [新增檔案]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="91cb5-245">在 [新增檔案] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="91cb5-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="91cb5-246">選取左窗格的 [Web]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="91cb5-247">選取中間窗格的 [空的 HTML 檔案]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="91cb5-248">在 [名稱] 方塊中，鍵入 **Index.cshtml**。</span><span class="sxs-lookup"><span data-stu-id="91cb5-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="91cb5-249">選取 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="91cb5-249">Select **New**.</span></span>

![[新增項目] 對話方塊](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="91cb5-251">以下列內容取代 *Views/HelloWorld/Index. cshtml* view 檔案的內容 Razor ：</span><span class="sxs-lookup"><span data-stu-id="91cb5-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="91cb5-252">瀏覽至 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="91cb5-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="91cb5-253">`HelloWorldController` 中的 `Index` 方法不會執行什麼作業；它會執行陳述式 `return View();`，其指定方法應使用檢視範本檔案來呈現瀏覽器的回應。</span><span class="sxs-lookup"><span data-stu-id="91cb5-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="91cb5-254">因為未指定檢視範本檔案名稱，因此 MVC 預設為使用預設檢視檔案。</span><span class="sxs-lookup"><span data-stu-id="91cb5-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="91cb5-255">預設檢視檔案與方法 (`Index`) 擁有相同的名稱，因此會在 */Views/HelloWorld/Index.cshtml* 中使用。</span><span class="sxs-lookup"><span data-stu-id="91cb5-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="91cb5-256">下列影像顯示檢視中硬式編碼的字串 "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="91cb5-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="91cb5-257">。</span><span class="sxs-lookup"><span data-stu-id="91cb5-257">hard-coded in the view.</span></span>

![瀏覽器視窗](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="91cb5-259">變更檢視和版面配置頁</span><span class="sxs-lookup"><span data-stu-id="91cb5-259">Change views and layout pages</span></span>

<span data-ttu-id="91cb5-260">選取功能表連結 (**MvcMovie**、**Home** 和 **Privacy**)。</span><span class="sxs-lookup"><span data-stu-id="91cb5-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="91cb5-261">每個頁面會顯示相同的功能表配置。</span><span class="sxs-lookup"><span data-stu-id="91cb5-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="91cb5-262">功能表配置是在 *Views/Shared/_Layout.cshtml* 檔案中實作。</span><span class="sxs-lookup"><span data-stu-id="91cb5-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="91cb5-263">開啟 *Views/Shared/_Layout.cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="91cb5-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="91cb5-264">[版面配置](xref:mvc/views/layout)範本可讓您在某個位置指定網站的 HTML 容器配置，然後將它套用到網站中的多個頁面。</span><span class="sxs-lookup"><span data-stu-id="91cb5-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="91cb5-265">找到 `@RenderBody()` 這行。</span><span class="sxs-lookup"><span data-stu-id="91cb5-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="91cb5-266">`RenderBody` 是顯示您建立之所有檢視特定頁面的預留位置，「包裝」在版面配置頁中。</span><span class="sxs-lookup"><span data-stu-id="91cb5-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="91cb5-267">例如，如果您選取 **Privacy** 連結，**Views/Home/Privacy.cshtml** 檢視就會呈現在 `RenderBody` 方法內。</span><span class="sxs-lookup"><span data-stu-id="91cb5-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="91cb5-268">變更配置檔案中的標題、頁尾及功能表連結</span><span class="sxs-lookup"><span data-stu-id="91cb5-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="91cb5-269">在 title 和 footer 元素中，將 `MvcMovie` 變更為 `Movie App`。</span><span class="sxs-lookup"><span data-stu-id="91cb5-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="91cb5-270">將錨點元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 變更為 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。</span><span class="sxs-lookup"><span data-stu-id="91cb5-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="91cb5-271">下列標記顯示醒目提示的變更：</span><span class="sxs-lookup"><span data-stu-id="91cb5-271">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="91cb5-272">在上述標記中，由於此應用程式未使用[區域](xref:mvc/controllers/areas)，因此已省略 `asp-area` [錨點標籤協助程式屬性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="91cb5-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="91cb5-273">**注意**：尚未 `Movies` 執行控制器。</span><span class="sxs-lookup"><span data-stu-id="91cb5-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="91cb5-274">此時，`Movie App` 連結無法運作。</span><span class="sxs-lookup"><span data-stu-id="91cb5-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="91cb5-275">儲存您的變更並選取 **Privacy** 連結。</span><span class="sxs-lookup"><span data-stu-id="91cb5-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="91cb5-276">請注意，瀏覽器索引標籤上的標題會顯示 **Privacy Policy - Movie App**，而不是 **Privacy Policy - Mvc Movie**：</span><span class="sxs-lookup"><span data-stu-id="91cb5-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Privacy 索引標籤](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="91cb5-278">選取 **Home** 連結，並注意標題和錨點文字也要顯示 **Movie App**。</span><span class="sxs-lookup"><span data-stu-id="91cb5-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="91cb5-279">我們能夠在版面配置範本中一次進行變更，並讓網站上的所有頁面反映新的連結文字和新的標題。</span><span class="sxs-lookup"><span data-stu-id="91cb5-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="91cb5-280">檢查 *Views/_ViewStart.cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="91cb5-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="91cb5-281">*Views/_ViewStart.cshtml* 檔案會將 *Views/Shared/_Layout.cshtml* 檔案引入每一個檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="91cb5-282">`Layout` 屬性可用來設定不同的版面配置檢視，或將它設定為 `null`，因此不會使用任何版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="91cb5-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="91cb5-283">變更 *Views/HelloWorld/Index.cshtml* 檢視檔案的標題和 `<h2>` 元素：</span><span class="sxs-lookup"><span data-stu-id="91cb5-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="91cb5-284">標題和 `<h2>` 元素略有不同，因此可看出哪一段程式碼變更了顯示。</span><span class="sxs-lookup"><span data-stu-id="91cb5-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="91cb5-285">上述程式碼中的 `ViewData["Title"] = "Movie List";` 會將 `ViewData` 字典的 `Title` 屬性設定為 "Movie List"。</span><span class="sxs-lookup"><span data-stu-id="91cb5-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="91cb5-286">`Title` 屬性則用於版面配置頁中的 `<title>` HTML 元素：</span><span class="sxs-lookup"><span data-stu-id="91cb5-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="91cb5-287">儲存變更並巡覽至 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="91cb5-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="91cb5-288">請注意，瀏覽器標題、主要標題和次要標題已變更</span><span class="sxs-lookup"><span data-stu-id="91cb5-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="91cb5-289">(如果您在瀏覽器中沒有看到變更，可能檢視的是快取的內容。</span><span class="sxs-lookup"><span data-stu-id="91cb5-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="91cb5-290">在您的瀏覽器中按 Ctrl + F5，以強制載入來自伺服器的回應。 ) 瀏覽器標題是使用 `ViewData["Title"]` 我們在 *Index. cshtml* view 範本中設定的，以及在版面配置檔案中新增的其他 "-Movie App" 所建立。</span><span class="sxs-lookup"><span data-stu-id="91cb5-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="91cb5-291">同時也請注意，*Index.cshtml* 檢視範本中的內容如何與 *Views/Shared/_Layout.cshtml* 檢視範本和已傳送至瀏覽器的單一 HTML 回應合併。</span><span class="sxs-lookup"><span data-stu-id="91cb5-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="91cb5-292">版面配置範本可讓您輕鬆進行會套用到應用程式之所有頁面的變更。</span><span class="sxs-lookup"><span data-stu-id="91cb5-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="91cb5-293">若要深入瞭解，請參閱 [版面](xref:mvc/views/layout)配置。</span><span class="sxs-lookup"><span data-stu-id="91cb5-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![電影清單檢視](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="91cb5-295">然而，我們的這一點點「資料」(在此案例中為 "Hello from our View Template!"</span><span class="sxs-lookup"><span data-stu-id="91cb5-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="91cb5-296">訊息) 是硬式編碼的資料。</span><span class="sxs-lookup"><span data-stu-id="91cb5-296">message) is hard-coded, though.</span></span> <span data-ttu-id="91cb5-297">MVC 應用程式具有 "V" (檢視)，並已取得 "C" (控制器)，但還沒有 "M" (模型)。</span><span class="sxs-lookup"><span data-stu-id="91cb5-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="91cb5-298">將資料從控制器傳遞至檢視</span><span class="sxs-lookup"><span data-stu-id="91cb5-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="91cb5-299">回應傳入的 URL 要求時會叫用控制器動作。</span><span class="sxs-lookup"><span data-stu-id="91cb5-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="91cb5-300">控制器類別是撰寫處理傳入瀏覽器要求之程式碼的位置。</span><span class="sxs-lookup"><span data-stu-id="91cb5-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="91cb5-301">控制器會從資料來源擷取資料，並決定要將哪一種回應傳回瀏覽器中。</span><span class="sxs-lookup"><span data-stu-id="91cb5-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="91cb5-302">您可以從控制器使用檢視範本來產生並格式化瀏覽器的 HTML 回應。</span><span class="sxs-lookup"><span data-stu-id="91cb5-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="91cb5-303">控制器負責提供為了讓檢視範本呈現回應所需的資料。</span><span class="sxs-lookup"><span data-stu-id="91cb5-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="91cb5-304">最佳做法：檢視範本 **不** 應該執行商務邏輯，或直接與資料庫互動。</span><span class="sxs-lookup"><span data-stu-id="91cb5-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="91cb5-305">相反地，檢視範本應該只使用由控制器提供給它的資料。</span><span class="sxs-lookup"><span data-stu-id="91cb5-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="91cb5-306">維護這項「關注點分離」，有助於保持程式碼整潔、可測試且可維護。</span><span class="sxs-lookup"><span data-stu-id="91cb5-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="91cb5-307">目前，`HelloWorldController` 類別中的 `Welcome` 方法會採用 `name` 和 `ID` 參數，然後將值直接輸出到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="91cb5-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="91cb5-308">與其讓控制器以字串方式呈現這個回應，不如變更控制器以改為使用檢視範本。</span><span class="sxs-lookup"><span data-stu-id="91cb5-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="91cb5-309">檢視範本會產生動態回應，這表示必須將適當數量的資料從控制器傳遞至檢視，以便產生回應。</span><span class="sxs-lookup"><span data-stu-id="91cb5-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="91cb5-310">透過讓控制器將檢視範本需要的動態資料 (參數) 放置在檢視範本之後可以存取的 `ViewData` 字典，即可完成這項操作。</span><span class="sxs-lookup"><span data-stu-id="91cb5-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="91cb5-311">在 *HelloWorldController.cs* 中變更 `Welcome` 方法，將 `Message` 和 `NumTimes` 值新增至 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="91cb5-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="91cb5-312">`ViewData` 字典是動態物件，這表示您可以使用任何類型；`ViewData` 物件則要在您於其中放入某個項目之後，才會有定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="91cb5-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="91cb5-313">MVC [模型繫結](xref:mvc/models/model-binding)系統會自動將網址列上查詢字串中的具名參數 (`name` 和 `numTimes`) 對應至方法中的參數。</span><span class="sxs-lookup"><span data-stu-id="91cb5-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="91cb5-314">完整的 *HelloWorldController.cs* 檔案如下所示：</span><span class="sxs-lookup"><span data-stu-id="91cb5-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="91cb5-315">`ViewData` 字典物件包含將傳遞至檢視的資料。</span><span class="sxs-lookup"><span data-stu-id="91cb5-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="91cb5-316">建立名為 *Views/HelloWorld/Welcome.cshtml* 的 Welcome 檢視範本。</span><span class="sxs-lookup"><span data-stu-id="91cb5-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="91cb5-317">您將在 *Welcome.cshtml* 檢視範本中建立迴圈，以顯示 "Hello" `NumTimes` 次。</span><span class="sxs-lookup"><span data-stu-id="91cb5-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="91cb5-318">使用下列內容取代 *Views/HelloWorld/Welcome.cshtml* 的內容：</span><span class="sxs-lookup"><span data-stu-id="91cb5-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="91cb5-319">儲存變更並瀏覽至下列 URL：</span><span class="sxs-lookup"><span data-stu-id="91cb5-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="91cb5-320">資料是使用 [MVC 模型繫結器](xref:mvc/models/model-binding)從 URL 取得並傳遞至控制站。</span><span class="sxs-lookup"><span data-stu-id="91cb5-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="91cb5-321">控制器會將資料封裝成 `ViewData` 字典，並將該物件傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="91cb5-322">接著，檢視會以 HTML 將資料呈現到瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="91cb5-322">The view then renders the data as HTML to the browser.</span></span>

![顯示一個 Welcome 標籤和顯示四次的片語 Hello Rick 的 Privacy 檢視](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="91cb5-324">在上述範例中，使用了 `ViewData` 字典將資料從控制器傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="91cb5-325">稍後在教學課程中，會使用檢視模型將控制器中的資料傳遞至檢視。</span><span class="sxs-lookup"><span data-stu-id="91cb5-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="91cb5-326">傳遞資料的檢視模型方法通常比 `ViewData` 字典方法較為慣用。</span><span class="sxs-lookup"><span data-stu-id="91cb5-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="91cb5-327">如需詳細資訊，請參閱[何時應該使用 ViewBag、ViewData 或 TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="91cb5-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="91cb5-328">在下一個教學課程中，將會建立電影資料庫。</span><span class="sxs-lookup"><span data-stu-id="91cb5-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="91cb5-329">[上一個](adding-controller.md) 
> [下一步](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="91cb5-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
