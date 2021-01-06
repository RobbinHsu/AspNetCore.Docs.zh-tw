---
title: 從 ASP.NET Core 呼叫 web API Blazor WebAssembly
author: guardrex
description: 瞭解如何 Blazor WebAssembly 使用 JSON 協助程式從應用程式呼叫 WEB API，包括建立跨原始資源分享 (CORS) 要求。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 85b3ded6ec25310a573e99cbedf0df005d92bdbe
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93234409"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a><span data-ttu-id="606a2-103">從 ASP.NET Core 呼叫 web API Blazor</span><span class="sxs-lookup"><span data-stu-id="606a2-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="606a2-104">[Luke Latham](https://github.com/guardrex)、 [Daniel Roth](https://github.com/danroth27)和[Juan De la cruz 一起探討](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="606a2-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="606a2-105">本主題適用于 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="606a2-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="606a2-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) 應用程式會使用 <xref:System.Net.Http.HttpClient> 實例（通常是使用建立）來呼叫 Web api <xref:System.Net.Http.IHttpClientFactory> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="606a2-107">如需適用于的指引 Blazor Server ，請參閱 <xref:fundamentals/http-requests> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="606a2-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 應用程式會使用預先設定的服務來呼叫 web Api <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="606a2-109">撰寫要求（可包含 JavaScript [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 選項），使用 Blazor JSON 協助程式或搭配 <xref:System.Net.Http.HttpRequestMessage> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="606a2-110"><xref:System.Net.Http.HttpClient>應用程式中的服務著重于將 Blazor WebAssembly 要求傳回給來源伺服器。</span><span class="sxs-lookup"><span data-stu-id="606a2-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="606a2-111">本主題中的指導方針僅適用于 Blazor WebAssembly 應用程式。</span><span class="sxs-lookup"><span data-stu-id="606a2-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="606a2-112">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([如何下載](xref:index#how-to-download-a-sample)) ：選取 `BlazorWebAssemblySample` 應用程式。</span><span class="sxs-lookup"><span data-stu-id="606a2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="606a2-113">請參閱範例應用程式中的下列元件 `BlazorWebAssemblySample` ：</span><span class="sxs-lookup"><span data-stu-id="606a2-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="606a2-114"> () 呼叫 Web API `Pages/CallWebAPI.razor`</span><span class="sxs-lookup"><span data-stu-id="606a2-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="606a2-115">HTTP 要求測試器 (`Components/HTTPRequestTester.razor`) </span><span class="sxs-lookup"><span data-stu-id="606a2-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="606a2-116">套件</span><span class="sxs-lookup"><span data-stu-id="606a2-116">Packages</span></span>

<span data-ttu-id="606a2-117">參考 [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) 專案檔中的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="606a2-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="606a2-118">新增 HttpClient 服務</span><span class="sxs-lookup"><span data-stu-id="606a2-118">Add the HttpClient service</span></span>

<span data-ttu-id="606a2-119">在中 `Program.Main` ，新增 <xref:System.Net.Http.HttpClient> 服務（如果尚未存在）：</span><span class="sxs-lookup"><span data-stu-id="606a2-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="606a2-120">HttpClient 和 JSON 協助程式</span><span class="sxs-lookup"><span data-stu-id="606a2-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="606a2-121">在 Blazor WebAssembly 應用程式中，可做為預先設定的服務，以向 [`HttpClient`](xref:fundamentals/http-requests) 源伺服器提出要求。</span><span class="sxs-lookup"><span data-stu-id="606a2-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="606a2-122">Blazor Server依預設，應用程式不會包含 <xref:System.Net.Http.HttpClient> 服務。</span><span class="sxs-lookup"><span data-stu-id="606a2-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="606a2-123"><xref:System.Net.Http.HttpClient>使用[ `HttpClient` 工廠基礎結構](xref:fundamentals/http-requests)，提供給應用程式。</span><span class="sxs-lookup"><span data-stu-id="606a2-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="606a2-124"><xref:System.Net.Http.HttpClient> 此外，也會使用 JSON 協助程式來呼叫協力廠商 web API 端點。</span><span class="sxs-lookup"><span data-stu-id="606a2-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="606a2-125"><xref:System.Net.Http.HttpClient> 是使用瀏覽器 [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 來執行，並且受限於其限制，包括強制執行相同的原始原則。</span><span class="sxs-lookup"><span data-stu-id="606a2-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="606a2-126">用戶端的基底位址會設定為來源伺服器的位址。</span><span class="sxs-lookup"><span data-stu-id="606a2-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="606a2-127">使用指示詞插入 <xref:System.Net.Http.HttpClient> 實例 [`@inject`](xref:mvc/views/razor#inject) ：</span><span class="sxs-lookup"><span data-stu-id="606a2-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="606a2-128">在下列範例中，Todo web API 會處理 (CRUD) 作業的建立、讀取、更新和刪除作業。</span><span class="sxs-lookup"><span data-stu-id="606a2-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="606a2-129">這些範例是以 `TodoItem` 儲存下列各類的類別為基礎：</span><span class="sxs-lookup"><span data-stu-id="606a2-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="606a2-130">ID (`Id` ， `long`) ：專案的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="606a2-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="606a2-131">名稱 (`Name` ， `string`) ：專案的名稱。</span><span class="sxs-lookup"><span data-stu-id="606a2-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="606a2-132">狀態 (`IsComplete` ， `bool`) ：是否已完成 Todo 專案的指示。</span><span class="sxs-lookup"><span data-stu-id="606a2-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="606a2-133">JSON helper 方法會在下列範例中將要求傳送至 URI (web API) 並處理回應：</span><span class="sxs-lookup"><span data-stu-id="606a2-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="606a2-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>：傳送 HTTP GET 要求，並剖析 JSON 回應主體來建立物件。</span><span class="sxs-lookup"><span data-stu-id="606a2-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="606a2-135">在下列程式碼中， `todoItems` 元件會顯示。</span><span class="sxs-lookup"><span data-stu-id="606a2-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="606a2-136">`GetTodoItems`當元件 () 轉譯完成時，就會觸發方法 [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) 。</span><span class="sxs-lookup"><span data-stu-id="606a2-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="606a2-137">如需完整範例，請參閱範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="606a2-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="606a2-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>：傳送 HTTP POST 要求（包括 JSON 編碼的內容），並剖析 JSON 回應主體來建立物件。</span><span class="sxs-lookup"><span data-stu-id="606a2-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="606a2-139">在下列程式碼中， `newItemName` 是由元件的繫結項目提供。</span><span class="sxs-lookup"><span data-stu-id="606a2-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="606a2-140">藉 `AddItem` 由選取元素來觸發方法 `<button>` 。</span><span class="sxs-lookup"><span data-stu-id="606a2-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="606a2-141">如需完整範例，請參閱範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="606a2-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="606a2-142">呼叫以傳回 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="606a2-143">若要從回應訊息還原序列化 JSON 內容，請使用 `ReadFromJsonAsync<T>` 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="606a2-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="606a2-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>：傳送 HTTP PUT 要求，包括 JSON 編碼的內容。</span><span class="sxs-lookup"><span data-stu-id="606a2-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="606a2-145">在下列程式碼中， `editItem` 和的值 `Name` `IsCompleted` 是由元件的繫結項目提供。</span><span class="sxs-lookup"><span data-stu-id="606a2-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="606a2-146">`Id`當專案在 UI 的另一個部分中選取並被呼叫時，會設定專案 `EditItem` 。</span><span class="sxs-lookup"><span data-stu-id="606a2-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="606a2-147">藉 `SaveItem` 由選取 [儲存] 元素來觸發方法 `<button>` 。</span><span class="sxs-lookup"><span data-stu-id="606a2-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="606a2-148">如需完整範例，請參閱範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="606a2-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="606a2-149">呼叫以傳回 <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="606a2-150">若要從回應訊息還原序列化 JSON 內容，請使用 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="606a2-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="606a2-151"><xref:System.Net.Http> 包含用來傳送 HTTP 要求和接收 HTTP 回應的額外擴充方法。</span><span class="sxs-lookup"><span data-stu-id="606a2-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="606a2-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> 用來將 HTTP DELETE 要求傳送至 web API。</span><span class="sxs-lookup"><span data-stu-id="606a2-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="606a2-153">在下列程式碼中，Delete `<button>` 元素會呼叫 `DeleteItem` 方法。</span><span class="sxs-lookup"><span data-stu-id="606a2-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="606a2-154">綁定 `<input>` 項會提供 `id` 要刪除之專案的。</span><span class="sxs-lookup"><span data-stu-id="606a2-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="606a2-155">如需完整範例，請參閱範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="606a2-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="606a2-156">使用 IHttpClientFactory 命名的 HttpClient</span><span class="sxs-lookup"><span data-stu-id="606a2-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="606a2-157"><xref:System.Net.Http.IHttpClientFactory> 支援服務以及命名的設定 <xref:System.Net.Http.HttpClient> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="606a2-158">參考 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 專案檔中的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="606a2-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package in the project file.</span></span>

<span data-ttu-id="606a2-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="606a2-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="606a2-160">`FetchData` 元件 (`Pages/FetchData.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="606a2-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="606a2-161">具類型的 HttpClient</span><span class="sxs-lookup"><span data-stu-id="606a2-161">Typed HttpClient</span></span>

<span data-ttu-id="606a2-162">具型別會 <xref:System.Net.Http.HttpClient> 使用一或多個應用程式的 <xref:System.Net.Http.HttpClient> 實例（預設或命名）來傳回一或多個 web API 端點的資料。</span><span class="sxs-lookup"><span data-stu-id="606a2-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="606a2-163">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="606a2-163">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

<span data-ttu-id="606a2-164">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="606a2-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="606a2-165">元件會插入型別 <xref:System.Net.Http.HttpClient> 以呼叫 WEB API。</span><span class="sxs-lookup"><span data-stu-id="606a2-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="606a2-166">`FetchData` 元件 (`Pages/FetchData.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="606a2-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="606a2-167">`HttpClient` 以及 `HttpRequestMessage` 使用 FETCH API 要求選項</span><span class="sxs-lookup"><span data-stu-id="606a2-167">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="606a2-168">在應用程式的 WebAssembly 上執行時 Blazor WebAssembly ， [`HttpClient`](xref:fundamentals/http-requests) ([API 檔](xref:System.Net.Http.HttpClient)) 並 <xref:System.Net.Http.HttpRequestMessage> 可用於自訂要求。</span><span class="sxs-lookup"><span data-stu-id="606a2-168">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="606a2-169">例如，您可以指定 HTTP 方法和要求標頭。</span><span class="sxs-lookup"><span data-stu-id="606a2-169">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="606a2-170">下列元件會 `POST` 要求伺服器上的待辦事項清單 API 端點，並顯示回應主體：</span><span class="sxs-lookup"><span data-stu-id="606a2-170">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="606a2-171">.NET WebAssembly 的執行會 <xref:System.Net.Http.HttpClient> 使用 [WindowOrWorkerGlobalScope ( # B1 ](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)。</span><span class="sxs-lookup"><span data-stu-id="606a2-171">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="606a2-172">Fetch 可讓您設定數個 [要求特定的選項](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="606a2-172">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="606a2-173">您可以使用 <xref:System.Net.Http.HttpRequestMessage> 下表所示的擴充方法來設定 HTTP 提取要求選項。</span><span class="sxs-lookup"><span data-stu-id="606a2-173">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="606a2-174">擴充方法</span><span class="sxs-lookup"><span data-stu-id="606a2-174">Extension method</span></span> | <span data-ttu-id="606a2-175">Fetch 要求屬性</span><span class="sxs-lookup"><span data-stu-id="606a2-175">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="606a2-176">您可以使用更通用的擴充方法來設定其他選項 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-176">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="606a2-177">HTTP 回應通常會在 Blazor WebAssembly 應用程式中進行緩衝處理，以啟用對回應內容進行同步讀取的支援。</span><span class="sxs-lookup"><span data-stu-id="606a2-177">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="606a2-178">若要啟用對回應串流的支援，請 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 對要求使用擴充方法。</span><span class="sxs-lookup"><span data-stu-id="606a2-178">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="606a2-179">若要在跨原始來源要求中包含認證，請使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 擴充方法：</span><span class="sxs-lookup"><span data-stu-id="606a2-179">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="606a2-180">如需有關 Fetch API 選項的詳細資訊，請參閱 [MDN web 檔： WindowOrWorkerGlobalScope。提取 ( # A1：參數](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)。</span><span class="sxs-lookup"><span data-stu-id="606a2-180">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="handle-errors"></a><span data-ttu-id="606a2-181">處理錯誤</span><span class="sxs-lookup"><span data-stu-id="606a2-181">Handle errors</span></span>

<span data-ttu-id="606a2-182">當與 web API 互動時發生錯誤，可由開發人員程式碼處理。</span><span class="sxs-lookup"><span data-stu-id="606a2-182">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="606a2-183">例如， <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> 需要來自伺服器 API 的 JSON 回應，以及 `Content-Type` 的 `application/json` 。</span><span class="sxs-lookup"><span data-stu-id="606a2-183">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="606a2-184">如果回應不是 JSON 格式，則內容驗證會擲回 <xref:System.NotSupportedException> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-184">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="606a2-185">在下列範例中，氣象預測資料要求的 URI 端點拼寫錯誤。</span><span class="sxs-lookup"><span data-stu-id="606a2-185">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="606a2-186">URI 應該會 `WeatherForecast` 在呼叫中顯示，但 `WeatherForcast` (遺漏 "e" ) 。</span><span class="sxs-lookup"><span data-stu-id="606a2-186">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="606a2-187"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>呼叫需要傳回 JSON，但是伺服器會傳回 HTML，以取得伺服器上未處理的例外狀況，以及 `Content-Type` 的 `text/html` 。</span><span class="sxs-lookup"><span data-stu-id="606a2-187">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="606a2-188">伺服器上發生未處理的例外狀況，因為找不到路徑，且中介軟體無法提供要求的頁面或觀點。</span><span class="sxs-lookup"><span data-stu-id="606a2-188">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="606a2-189">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 用戶端上， <xref:System.NotSupportedException> 會在回應內容驗證為非 JSON 時擲回。</span><span class="sxs-lookup"><span data-stu-id="606a2-189">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="606a2-190">例外狀況會在區塊中 `catch` 攔截，其中自訂邏輯可能會記錄錯誤，或向使用者呈現易記的錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="606a2-190">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="606a2-191">上述範例僅供示範之用。</span><span class="sxs-lookup"><span data-stu-id="606a2-191">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="606a2-192">Web API 伺服器應用程式可以設定為傳回 JSON，即使端點不存在，或伺服器上發生未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="606a2-192">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="606a2-193">如需詳細資訊，請參閱 <xref:blazor/fundamentals/handle-errors> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-193">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="606a2-194">跨原始來源資源分享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="606a2-194">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="606a2-195">瀏覽器安全性可防止網頁向與提供網頁的不同網域提出要求。</span><span class="sxs-lookup"><span data-stu-id="606a2-195">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="606a2-196">這項限制稱為 *相同原始來源原則*。</span><span class="sxs-lookup"><span data-stu-id="606a2-196">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="606a2-197">相同來源原則可防止惡意網站從另一個網站讀取敏感性資料。</span><span class="sxs-lookup"><span data-stu-id="606a2-197">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="606a2-198">若要從瀏覽器向具有不同來源的端點提出要求， *端點* 必須啟用 [跨原始來源資源分享 (CORS)](https://www.w3.org/TR/cors/)。</span><span class="sxs-lookup"><span data-stu-id="606a2-198">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="606a2-199">[ Blazor WebAssembly (Blazor WebAssemblySample) 範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)會示範如何在呼叫 Web API 元件中使用 CORS (`Pages/CallWebAPI.razor`) 。</span><span class="sxs-lookup"><span data-stu-id="606a2-199">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="606a2-200">如需在應用程式中使用安全要求進行 CORS 的詳細資訊 Blazor ，請參閱 <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-200">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="606a2-201">如需搭配 ASP.NET Core apps 之 CORS 的一般資訊，請參閱 <xref:security/cors> 。</span><span class="sxs-lookup"><span data-stu-id="606a2-201">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="606a2-202">其他資源</span><span class="sxs-lookup"><span data-stu-id="606a2-202">Additional resources</span></span>

* <span data-ttu-id="606a2-203"><xref:blazor/security/webassembly/additional-scenarios>：包含使用的涵蓋範圍 <xref:System.Net.Http.HttpClient> ，以提出安全的 WEB API 要求。</span><span class="sxs-lookup"><span data-stu-id="606a2-203"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="606a2-204">Kestrel HTTPS 端點設定</span><span class="sxs-lookup"><span data-stu-id="606a2-204">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="606a2-205">在 W3C (CORS) 的跨原始資源分享</span><span class="sxs-lookup"><span data-stu-id="606a2-205">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
