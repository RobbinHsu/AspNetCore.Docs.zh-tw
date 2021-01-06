# <a name="blazor-webassembly-sample-app"></a><span data-ttu-id="665de-101">Blazor WebAssembly 範例應用程式</span><span class="sxs-lookup"><span data-stu-id="665de-101">Blazor WebAssembly Sample App</span></span>

<span data-ttu-id="665de-102">此範例說明如何使用 Blazor 檔中所述的 Blazor 案例。</span><span class="sxs-lookup"><span data-stu-id="665de-102">This sample illustrates the use of Blazor scenarios described in the Blazor documentation.</span></span>

## <a name="call-web-api-example"></a><span data-ttu-id="665de-103">呼叫 web API 範例</span><span class="sxs-lookup"><span data-stu-id="665de-103">Call web API example</span></span>

<span data-ttu-id="665de-104">Web API 範例需要以範例應用程式為基礎的執行中 web API，才能使用 ASP.NET Core 主題來 <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">建立 WEB api</a> ，此 api 預設會使用與 Blazor 範例應用程式相同的 HTTPS 埠 (5001) 。</span><span class="sxs-lookup"><span data-stu-id="665de-104">The web API example requires a running web API based on the sample app for the <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Create a web API with ASP.NET Core</a> topic, which by default uses the same HTTPS port (5001) as the Blazor sample app.</span></span> <span data-ttu-id="665de-105">若要同時在同一部電腦上使用這兩個應用程式，請變更 web API 的埠 (例如，使用埠 10000) 。</span><span class="sxs-lookup"><span data-stu-id="665de-105">To use both apps on the same machine at the same time, change the port of the web API (for example, use port 10000).</span></span> <span data-ttu-id="665de-106">範例應用程式會對 web API 提出要求 `https://localhost:10000/api/TodoItems` 。</span><span class="sxs-lookup"><span data-stu-id="665de-106">The sample app makes requests to the web API at `https://localhost:10000/api/TodoItems`.</span></span> <span data-ttu-id="665de-107">如果使用不同的 web API 位址，請更新 `ServiceEndpoint` Razor 元件區塊中的常數值 `@code` 。</span><span class="sxs-lookup"><span data-stu-id="665de-107">If a different web API address is used, update the `ServiceEndpoint` constant value in the Razor component's `@code` block.</span></span></p>

<span data-ttu-id="665de-108">範例應用程式會讓 <a href="https://docs.microsoft.com/aspnet/core/security/cors">跨原始來源資源分享 (CORS) </a> 要求 `http://localhost:5000` 或 `https://localhost:5001` web API 的要求。</span><span class="sxs-lookup"><span data-stu-id="665de-108">The sample app makes a <a href="https://docs.microsoft.com/aspnet/core/security/cors">cross-origin resource sharing (CORS)</a> request from `http://localhost:5000` or `https://localhost:5001` to the web API.</span></span> <span data-ttu-id="665de-109">允許 (授權 cookie/標頭) 的認證。</span><span class="sxs-lookup"><span data-stu-id="665de-109">Credentials (authorization cookies/headers) are permitted.</span></span> <span data-ttu-id="665de-110">將下列 CORS 中介軟體設定新增至 web API 的 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="665de-110">Add the following CORS middleware configuration to the web API's `Startup.Configure` method:</span></span></p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

<span data-ttu-id="665de-111">`WithOrigins`視需要調整 Blazor 應用程式所需的網域和埠。</span><span class="sxs-lookup"><span data-stu-id="665de-111">Adjust the domains and ports of `WithOrigins` as needed for the Blazor app.</span></span>

<span data-ttu-id="665de-112">Web API 是針對 CORS 設定的，以允許來自用戶端程式代碼的授權 cookie/標頭和要求，但本教學課程所建立的 web API 實際上不會授權要求。</span><span class="sxs-lookup"><span data-stu-id="665de-112">The web API is configured for CORS to permit authorization cookies/headers and requests from client code, but the web API as created by the tutorial doesn't actually authorize requests.</span></span> <span data-ttu-id="665de-113">請參閱 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 的安全性和身分識別文章</a> ，以取得實施指引。</span><span class="sxs-lookup"><span data-stu-id="665de-113">See the <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core Security and Identity articles</a> for implementation guidance.</span></span>
