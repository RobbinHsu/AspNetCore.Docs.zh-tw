---
title: 教學課程：使用 JavaScript 呼叫 ASP.NET Core web API
author: rick-anderson
description: 了解如何使用 JavaScript 呼叫 ASP.NET Core Web API。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 11/26/2019
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: c32c5befe0be3b1ad4bd87649d3cc74b0296a134
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94703705"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="75425-103">教學課程：使用 JavaScript 呼叫 ASP.NET Core web API</span><span class="sxs-lookup"><span data-stu-id="75425-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="75425-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="75425-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="75425-105">此教學課程會示範如何使用 JavaScript (使用 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 呼叫 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="75425-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="75425-106">針對 ASP.NET Core 2.2，請參閱[使用 JavaScript 呼叫 Web API](xref:tutorials/first-web-api#call-the-web-api-with-javascript) 的 2.2 版。</span><span class="sxs-lookup"><span data-stu-id="75425-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="75425-107">先決條件</span><span class="sxs-lookup"><span data-stu-id="75425-107">Prerequisites</span></span>

* <span data-ttu-id="75425-108">完成 [教學課程：建立 WEB API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="75425-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="75425-109">熟悉 CSS、HTML 和 JavaScript</span><span class="sxs-lookup"><span data-stu-id="75425-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="75425-110">使用 JavaScript 呼叫 Web API</span><span class="sxs-lookup"><span data-stu-id="75425-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="75425-111">在此節中，您會新增一個 HTML 網頁，其中包含用於建立及管理待辦事項的表單。</span><span class="sxs-lookup"><span data-stu-id="75425-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="75425-112">事件處理常式會附加至頁面上的元素。</span><span class="sxs-lookup"><span data-stu-id="75425-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="75425-113">事件處理常式會產生對 Web API 的動作方法發出的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="75425-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="75425-114">Fetch API 的 `fetch` 函式會起始每個 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="75425-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="75425-115">函數會傳回 `fetch` [承諾](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) 物件，其中包含以物件表示的 HTTP 回應 `Response` 。</span><span class="sxs-lookup"><span data-stu-id="75425-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="75425-116">常見的模式是叫用 `Response` 物件上的 `json` 函式，以擷取 JSON 回應主體。</span><span class="sxs-lookup"><span data-stu-id="75425-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="75425-117">JavaScript 會使用來自 Web API 回應的詳細資料來更新頁面。</span><span class="sxs-lookup"><span data-stu-id="75425-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="75425-118">最簡單 `fetch` 呼叫會接受代表路由的單一參數。</span><span class="sxs-lookup"><span data-stu-id="75425-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="75425-119">第二個參數 (稱為 `init` 物件) 是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="75425-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="75425-120">`init` 是用來設定 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="75425-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="75425-121">設定應用程式以 [提供靜態](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 檔案，並 [啟用預設檔案對應](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)。</span><span class="sxs-lookup"><span data-stu-id="75425-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="75425-122">*Startup.cs* 的 `Configure` 方法中需要下列反白顯示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="75425-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="75425-123">在專案根目錄中建立 *wwwroot* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="75425-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="75425-124">在 *wwwroot* 資料夾內建立 *js* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="75425-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="75425-125">將名為 *index.html* 的 HTML 檔案新增至 *wwwroot* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="75425-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="75425-126">以下列標記取代 *index.html* 的內容：</span><span class="sxs-lookup"><span data-stu-id="75425-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="75425-127">將名為 *site .css* 的 css 檔案新增至 *wwwroot/CSS* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="75425-127">Add a CSS file named *site.css* to the *wwwroot/css* folder.</span></span> <span data-ttu-id="75425-128">以下列樣式取代 *網站* 的內容：</span><span class="sxs-lookup"><span data-stu-id="75425-128">Replace the contents of *site.css* with the following styles:</span></span>

    [!code-css[](first-web-api/samples/3.0/TodoApi/wwwroot/css/site.css)]

1. <span data-ttu-id="75425-129">將名為 *site.js* 的 JavaScript 檔案新增至 *wwwroot/js* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="75425-129">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="75425-130">以下列程式碼取代 *site.js* 的內容：</span><span class="sxs-lookup"><span data-stu-id="75425-130">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="75425-131">若要在本機測試 HTML 網頁，可能需要變更 ASP.NET Core 專案的啟動設定：</span><span class="sxs-lookup"><span data-stu-id="75425-131">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="75425-132">開啟 *Properties\launchSettings.json*。</span><span class="sxs-lookup"><span data-stu-id="75425-132">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="75425-133">移除 `launchUrl` 屬性，以強制在專案的預設檔案 *index.html* 開啟應用程式 &mdash; 。</span><span class="sxs-lookup"><span data-stu-id="75425-133">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="75425-134">此範例會呼叫 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="75425-134">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="75425-135">以下是關於 Web API 要求的說明。</span><span class="sxs-lookup"><span data-stu-id="75425-135">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="75425-136">取得待辦事項的清單</span><span class="sxs-lookup"><span data-stu-id="75425-136">Get a list of to-do items</span></span>

<span data-ttu-id="75425-137">在下列程式碼中，會將 HTTP GET 要求傳送至 *api/TodoItems* 路由：</span><span class="sxs-lookup"><span data-stu-id="75425-137">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="75425-138">當 Web API 傳回成功狀態碼時，會叫用 `_displayItems` 函式。</span><span class="sxs-lookup"><span data-stu-id="75425-138">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="75425-139">`_displayItems` 所接受之陣列參數中的每個待辦事項，都會加入具有 [編輯] 和 [刪除] 按鈕的表格。</span><span class="sxs-lookup"><span data-stu-id="75425-139">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="75425-140">如果 Web API 要求失敗，則會在瀏覽器的主控台中記錄錯誤。</span><span class="sxs-lookup"><span data-stu-id="75425-140">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="75425-141">新增待辦事項</span><span class="sxs-lookup"><span data-stu-id="75425-141">Add a to-do item</span></span>

<span data-ttu-id="75425-142">在下列程式碼中：</span><span class="sxs-lookup"><span data-stu-id="75425-142">In the following code:</span></span>

* <span data-ttu-id="75425-143">系統會宣告 `item` 變數來建構待辦事項的物件常值表示法。</span><span class="sxs-lookup"><span data-stu-id="75425-143">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="75425-144">您可以使用下列選項來設定 Fetch 要求：</span><span class="sxs-lookup"><span data-stu-id="75425-144">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="75425-145">`method`&mdash;指定 POST HTTP 動作動詞命令。</span><span class="sxs-lookup"><span data-stu-id="75425-145">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="75425-146">`body`&mdash;指定要求本文的 JSON 表示法。</span><span class="sxs-lookup"><span data-stu-id="75425-146">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="75425-147">JSON 是透過將儲存在 `item` 中的物件常值傳遞至 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 函式來產生。</span><span class="sxs-lookup"><span data-stu-id="75425-147">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="75425-148">`headers`&mdash;指定 `Accept` 和 `Content-Type` HTTP 要求標題。</span><span class="sxs-lookup"><span data-stu-id="75425-148">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="75425-149">這兩個標題都設定為 `application/json`，以分別指定接收和傳送的媒體類型。</span><span class="sxs-lookup"><span data-stu-id="75425-149">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="75425-150">HTTP POST 要求會傳送至 *api/TodoItems* 路由。</span><span class="sxs-lookup"><span data-stu-id="75425-150">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="75425-151">當 Web API 傳回成功狀態碼時，會叫用 `getItems` 函式來更新 HTML 表格 。</span><span class="sxs-lookup"><span data-stu-id="75425-151">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="75425-152">如果 Web API 要求失敗，則會在瀏覽器的主控台中記錄錯誤。</span><span class="sxs-lookup"><span data-stu-id="75425-152">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="75425-153">更新待辦事項</span><span class="sxs-lookup"><span data-stu-id="75425-153">Update a to-do item</span></span>

<span data-ttu-id="75425-154">更新待辦事項類似於新增待辦事項；不過，有兩個重大差異：</span><span class="sxs-lookup"><span data-stu-id="75425-154">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="75425-155">路由的尾碼為要更新之項目的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="75425-155">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="75425-156">例如，*api/TodoItems/1*。</span><span class="sxs-lookup"><span data-stu-id="75425-156">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="75425-157">HTTP 動作動詞命令是 PUT，如 `method` 選項所指示。</span><span class="sxs-lookup"><span data-stu-id="75425-157">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="75425-158">刪除待辦事項</span><span class="sxs-lookup"><span data-stu-id="75425-158">Delete a to-do item</span></span>

<span data-ttu-id="75425-159">若要刪除待辦事項，請將要求的 `method` 選項設定為 `DELETE`，並在 URL 中指定項目的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="75425-159">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="75425-160">前進到下一個教學課程來了解如何產生 Web API 說明頁面：</span><span class="sxs-lookup"><span data-stu-id="75425-160">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
