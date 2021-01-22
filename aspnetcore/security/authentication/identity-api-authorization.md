---
title: ASP.NET Core 上的單一頁面應用程式驗證簡介
author: javiercn
description: 搭配使用 Identity 于 ASP.NET Core 應用程式內的單一頁面應用程式。
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658660"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="6b8d1-103">Spa 的驗證和授權</span><span class="sxs-lookup"><span data-stu-id="6b8d1-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="6b8d1-104">ASP.NET Core 3.1 和更新版本的範本會在單一頁面應用程式中提供驗證， (使用 API 授權支援的 Spa) 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="6b8d1-105">ASP.NET Core Identity針對驗證和儲存使用者，會結合[ Identity 伺服器](https://identityserver.io/)來執行 OpenID Connect。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="6b8d1-106">驗證參數已新增至「 **角度** 」和「 **回應** 」專案範本，類似于 Web 應用程式中的驗證參數 **(模型-視圖控制器)** (MVC) 和 **web 應用程式** (Razor 頁面) 專案範本。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="6b8d1-107">允許的參數值為 [ **無** ] 和 [ **個人**]。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="6b8d1-108">**React.js 和 Redux** 專案範本目前不支援驗證參數。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="6b8d1-109">建立具有 API 授權支援的應用程式</span><span class="sxs-lookup"><span data-stu-id="6b8d1-109">Create an app with API authorization support</span></span>

<span data-ttu-id="6b8d1-110">使用者驗證和授權可以搭配使用角度和回應 Spa。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="6b8d1-111">開啟命令 shell，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="6b8d1-112">**角度**：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="6b8d1-113">**回應**：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="6b8d1-114">上述命令會使用包含 SPA 的 *ClientApp* 目錄來建立 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="6b8d1-115">應用程式 ASP.NET Core 元件的一般描述</span><span class="sxs-lookup"><span data-stu-id="6b8d1-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="6b8d1-116">下列各節說明在包含驗證支援時，專案的新增專案：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="6b8d1-117">啟始類別</span><span class="sxs-lookup"><span data-stu-id="6b8d1-117">Startup class</span></span>

<span data-ttu-id="6b8d1-118">下列程式碼範例依賴 [AspNetCore. ApiAuthorization。 IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="6b8d1-119">這些範例會使用 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 和擴充方法來設定 API 驗證和授權 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="6b8d1-120">使用回應或角 SPA 專案範本進行驗證的專案，會包含此封裝的參考。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="6b8d1-121">`Startup`類別具有下列新增專案：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="6b8d1-122">在 `Startup.ConfigureServices` 方法內：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="6b8d1-123">Identity 使用預設 UI：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="6b8d1-124">Identity具有其他 `AddApiAuthorization` helper 方法的伺服器，可在伺服器上設定一些預設 ASP.NET Core 慣例 Identity ：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="6b8d1-125">使用額外的 helper 方法進行驗證，以設定 `AddIdentityServerJwt` 應用程式驗證服務器所產生的 JWT 權杖 Identity ：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="6b8d1-126">在 `Startup.Configure` 方法內：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="6b8d1-127">負責驗證要求認證和在要求內容上設定使用者的驗證中介軟體：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="6b8d1-128">Identity公開 OpenID Connect 端點的伺服器中介軟體：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="6b8d1-129">Linux 上的 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6b8d1-129">Azure App Service on Linux</span></span>

<span data-ttu-id="6b8d1-130">針對 Linux 上的 Azure App Service 部署，請在下列程式中明確指定簽發者 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-130">For Azure App Service deployments on Linux, specify the issuer explicitly in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

<span data-ttu-id="6b8d1-131">在上述程式碼中， `{AUTHORITY}` 預留位置是在 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> 進行 OpenID Connect 呼叫時要使用的預留位置。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-131">In the preceding code, the `{AUTHORITY}` placeholder is the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> to use when making OpenID Connect calls.</span></span>

<span data-ttu-id="6b8d1-132">範例：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-132">Example:</span></span>

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a><span data-ttu-id="6b8d1-133">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="6b8d1-133">AddApiAuthorization</span></span>

<span data-ttu-id="6b8d1-134">此 helper 方法會將 Identity 伺服器設定為使用我們支援的設定。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-134">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="6b8d1-135">Identity伺服器是一種功能強大且可擴充的架構，可處理應用程式安全性的考慮。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-135">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="6b8d1-136">同時也會在最常見的情況下公開不必要的複雜性。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-136">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="6b8d1-137">因此，系統會為您提供一組慣例和設定選項，這些選項會被視為良好的起點。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-137">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="6b8d1-138">一旦您的驗證需要變更，伺服器的完整功能 Identity 仍然可以自訂驗證以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-138">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="6b8d1-139">新增 Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="6b8d1-139">AddIdentityServerJwt</span></span>

<span data-ttu-id="6b8d1-140">此 helper 方法會設定應用程式的原則配置作為預設驗證處理常式。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-140">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="6b8d1-141">原則設定為可讓您 Identity 處理所有路由至 Identity URL 空間 "/" 中之子路徑的要求 Identity 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-141">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="6b8d1-142">會 `JwtBearerHandler` 處理所有其他要求。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-142">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="6b8d1-143">此外，此方法會 `<<ApplicationName>>API` 使用 Identity 預設範圍的伺服器來註冊 API 資源 `<<ApplicationName>>API` ，並設定 JWT 持有人權杖中介軟體，以驗證 Identity 伺服器針對應用程式所發出的權杖。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-143">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="6b8d1-144">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="6b8d1-144">WeatherForecastController</span></span>

<span data-ttu-id="6b8d1-145">在 *Controllers\WeatherForecastController.cs* 檔案中，請注意套用 `[Authorize]` 至類別的屬性，指出使用者必須根據預設原則來存取資源，才能獲得授權。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-145">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="6b8d1-146">預設授權原則會設定為使用預設的驗證配置，此配置是由上述的原則配置所設定 `AddIdentityServerJwt` ，讓 `JwtBearerHandler` 這類 helper 方法設定為應用程式要求的預設處理常式。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-146">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="6b8d1-147">[ApplicationdbcoNtext</span><span class="sxs-lookup"><span data-stu-id="6b8d1-147">ApplicationDbContext</span></span>

<span data-ttu-id="6b8d1-148">在 *Data\ApplicationDbCoNtext.cs* 檔案中，請注意在 `DbContext` 中使用相同的， Identity 因為它會 `ApiAuthorizationDbContext` 從) 延伸 (更衍生的類別 `IdentityDbContext` ，以包含伺服器的架構 Identity 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-148">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="6b8d1-149">若要取得資料庫架構的完整控制權，請從其中一個可用類別繼承， Identity `DbContext` 並設定內容以包含 Identity 架構，方法是 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 在方法上呼叫 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-149">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="6b8d1-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="6b8d1-150">OidcConfigurationController</span></span>

<span data-ttu-id="6b8d1-151">在 *Controllers\OidcConfigurationController.cs* 檔案中，請注意布建來提供用戶端需要使用之 OIDC 參數的端點。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-151">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="6b8d1-152">在專案根目錄的檔案中 *appsettings.json* ，有一個新的 `IdentityServer` 區段描述已設定的用戶端清單。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-152">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="6b8d1-153">在下列範例中，有一個用戶端。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-153">In the following example, there's a single client.</span></span> <span data-ttu-id="6b8d1-154">用戶端名稱會對應到應用程式名稱，並依照慣例對應至 OAuth `ClientId` 參數。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="6b8d1-155">設定檔會指出正在設定的應用程式類型。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="6b8d1-156">它是在內部用來推動可簡化伺服器設定流程的慣例。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-156">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="6b8d1-157">有數個可用的設定檔，如 [應用程式佈建檔](#application-profiles) 一節中所述。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-157">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="6b8d1-158">appsettings.Development.js開啟</span><span class="sxs-lookup"><span data-stu-id="6b8d1-158">appsettings.Development.json</span></span>

<span data-ttu-id="6b8d1-159">在專案根目錄的 *appsettings.Development.js* 檔案中，有一個 `IdentityServer` 區段描述用來簽署權杖的金鑰。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-159">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="6b8d1-160">部署至生產環境時，必須連同應用程式一起布建和部署金鑰，如「 [部署至生產](#deploy-to-production) 」一節中所述。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-160">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="6b8d1-161">角度應用程式的一般描述</span><span class="sxs-lookup"><span data-stu-id="6b8d1-161">General description of the Angular app</span></span>

<span data-ttu-id="6b8d1-162">角度範本中的驗證和 API 授權支援位於其本身在 *ClientApp\src\api-authorization* 目錄中的角度模組。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-162">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="6b8d1-163">模組是由下列元素所組成：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-163">The module is composed of the following elements:</span></span>

* <span data-ttu-id="6b8d1-164">3個元件：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-164">3 components:</span></span>
  * <span data-ttu-id="6b8d1-165">*login*：處理應用程式的登入流程。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-165">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="6b8d1-166">*登出。 component*：處理應用程式的登出流程。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-166">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="6b8d1-167">*登入-功能表.* t：顯示下列其中一組連結的 widget：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-167">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="6b8d1-168">使用者設定檔管理和登出連結（當使用者通過驗證時）。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-168">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="6b8d1-169">當使用者未通過驗證時，註冊並登入連結。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-169">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="6b8d1-170">`AuthorizeGuard`可以新增至路由的路由防護，並且需要先驗證使用者，才能造訪路由。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-170">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="6b8d1-171">HTTP 攔截器 `AuthorizeInterceptor` ，會在使用者通過驗證時，將存取權杖附加至以 API 為目標的傳出 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-171">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="6b8d1-172">一種服務 `AuthorizeService` ，可處理驗證程式的較低層級詳細資料，並將已驗證使用者的相關資訊公開給其餘的應用程式以供取用。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-172">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="6b8d1-173">用來定義與應用程式驗證部分相關聯之路由的角度模組。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-173">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="6b8d1-174">它會公開登入功能表元件、攔截器、防護和服務，以從應用程式的其餘部分取用。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-174">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="6b8d1-175">回應應用程式的一般描述</span><span class="sxs-lookup"><span data-stu-id="6b8d1-175">General description of the React app</span></span>

<span data-ttu-id="6b8d1-176">回應範本中的驗證和 API 授權支援位於 *ClientApp\src\components\api-authorization* 目錄中。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-176">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="6b8d1-177">它是由下列元素所組成：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-177">It's composed of the following elements:</span></span>

* <span data-ttu-id="6b8d1-178">4個元件：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-178">4 components:</span></span>
  * <span data-ttu-id="6b8d1-179">*Login.js*：處理應用程式的登入流程。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-179">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="6b8d1-180">*Logout.js*：處理應用程式的登出流程。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-180">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="6b8d1-181">*LoginMenu.js*：顯示下列其中一組連結的 widget：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-181">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="6b8d1-182">使用者設定檔管理和登出連結（當使用者通過驗證時）。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-182">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="6b8d1-183">當使用者未通過驗證時，註冊並登入連結。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-183">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="6b8d1-184">*AuthorizeRoute.js*：需要先驗證使用者才能轉譯參數中所指出之元件的路由元件 `Component` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-184">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="6b8d1-185">已匯出 `authService` 之類別的實例 `AuthorizeService` ，可處理驗證進程的較低層級詳細資料，並將已驗證使用者的相關資訊公開給其餘的應用程式以供取用。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-185">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="6b8d1-186">現在您已瞭解解決方案的主要元件，您可以進一步瞭解應用程式的個別案例。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-186">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="6b8d1-187">在新的 API 上要求授權</span><span class="sxs-lookup"><span data-stu-id="6b8d1-187">Require authorization on a new API</span></span>

<span data-ttu-id="6b8d1-188">根據預設，系統會設定為輕鬆地要求新 Api 的授權。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-188">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="6b8d1-189">若要這樣做，請建立新的控制器，並將 `[Authorize]` 屬性新增至控制器類別或控制器內的任何動作。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-189">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="6b8d1-190">自訂 API 驗證處理常式</span><span class="sxs-lookup"><span data-stu-id="6b8d1-190">Customize the API authentication handler</span></span>

<span data-ttu-id="6b8d1-191">若要自訂 API 的 JWT 處理常式的設定，請設定其 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> 實例：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-191">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="6b8d1-192">API 的 JWT 處理常式會引發事件，讓您可以使用來控制驗證程式 `JwtBearerEvents` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-192">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="6b8d1-193">若要提供 API 授權支援，請 `AddIdentityServerJwt` 註冊其本身的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-193">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="6b8d1-194">若要自訂事件的處理，請視需要使用額外的邏輯來包裝現有的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-194">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="6b8d1-195">例如：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-195">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="6b8d1-196">在上述程式碼中， `OnTokenValidated` 事件處理常式會取代為自訂的執行。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-196">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="6b8d1-197">此實作為：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-197">This implementation:</span></span>

1. <span data-ttu-id="6b8d1-198">呼叫 API 授權支援所提供的原始實作為。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-198">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="6b8d1-199">執行自己的自訂邏輯。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-199">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="6b8d1-200">保護用戶端路由 (角度) </span><span class="sxs-lookup"><span data-stu-id="6b8d1-200">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="6b8d1-201">保護用戶端路由的方式是將授權防護新增至設定路由時要執行的防護清單。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-201">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="6b8d1-202">例如，您可以在 `fetch-data` 主要應用程式角度模組中查看路由的設定方式：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-202">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="6b8d1-203">請務必注意，保護路由並不會保護實際的端點 (但仍需要套用 `[Authorize]` 屬性) 但它只會防止使用者在未經過驗證時流覽至指定的用戶端路由。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-203">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="6b8d1-204"> (角) 驗證 API 要求</span><span class="sxs-lookup"><span data-stu-id="6b8d1-204">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="6b8d1-205">使用應用程式所定義的 HTTP 用戶端攔截器，對與應用程式一起裝載的 Api 進行驗證要求。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-205">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="6b8d1-206">保護用戶端路由 (回應) </span><span class="sxs-lookup"><span data-stu-id="6b8d1-206">Protect a client-side route (React)</span></span>

<span data-ttu-id="6b8d1-207">使用 `AuthorizeRoute` 元件而非純文字元件來保護用戶端路由 `Route` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-207">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="6b8d1-208">例如，請注意如何在 `fetch-data` 元件中設定路由 `App` ：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-208">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="6b8d1-209">保護路由：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-209">Protecting a route:</span></span>

* <span data-ttu-id="6b8d1-210">不會保護實際的端點 (但仍需要將 `[Authorize]` 屬性套用至該端點) 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-210">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="6b8d1-211">只有當使用者未經過驗證時，才會防止使用者流覽至指定的用戶端路由。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-211">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="6b8d1-212">驗證 API 要求 (回應) </span><span class="sxs-lookup"><span data-stu-id="6b8d1-212">Authenticate API requests (React)</span></span>

<span data-ttu-id="6b8d1-213">藉由先從匯入實例來驗證具有回應的要求 `authService` `AuthorizeService` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-213">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="6b8d1-214">從取出存取權杖 `authService` ，並附加至要求，如下所示。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-214">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="6b8d1-215">在回應元件中，這項工作通常是在 `componentDidMount` 生命週期方法中完成，或做為某些使用者互動的結果。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-215">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="6b8d1-216">將 authService 匯入您的元件</span><span class="sxs-lookup"><span data-stu-id="6b8d1-216">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="6b8d1-217">取得存取權杖並將其附加至回應</span><span class="sxs-lookup"><span data-stu-id="6b8d1-217">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="6b8d1-218">部署到生產</span><span class="sxs-lookup"><span data-stu-id="6b8d1-218">Deploy to production</span></span>

<span data-ttu-id="6b8d1-219">若要將應用程式部署至生產環境，必須布建下列資源：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-219">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="6b8d1-220">用來儲存 Identity 使用者帳戶和伺服器授與的資料庫 Identity 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-220">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="6b8d1-221">用於簽署權杖的生產憑證。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-221">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="6b8d1-222">此憑證沒有特定需求;它可以是自我簽署的憑證，或透過 CA 授權單位布建的憑證。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-222">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="6b8d1-223">您可以透過 PowerShell 或 OpenSSL 等標準工具來產生它。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-223">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="6b8d1-224">您可以將它安裝到目的電腦上的憑證存放區中，或部署為具有強式密碼的 *.pfx* 檔案。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-224">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="6b8d1-225">範例：部署至非 Azure web 主控提供者</span><span class="sxs-lookup"><span data-stu-id="6b8d1-225">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="6b8d1-226">在您的 web 主控面板中，建立或載入您的憑證。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-226">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="6b8d1-227">然後，在應用程式的檔案中 *appsettings.json* ，修改 `IdentityServer` 區段以包含金鑰詳細資料。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-227">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="6b8d1-228">例如：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-228">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="6b8d1-229">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-229">In the preceding example:</span></span>

* <span data-ttu-id="6b8d1-230">`StoreName` 代表用來儲存憑證的憑證存放區名稱。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-230">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="6b8d1-231">在此情況下，它會指向 web 主控存放區。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-231">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="6b8d1-232">`StoreLocation` 代表在此案例中從 (載入憑證的位置 `CurrentUser`) 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-232">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="6b8d1-233">`Name` 對應于憑證的辨別主體。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-233">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="6b8d1-234">範例：部署至 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6b8d1-234">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="6b8d1-235">本節說明如何使用儲存在憑證存放區中的憑證，將應用程式部署至 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-235">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="6b8d1-236">若要修改應用程式以從憑證存放區載入憑證，當您在稍後的步驟中設定 Azure 入口網站中的應用程式時，需要標準層服務方案或更高的版本。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-236">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="6b8d1-237">在應用程式的檔案中 *appsettings.json* ，修改 `IdentityServer` 區段以包含金鑰詳細資料：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-237">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="6b8d1-238">存放區名稱代表儲存憑證的憑證存放區名稱。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-238">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="6b8d1-239">在此情況下，它會指向個人使用者存放區。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-239">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="6b8d1-240">存放區位置代表從 (或) 載入憑證的 `CurrentUser` 位置 `LocalMachine` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-240">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="6b8d1-241">憑證上的名稱屬性與憑證的辨別主體相對應。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-241">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="6b8d1-242">若要部署至 Azure App Service，請遵循將 [應用程式部署至 Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)中的步驟，其中說明如何建立必要的 Azure 資源，並將應用程式部署至生產環境。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-242">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="6b8d1-243">遵循上述指示之後，應用程式會部署至 Azure，但還無法運作。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-243">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="6b8d1-244">應用程式所使用的憑證必須在 Azure 入口網站中設定。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-244">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="6b8d1-245">找出憑證的憑證指紋，並依照 [載入您的憑證](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)中所述的步驟進行。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-245">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="6b8d1-246">雖然這些步驟提及 SSL，但 Azure 入口網站中有一個 **私用憑證** 區段，您可以在其中上傳佈建的憑證以與應用程式搭配使用。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-246">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="6b8d1-247">在 Azure 入口網站中設定應用程式和應用程式的設定之後，請在入口網站中重新開機應用程式。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-247">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="6b8d1-248">其他設定選項</span><span class="sxs-lookup"><span data-stu-id="6b8d1-248">Other configuration options</span></span>

<span data-ttu-id="6b8d1-249">Identity使用一組慣例、預設值和增強功能來支援 API 授權，以簡化 spa 的體驗。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-249">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="6b8d1-250">不用說， Identity 如果 ASP.NET Core 的整合不涵蓋您的案例，伺服器的完整威力就可以在幕後使用。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-250">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="6b8d1-251">ASP.NET Core 支援著重于「第一方」應用程式，而所有應用程式都是由我們的組織建立和部署。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-251">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="6b8d1-252">因此，不會針對同意或同盟等專案提供支援。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-252">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="6b8d1-253">在這些情況下，請使用 Identity Server 並遵循其檔。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-253">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="6b8d1-254">應用程式佈建檔</span><span class="sxs-lookup"><span data-stu-id="6b8d1-254">Application profiles</span></span>

<span data-ttu-id="6b8d1-255">應用程式佈建檔是針對進一步定義其參數的應用程式預先定義的設定。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-255">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="6b8d1-256">目前支援下列設定檔：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-256">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="6b8d1-257">`IdentityServerSPA`：表示 Identity 以單一單位的形式裝載于伺服器的 SPA。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-257">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="6b8d1-258">`redirect_uri`預設為 `/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-258">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="6b8d1-259">`post_logout_redirect_uri`預設為 `/authentication/logout-callback` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-259">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="6b8d1-260">這組範圍包括 `openid` 、 `profile` 和針對應用程式中的 api 所定義的每個範圍。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="6b8d1-261">一組允許的 OIDC 回應類型為 `id_token token` 個別或個別 (`id_token` ， `token`) 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="6b8d1-262">允許的回應模式為 `fragment` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="6b8d1-263">`SPA`：代表未裝載于伺服器的 SPA Identity 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-263">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="6b8d1-264">這組範圍包括 `openid` 、 `profile` 和針對應用程式中的 api 所定義的每個範圍。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-264">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="6b8d1-265">一組允許的 OIDC 回應類型為 `id_token token` 個別或個別 (`id_token` ， `token`) 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-265">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="6b8d1-266">允許的回應模式為 `fragment` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-266">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="6b8d1-267">`IdentityServerJwt`：表示與伺服器一起主控的 API Identity 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-267">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="6b8d1-268">應用程式會設定為具有預設為應用程式名稱的單一範圍。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-268">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="6b8d1-269">`API`：代表未裝載在伺服器上的 API Identity 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-269">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="6b8d1-270">應用程式會設定為具有預設為應用程式名稱的單一範圍。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-270">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="6b8d1-271">透過 AppSettings 設定</span><span class="sxs-lookup"><span data-stu-id="6b8d1-271">Configuration through AppSettings</span></span>

<span data-ttu-id="6b8d1-272">將應用程式新增至或的清單，以透過設定系統設定這些應用程式 `Clients` `Resources` 。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-272">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="6b8d1-273">設定每個用戶端 `redirect_uri` 和 `post_logout_redirect_uri` 屬性，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-273">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="6b8d1-274">設定資源時，您可以設定資源的範圍，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6b8d1-274">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="6b8d1-275">透過程式碼設定</span><span class="sxs-lookup"><span data-stu-id="6b8d1-275">Configuration through code</span></span>

<span data-ttu-id="6b8d1-276">您也可以透過程式碼 `AddApiAuthorization` ，使用會採取動作來設定選項的多載，來設定用戶端和資源。</span><span class="sxs-lookup"><span data-stu-id="6b8d1-276">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="6b8d1-277">其他資源</span><span class="sxs-lookup"><span data-stu-id="6b8d1-277">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
