---
title: 使用伺服器保護託管的 ASP.NET Core Blazor WebAssembly 應用程式 Identity
author: guardrex
description: 瞭解如何使用伺服器保護託管的 ASP.NET Core Blazor WebAssembly 應用程式 Identity 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 80196945bc6891d5517d7da0e07ca1b0debddd28
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854677"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="e855f-103">Blazor WebAssembly使用伺服器保護 ASP.NET Core 託管應用 Identity 程式</span><span class="sxs-lookup"><span data-stu-id="e855f-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="e855f-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e855f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e855f-105">本文說明如何建立使用[ Identity 伺服器](https://identityserver.io/)來驗證使用者和 API 呼叫的[託管 Blazor WebAssembly 應用程式](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="e855f-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="e855f-106">若要將獨立或託管的 Blazor WebAssembly 應用程式設定為使用現有的外部 Identity 伺服器實例，請遵循中的指導方針 <xref:blazor/security/webassembly/standalone-with-authentication-library> 。</span><span class="sxs-lookup"><span data-stu-id="e855f-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e855f-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e855f-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e855f-108">若要建立 Blazor WebAssembly 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="e855f-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="e855f-109">在 [**建立新的 ASP.NET Core Web 應用程式**] 對話方塊中選擇 **Blazor WebAssembly 應用程式** 範本之後，請選取 [**驗證**] 下的 [**變更**]。</span><span class="sxs-lookup"><span data-stu-id="e855f-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="e855f-110">使用「**儲存使用者帳戶應用程式內**」選項選取 **個別使用者帳戶**，以使用 ASP.NET Core 的系統將使用者儲存在應用程式內 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="e855f-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="e855f-111">在 [ **Advanced** ] 區段中，選取 [裝載 **ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e855f-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="e855f-112">Visual Studio Code / .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e855f-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="e855f-113">若要 Blazor WebAssembly 在空的資料夾中建立具有驗證機制的新專案，請指定 `Individual` 驗證機制，並 `-au|--auth` 使用 ASP.NET Core 的系統將使用者儲存在應用程式中的選項 [Identity](xref:security/authentication/identity) ：</span><span class="sxs-lookup"><span data-stu-id="e855f-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="e855f-114">預留位置</span><span class="sxs-lookup"><span data-stu-id="e855f-114">Placeholder</span></span>  | <span data-ttu-id="e855f-115">範例</span><span class="sxs-lookup"><span data-stu-id="e855f-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="e855f-116">使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。</span><span class="sxs-lookup"><span data-stu-id="e855f-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="e855f-117">如需詳細資訊，請參閱 [`dotnet new`](/dotnet/core/tools/dotnet-new) .Net Core 指南中的命令。</span><span class="sxs-lookup"><span data-stu-id="e855f-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e855f-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e855f-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e855f-119">若要建立 Blazor WebAssembly 具有驗證機制的新專案：</span><span class="sxs-lookup"><span data-stu-id="e855f-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="e855f-120">在 [**設定新的 Blazor WebAssembly 應用程式**] 步驟中，從 [**驗證**] 下拉式清單中選取 [**應用程式內) 的個別驗證 (** 。</span><span class="sxs-lookup"><span data-stu-id="e855f-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="e855f-121">應用程式會針對使用 ASP.NET Core 儲存在應用程式中的個別使用者建立 [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="e855f-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="e855f-122">選取 [ **主控 ASP.NET Core** ] 核取方塊。</span><span class="sxs-lookup"><span data-stu-id="e855f-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="e855f-123">*`Server`* 應用程式設定</span><span class="sxs-lookup"><span data-stu-id="e855f-123">*`Server`* app configuration</span></span>

<span data-ttu-id="e855f-124">下列各節說明在包含驗證支援時，專案的新增專案。</span><span class="sxs-lookup"><span data-stu-id="e855f-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="e855f-125">啟始類別</span><span class="sxs-lookup"><span data-stu-id="e855f-125">Startup class</span></span>

<span data-ttu-id="e855f-126">`Startup`類別具有下列新增專案。</span><span class="sxs-lookup"><span data-stu-id="e855f-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="e855f-127">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="e855f-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="e855f-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="e855f-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="e855f-129">Identity具有其他 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper 方法的伺服器，可在伺服器上設定預設的 ASP.NET Core 慣例 Identity ：</span><span class="sxs-lookup"><span data-stu-id="e855f-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="e855f-130">使用額外的 helper 方法進行驗證，以設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 應用程式驗證服務器所產生的 JWT 權杖 Identity ：</span><span class="sxs-lookup"><span data-stu-id="e855f-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="e855f-131">在 `Startup.Configure` 中：</span><span class="sxs-lookup"><span data-stu-id="e855f-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="e855f-132">Identity伺服器中介軟體會公開 OpenID Connect (OIDC) 端點：</span><span class="sxs-lookup"><span data-stu-id="e855f-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="e855f-133">驗證中介軟體負責驗證要求的認證，並在要求內容上設定使用者：</span><span class="sxs-lookup"><span data-stu-id="e855f-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="e855f-134">授權中介軟體可啟用授權功能：</span><span class="sxs-lookup"><span data-stu-id="e855f-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="e855f-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="e855f-135">AddApiAuthorization</span></span>

<span data-ttu-id="e855f-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Helper 方法會設定 ASP.NET Core 案例的[ Identity 伺服器](https://identityserver.io/)。</span><span class="sxs-lookup"><span data-stu-id="e855f-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="e855f-137">Identity伺服器是一種功能強大且可擴充的架構，可處理應用程式安全性的考慮。</span><span class="sxs-lookup"><span data-stu-id="e855f-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="e855f-138">Identity針對最常見的情況，伺服器會公開不必要的複雜性。</span><span class="sxs-lookup"><span data-stu-id="e855f-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="e855f-139">因此，我們假設有一組慣例和設定選項，我們考慮的是很好的起點。</span><span class="sxs-lookup"><span data-stu-id="e855f-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="e855f-140">一旦您的驗證需要變更， Identity 就可以使用伺服器的完整功能來自訂驗證，以符合應用程式的需求。</span><span class="sxs-lookup"><span data-stu-id="e855f-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="e855f-141">新增 Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="e855f-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="e855f-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Helper 方法會設定應用程式的原則配置作為預設驗證處理常式。</span><span class="sxs-lookup"><span data-stu-id="e855f-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="e855f-143">原則設定為允許 Identity 處理所有路由至 URL 空間中之子路徑的要求 Identity `/Identity` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="e855f-144">會 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 處理所有其他要求。</span><span class="sxs-lookup"><span data-stu-id="e855f-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="e855f-145">此外，這個方法：</span><span class="sxs-lookup"><span data-stu-id="e855f-145">Additionally, this method:</span></span>

* <span data-ttu-id="e855f-146">註冊 `{APPLICATION NAME}API` 具有 Identity 預設範圍之伺服器的 API 資源 `{APPLICATION NAME}API` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="e855f-147">設定 JWT 持有人權杖中介軟體，以驗證 Identity 伺服器針對應用程式所發出的權杖。</span><span class="sxs-lookup"><span data-stu-id="e855f-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="e855f-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="e855f-148">WeatherForecastController</span></span>

<span data-ttu-id="e855f-149">在 `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) 中，會將屬性套用 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 至類別。</span><span class="sxs-lookup"><span data-stu-id="e855f-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="e855f-150">屬性（attribute）會指出使用者必須根據預設原則來取得存取資源的授權。</span><span class="sxs-lookup"><span data-stu-id="e855f-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="e855f-151">預設授權原則會設定為使用預設的驗證配置，此配置是由設定 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e855f-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="e855f-152">Helper 方法會將 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> 要求設定為應用程式要求的預設處理常式。</span><span class="sxs-lookup"><span data-stu-id="e855f-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="e855f-153">[ApplicationdbcoNtext</span><span class="sxs-lookup"><span data-stu-id="e855f-153">ApplicationDbContext</span></span>

<span data-ttu-id="e855f-154">在 `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) 中，會 <xref:Microsoft.EntityFrameworkCore.DbContext> 擴充 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 以包含伺服器的架構 Identity 。</span><span class="sxs-lookup"><span data-stu-id="e855f-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="e855f-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> 衍生自 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>。</span><span class="sxs-lookup"><span data-stu-id="e855f-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="e855f-156">若要取得資料庫架構的完整控制權，請從其中一個可用類別繼承， Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 並設定內容以包含 Identity 架構，方法是 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` 在方法中呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 。</span><span class="sxs-lookup"><span data-stu-id="e855f-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="e855f-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="e855f-157">OidcConfigurationController</span></span>

<span data-ttu-id="e855f-158">在 `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) 中，會布建用戶端端點以提供 OIDC 參數。</span><span class="sxs-lookup"><span data-stu-id="e855f-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="e855f-159">應用程式設定</span><span class="sxs-lookup"><span data-stu-id="e855f-159">App settings</span></span>

<span data-ttu-id="e855f-160">在應用程式佈建檔 (`appsettings.json`) 的專案根目錄中， `IdentityServer` 區段會描述已設定的用戶端清單。</span><span class="sxs-lookup"><span data-stu-id="e855f-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="e855f-161">在下列範例中，有一個用戶端。</span><span class="sxs-lookup"><span data-stu-id="e855f-161">In the following example, there's a single client.</span></span> <span data-ttu-id="e855f-162">用戶端名稱會對應到應用程式名稱，並依照慣例對應至 OAuth `ClientId` 參數。</span><span class="sxs-lookup"><span data-stu-id="e855f-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="e855f-163">設定檔會指出正在設定的應用程式類型。</span><span class="sxs-lookup"><span data-stu-id="e855f-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="e855f-164">設定檔會在內部使用，以促進可簡化伺服器設定程式的慣例。</span><span class="sxs-lookup"><span data-stu-id="e855f-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="e855f-165">預留位置 `{APP ASSEMBLY}` 是應用程式的元件名稱 (例如 `BlazorSample.Client`) 。</span><span class="sxs-lookup"><span data-stu-id="e855f-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="e855f-166">*`Client`* 應用程式設定</span><span class="sxs-lookup"><span data-stu-id="e855f-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e855f-167">驗證套件</span><span class="sxs-lookup"><span data-stu-id="e855f-167">Authentication package</span></span>

<span data-ttu-id="e855f-168">建立應用程式以使用 () 的個別使用者帳戶時 `Individual` ，應用程式會 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 在應用程式的專案檔中自動收到套件的套件參考。</span><span class="sxs-lookup"><span data-stu-id="e855f-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="e855f-169">此套件提供一組基本類型，可協助應用程式驗證使用者，並取得權杖以呼叫受保護的 Api。</span><span class="sxs-lookup"><span data-stu-id="e855f-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e855f-170">如果將驗證新增至應用程式，請手動將套件新增到應用程式的專案檔：</span><span class="sxs-lookup"><span data-stu-id="e855f-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="e855f-171">針對預留位置 `{VERSION}` ，可以在套件的 **版本歷程記錄** （ [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到符合應用程式共用架構版本的最新穩定版本套件。</span><span class="sxs-lookup"><span data-stu-id="e855f-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="e855f-172">`HttpClient` 配置</span><span class="sxs-lookup"><span data-stu-id="e855f-172">`HttpClient` configuration</span></span>

<span data-ttu-id="e855f-173">在 `Program.Main` (`Program.cs`) 中，會將名為 <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) 設定為在對 <xref:System.Net.Http.HttpClient> 伺服器 API 提出要求時，提供包含存取權杖的實例：</span><span class="sxs-lookup"><span data-stu-id="e855f-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="e855f-174">如果您要將 Blazor WebAssembly 應用程式設定為使用 Identity 不屬於託管解決方案的現有伺服器實例 Blazor ，請將 <xref:System.Net.Http.HttpClient> 基底位址註冊從 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> () 變更 `builder.HostEnvironment.BaseAddress` 為伺服器應用程式的 API 授權端點 URL。</span><span class="sxs-lookup"><span data-stu-id="e855f-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="e855f-175">API 授權支援</span><span class="sxs-lookup"><span data-stu-id="e855f-175">API authorization support</span></span>

<span data-ttu-id="e855f-176">驗證使用者的支援是由套件內提供的擴充方法插入服務容器 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 。</span><span class="sxs-lookup"><span data-stu-id="e855f-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="e855f-177">這個方法會設定應用程式所需的服務，以與現有的授權系統互動。</span><span class="sxs-lookup"><span data-stu-id="e855f-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="e855f-178">根據預設，應用程式的設定會依照慣例載入 `_configuration/{client-id}` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="e855f-179">依照慣例，用戶端識別碼會設定為應用程式的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="e855f-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="e855f-180">您可以使用選項來呼叫多載，以將此 URL 變更為指向不同的端點。</span><span class="sxs-lookup"><span data-stu-id="e855f-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="e855f-181">匯入檔案</span><span class="sxs-lookup"><span data-stu-id="e855f-181">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e855f-182">索引頁面</span><span class="sxs-lookup"><span data-stu-id="e855f-182">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="e855f-183">應用程式元件</span><span class="sxs-lookup"><span data-stu-id="e855f-183">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e855f-184">RedirectToLogin 元件</span><span class="sxs-lookup"><span data-stu-id="e855f-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e855f-185">LoginDisplay 元件</span><span class="sxs-lookup"><span data-stu-id="e855f-185">LoginDisplay component</span></span>

<span data-ttu-id="e855f-186">`LoginDisplay`元件 (`Shared/LoginDisplay.razor`) 會轉譯在 `MainLayout` 元件 (`Shared/MainLayout.razor`) 並管理下列行為：</span><span class="sxs-lookup"><span data-stu-id="e855f-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="e855f-187">針對已驗證的使用者：</span><span class="sxs-lookup"><span data-stu-id="e855f-187">For authenticated users:</span></span>
  * <span data-ttu-id="e855f-188">顯示目前的使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="e855f-188">Displays the current user name.</span></span>
  * <span data-ttu-id="e855f-189">提供中 [使用者設定檔] 頁面的連結 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="e855f-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="e855f-190">提供用來登出應用程式的按鈕。</span><span class="sxs-lookup"><span data-stu-id="e855f-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="e855f-191">匿名使用者：</span><span class="sxs-lookup"><span data-stu-id="e855f-191">For anonymous users:</span></span>
  * <span data-ttu-id="e855f-192">提供註冊的選項。</span><span class="sxs-lookup"><span data-stu-id="e855f-192">Offers the option to register.</span></span>
  * <span data-ttu-id="e855f-193">提供登入的選項。</span><span class="sxs-lookup"><span data-stu-id="e855f-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="e855f-194">驗證元件</span><span class="sxs-lookup"><span data-stu-id="e855f-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e855f-195">FetchData 元件</span><span class="sxs-lookup"><span data-stu-id="e855f-195">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e855f-196">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="e855f-196">Run the app</span></span>

<span data-ttu-id="e855f-197">從伺服器專案執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e855f-197">Run the app from the Server project.</span></span> <span data-ttu-id="e855f-198">使用 Visual Studio 時，您可以：</span><span class="sxs-lookup"><span data-stu-id="e855f-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="e855f-199">將工具列中的 [ **啟始專案** ] 下拉式清單設定為 *伺服器 API 應用程式* ，然後選取 [ **執行** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="e855f-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="e855f-200">在 **方案總管** 中選取伺服器專案，然後選取工具列中的 [ **執行** ] 按鈕，或從 [ **調試** 程式] 功能表啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="e855f-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="e855f-201">使用 API 授權的名稱和角色宣告</span><span class="sxs-lookup"><span data-stu-id="e855f-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="e855f-202">自訂使用者 factory</span><span class="sxs-lookup"><span data-stu-id="e855f-202">Custom user factory</span></span>

<span data-ttu-id="e855f-203">在 *`Client`* 應用程式中，建立自訂的使用者 factory。</span><span class="sxs-lookup"><span data-stu-id="e855f-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="e855f-204">Identity 伺服器會在單一宣告中以 JSON 陣列的形式傳送多個角色 `role` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="e855f-205">單一角色會以字串值的形式傳送到宣告中。</span><span class="sxs-lookup"><span data-stu-id="e855f-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="e855f-206">Factory 會 `role` 為每個使用者的角色建立個別宣告。</span><span class="sxs-lookup"><span data-stu-id="e855f-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="e855f-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="e855f-207">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="e855f-208">在 *`Client`* 應用程式中，在 () 中註冊 factory `Program.Main` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="e855f-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="e855f-209">在應用程式中，在產生器 *`Server`* <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> 上呼叫 Identity ，以新增角色相關服務：</span><span class="sxs-lookup"><span data-stu-id="e855f-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="e855f-210">設定 Identity 伺服器</span><span class="sxs-lookup"><span data-stu-id="e855f-210">Configure Identity Server</span></span>

<span data-ttu-id="e855f-211">使用下列 **其中一** 種方法：</span><span class="sxs-lookup"><span data-stu-id="e855f-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="e855f-212">API 授權選項</span><span class="sxs-lookup"><span data-stu-id="e855f-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="e855f-213">設定檔服務</span><span class="sxs-lookup"><span data-stu-id="e855f-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="e855f-214">API 授權選項</span><span class="sxs-lookup"><span data-stu-id="e855f-214">API authorization options</span></span>

<span data-ttu-id="e855f-215">在 *`Server`* 應用程式中：</span><span class="sxs-lookup"><span data-stu-id="e855f-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="e855f-216">設定 Identity 伺服器將 `name` 和宣告放 `role` 入識別碼權杖和存取權杖中。</span><span class="sxs-lookup"><span data-stu-id="e855f-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="e855f-217">防止 JWT 權杖處理常式中的角色預設對應。</span><span class="sxs-lookup"><span data-stu-id="e855f-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="e855f-218">設定檔服務</span><span class="sxs-lookup"><span data-stu-id="e855f-218">Profile Service</span></span>

<span data-ttu-id="e855f-219">在 *`Server`* 應用程式中，建立一個 `ProfileService` 執行。</span><span class="sxs-lookup"><span data-stu-id="e855f-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="e855f-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="e855f-220">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="e855f-221">在 *`Server`* 應用程式中，在中註冊設定檔服務 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e855f-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="e855f-222">使用授權機制</span><span class="sxs-lookup"><span data-stu-id="e855f-222">Use authorization mechanisms</span></span>

<span data-ttu-id="e855f-223">在 *`Client`* 應用程式中，元件授權方法此時可正常運作。</span><span class="sxs-lookup"><span data-stu-id="e855f-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="e855f-224">元件中的任何授權機制都可以使用角色來授權使用者：</span><span class="sxs-lookup"><span data-stu-id="e855f-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="e855f-225">[ `AuthorizeView` 元件](xref:blazor/security/index#authorizeview-component) (範例： `<AuthorizeView Roles="admin">`) </span><span class="sxs-lookup"><span data-stu-id="e855f-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="e855f-226">[ `[Authorize]` 屬性](xref:blazor/security/index#authorize-attribute)指示詞 (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)  (範例： `@attribute [Authorize(Roles = "admin")]`) </span><span class="sxs-lookup"><span data-stu-id="e855f-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="e855f-227">程式[邏輯](xref:blazor/security/index#procedural-logic) (範例： `if (user.IsInRole("admin")) { ... }`) </span><span class="sxs-lookup"><span data-stu-id="e855f-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="e855f-228">支援多個角色測試：</span><span class="sxs-lookup"><span data-stu-id="e855f-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="e855f-229">`User.Identity.Name` 會在 *`Client`* 應用程式中填入使用者的使用者名稱，通常是他們的登入電子郵件地址。</span><span class="sxs-lookup"><span data-stu-id="e855f-229">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="e855f-230">使用自訂網域 Azure App Service 中的主機</span><span class="sxs-lookup"><span data-stu-id="e855f-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="e855f-231">下列指導方針說明如何 Blazor WebAssembly 使用伺服器將託管應用程式部署 Identity 至與自訂網域 [Azure App Service](https://azure.microsoft.com/services/app-service/) 。</span><span class="sxs-lookup"><span data-stu-id="e855f-231">The following guidance explains how to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="e855f-232">在此裝載案例中，**請不要將** 相同的憑證用於 [ Identity 伺服器的權杖簽署金鑰](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation)和網站與瀏覽器的 HTTPS 安全通訊：</span><span class="sxs-lookup"><span data-stu-id="e855f-232">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="e855f-233">針對這兩個需求使用不同的憑證是很好的安全性作法，因為它會為每個用途隔離私密金鑰。</span><span class="sxs-lookup"><span data-stu-id="e855f-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="e855f-234">與瀏覽器通訊的 TLS 憑證會獨立管理，而不會影響 Identity 伺服器的權杖簽署。</span><span class="sxs-lookup"><span data-stu-id="e855f-234">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="e855f-235">當 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 提供憑證給自訂網域系結的 App Service 應用程式時， Identity 伺服器無法從 Azure Key Vault 取得權杖簽署的相同憑證。</span><span class="sxs-lookup"><span data-stu-id="e855f-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="e855f-236">雖然 Identity 可能會將伺服器設定為使用來自實體路徑的相同 TLS 憑證，但將安全性憑證放入原始檔控制是一種不 **佳的作法，因此在大部分情況下都應該避免**。</span><span class="sxs-lookup"><span data-stu-id="e855f-236">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="e855f-237">在下列指引中，只會在 Azure Key Vault 中建立自我簽署憑證，以用於 Identity 伺服器權杖簽署。</span><span class="sxs-lookup"><span data-stu-id="e855f-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="e855f-238">伺服器設定會透過 Identity 應用程式的 `My`  >  `CurrentUser` 憑證存放區使用金鑰保存庫憑證。</span><span class="sxs-lookup"><span data-stu-id="e855f-238">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="e855f-239">使用自訂網域的 HTTPS 流量所使用的其他憑證，會與伺服器簽署憑證分開建立和設定 Identity 。</span><span class="sxs-lookup"><span data-stu-id="e855f-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="e855f-240">若要使用自訂網域和 HTTPS 來設定應用程式、Azure App Service 和 Azure Key Vault 主機：</span><span class="sxs-lookup"><span data-stu-id="e855f-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="e855f-241">使用或更高的方案層級建立 [App Service 計畫](/azure/app-service/overview-hosting-plans) `Basic B1` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="e855f-242">App Service 需要 `Basic B1` 或更高的服務層級才能使用自訂網域。</span><span class="sxs-lookup"><span data-stu-id="e855f-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="e855f-243">為網站的安全瀏覽器通訊建立 PFX 憑證 (HTTPS 通訊協定) ，並以網站的完整功能變數名稱 (FQDN) （例如 (）的一般名稱來建立 PFX 憑證 `www.contoso.com` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="e855f-244">使用下列內容建立憑證：</span><span class="sxs-lookup"><span data-stu-id="e855f-244">Create the certificate with:</span></span>
   * <span data-ttu-id="e855f-245">金鑰用途</span><span class="sxs-lookup"><span data-stu-id="e855f-245">Key uses</span></span>
     * <span data-ttu-id="e855f-246">數位簽章驗證 (`digitalSignature`) </span><span class="sxs-lookup"><span data-stu-id="e855f-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="e855f-247"> () 的金鑰加密 `keyEncipherment`</span><span class="sxs-lookup"><span data-stu-id="e855f-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="e855f-248">增強/擴充金鑰使用</span><span class="sxs-lookup"><span data-stu-id="e855f-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="e855f-249">用戶端驗證 (1.3.6.1.5.5.7.3.2) </span><span class="sxs-lookup"><span data-stu-id="e855f-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="e855f-250">伺服器驗證 (1.3.6.1.5.5.7.3.1) </span><span class="sxs-lookup"><span data-stu-id="e855f-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="e855f-251">若要建立憑證，請使用下列其中一種方法或任何其他適合的工具或線上服務：</span><span class="sxs-lookup"><span data-stu-id="e855f-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="e855f-252">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e855f-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="e855f-253">Windows 上的 MakeCert</span><span class="sxs-lookup"><span data-stu-id="e855f-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="e855f-254">Openssl</span><span class="sxs-lookup"><span data-stu-id="e855f-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="e855f-255">記下密碼，稍後用來將憑證匯入 Azure Key Vault。</span><span class="sxs-lookup"><span data-stu-id="e855f-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="e855f-256">如需 Azure Key Vault 憑證的詳細資訊，請參閱 [Azure Key Vault：憑證](/azure/key-vault/certificates/)。</span><span class="sxs-lookup"><span data-stu-id="e855f-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="e855f-257">建立新的 Azure Key Vault，或在您的 Azure 訂用帳戶中使用現有的金鑰保存庫。</span><span class="sxs-lookup"><span data-stu-id="e855f-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="e855f-258">在金鑰保存庫的 [ **憑證** ] 區域中，匯入 PFX 網站憑證。</span><span class="sxs-lookup"><span data-stu-id="e855f-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="e855f-259">記錄憑證的憑證指紋，稍後會在應用程式的設定中使用。</span><span class="sxs-lookup"><span data-stu-id="e855f-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="e855f-260">在 Azure Key Vault 中，為伺服器權杖簽署產生新的自我簽署憑證 Identity 。</span><span class="sxs-lookup"><span data-stu-id="e855f-260">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="e855f-261">提供憑證的 **憑證名稱** 和 **主體**。</span><span class="sxs-lookup"><span data-stu-id="e855f-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="e855f-262">**主體** 會指定為 `CN={COMMON NAME}` ，其中 `{COMMON NAME}` 預留位置是憑證的一般名稱。</span><span class="sxs-lookup"><span data-stu-id="e855f-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="e855f-263">一般名稱可以是任何英數位元字串。</span><span class="sxs-lookup"><span data-stu-id="e855f-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="e855f-264">例如， `CN=IdentityServerSigning` 是有效的憑證 **主體**。</span><span class="sxs-lookup"><span data-stu-id="e855f-264">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="e855f-265">使用預設的 [ **Advanced Policy Configuration** ] 設定。</span><span class="sxs-lookup"><span data-stu-id="e855f-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="e855f-266">記錄憑證的憑證指紋，稍後會在應用程式的設定中使用。</span><span class="sxs-lookup"><span data-stu-id="e855f-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="e855f-267">流覽至 Azure 入口網站中的 Azure App Service，並使用下列設定建立新的 App Service：</span><span class="sxs-lookup"><span data-stu-id="e855f-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="e855f-268">**發行** 設定為 `Code` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="e855f-269">**執行時間堆疊** 設定為應用程式的執行時間。</span><span class="sxs-lookup"><span data-stu-id="e855f-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="e855f-270">針對 **Sku 和大小**，確認 App Service 層是 `Basic B1` 或更高。</span><span class="sxs-lookup"><span data-stu-id="e855f-270">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="e855f-271">App Service 需要 `Basic B1` 或更高的服務層級才能使用自訂網域。</span><span class="sxs-lookup"><span data-stu-id="e855f-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="e855f-272">在 Azure 建立 App Service 之後，開啟應用程式的設定 **，並新增** 指定先前記錄之憑證指紋的新應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="e855f-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="e855f-273">應用程式設定機碼為 `WEBSITE_LOAD_CERTIFICATES` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="e855f-274">以逗號分隔應用程式設定值中的憑證指紋，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="e855f-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="e855f-275">索引鍵︰`WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="e855f-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="e855f-276">值：`57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="e855f-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="e855f-277">在 Azure 入口網站中，儲存應用程式設定有兩個步驟：儲存機 `WEBSITE_LOAD_CERTIFICATES` 碼-值設定，然後選取分頁頂端的 [ **儲存** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="e855f-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="e855f-278">選取應用程式的 **TLS/SSL 設定**。</span><span class="sxs-lookup"><span data-stu-id="e855f-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="e855f-279">選取 **私密金鑰憑證 ( .pfx)**。</span><span class="sxs-lookup"><span data-stu-id="e855f-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="e855f-280">使用匯 **入 Key Vault 憑證** 程式兩次，以匯入網站憑證以進行 HTTPS 通訊和網站的自我簽署 Identity 伺服器權杖簽署憑證。</span><span class="sxs-lookup"><span data-stu-id="e855f-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="e855f-281">流覽至 [ **自訂網域** ] 分頁。</span><span class="sxs-lookup"><span data-stu-id="e855f-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="e855f-282">在網域註冊機構的網站上，使用 **IP 位址** 和 **自訂網域驗證識別碼** 來設定網域。</span><span class="sxs-lookup"><span data-stu-id="e855f-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="e855f-283">一般的網域設定包括：</span><span class="sxs-lookup"><span data-stu-id="e855f-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="e855f-284">具有 **主機** 的 **a 記錄** `@` ，以及來自 Azure 入口網站之 IP 位址的值。</span><span class="sxs-lookup"><span data-stu-id="e855f-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="e855f-285">具有 **主機** 的 **TXT 記錄** `asuid` ，以及由 Azure 產生並由 Azure 入口網站提供的驗證識別碼值。</span><span class="sxs-lookup"><span data-stu-id="e855f-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="e855f-286">請確定您已正確地在網域註冊機構的網站上儲存變更。</span><span class="sxs-lookup"><span data-stu-id="e855f-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="e855f-287">某些註冊機構網站需要兩個步驟的程式來儲存網域記錄：一或多筆記錄會個別儲存，然後以個別的按鈕更新網域的註冊。</span><span class="sxs-lookup"><span data-stu-id="e855f-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="e855f-288">返回 Azure 入口網站中的 [ **自訂網域** ] 分頁。</span><span class="sxs-lookup"><span data-stu-id="e855f-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="e855f-289">選取 [新增自訂網域]。</span><span class="sxs-lookup"><span data-stu-id="e855f-289">Select **Add custom domain**.</span></span> <span data-ttu-id="e855f-290">選取 [ **記錄** ] 選項。</span><span class="sxs-lookup"><span data-stu-id="e855f-290">Select the **A Record** option.</span></span> <span data-ttu-id="e855f-291">提供網域，然後選取 [ **驗證**]。</span><span class="sxs-lookup"><span data-stu-id="e855f-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="e855f-292">如果網域記錄正確並傳播到網際網路，入口網站可讓您選取 [ **新增自訂網域** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="e855f-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="e855f-293">網域註冊變更可能需要幾天的時間，才能在您的網域註冊機構處理 (DNS) 的網際網路功能變數名稱伺服器。</span><span class="sxs-lookup"><span data-stu-id="e855f-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="e855f-294">如果未在三個工作天內更新網域記錄，請確認已使用網域註冊機構正確設定記錄，並與客戶支援人員聯繫。</span><span class="sxs-lookup"><span data-stu-id="e855f-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="e855f-295">在 [ **自訂網域** ] 分頁中，會標示網域的 **SSL 狀態** `Not Secure` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="e855f-296">選取 [ **新增** 系結] 連結。</span><span class="sxs-lookup"><span data-stu-id="e855f-296">Select the **Add binding** link.</span></span> <span data-ttu-id="e855f-297">從自訂網域系結的金鑰保存庫中，選取網站 HTTPS 憑證。</span><span class="sxs-lookup"><span data-stu-id="e855f-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="e855f-298">在 Visual Studio 中，開啟 *伺服器* 專案的應用程式佈建檔 (`appsettings.json` 或 `appsettings.Production.json`) 。</span><span class="sxs-lookup"><span data-stu-id="e855f-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="e855f-299">在 [伺服器設定] 中 Identity ，新增下列 `Key` 區段。</span><span class="sxs-lookup"><span data-stu-id="e855f-299">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="e855f-300">指定金鑰的自我簽署憑證 **主體** `Name` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="e855f-301">在下列範例中，在金鑰保存庫中指派的憑證一般名稱是 `IdentityServerSigning` ，它會產生的主體 `CN=IdentityServerSigning` ：</span><span class="sxs-lookup"><span data-stu-id="e855f-301">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="e855f-302">在 Visual Studio 中，建立 *伺服器* 專案的 Azure App Service [發行設定檔](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="e855f-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="e855f-303">從功能表列中，選取： **Build**  >  **發佈**  >  **新** 的  >  **Azure**  >  **Azure App Service** (Windows 或 Linux) 。</span><span class="sxs-lookup"><span data-stu-id="e855f-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="e855f-304">當 Visual Studio 連接至 Azure 訂用帳戶時，您可以依 **資源類型** 設定 azure 資源的 **觀點**。</span><span class="sxs-lookup"><span data-stu-id="e855f-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="e855f-305">在 **Web 應用程式** 清單中流覽，以找出應用程式的 App Service，然後選取它。</span><span class="sxs-lookup"><span data-stu-id="e855f-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="e855f-306">選取 [完成]。</span><span class="sxs-lookup"><span data-stu-id="e855f-306">Select **Finish**.</span></span>
1. <span data-ttu-id="e855f-307">當 Visual Studio 返回 [ **發佈** ] 視窗時，系統會自動偵測金鑰保存庫和 SQL Server 資料庫服務相依性。</span><span class="sxs-lookup"><span data-stu-id="e855f-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="e855f-308">Key vault 服務不需要對預設設定進行任何設定變更。</span><span class="sxs-lookup"><span data-stu-id="e855f-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="e855f-309">基於測試目的，由範本預設設定的應用程式本機 [SQLite](https://www.sqlite.org/index.html) 資料庫， Blazor 可以與應用程式一起部署，而不需要額外設定。</span><span class="sxs-lookup"><span data-stu-id="e855f-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="e855f-310">Identity在生產環境中為伺服器設定不同的資料庫已超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="e855f-310">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="e855f-311">如需詳細資訊，請參閱下列檔集中的資料庫資源：</span><span class="sxs-lookup"><span data-stu-id="e855f-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="e855f-312">App Service</span><span class="sxs-lookup"><span data-stu-id="e855f-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="e855f-313">Identity 伺服器</span><span class="sxs-lookup"><span data-stu-id="e855f-313">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="e855f-314">在視窗頂端的 [部署設定檔名稱] 底下，選取 [ **編輯** ] 連結。</span><span class="sxs-lookup"><span data-stu-id="e855f-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="e855f-315">將目的地 URL 變更為網站的自訂網域 URL (例如 `https://www.contoso.com`) 。</span><span class="sxs-lookup"><span data-stu-id="e855f-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="e855f-316">儲存設定。</span><span class="sxs-lookup"><span data-stu-id="e855f-316">Save the settings.</span></span>
1. <span data-ttu-id="e855f-317">發行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e855f-317">Publish the app.</span></span> <span data-ttu-id="e855f-318">Visual Studio 會開啟瀏覽器視窗，並在其自訂網域要求網站。</span><span class="sxs-lookup"><span data-stu-id="e855f-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="e855f-319">Azure 檔包含在 App Service 中使用 Azure 服務和自訂網域搭配 TLS 系結的其他詳細資料，包括使用 CNAME 記錄而非記錄的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e855f-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="e855f-320">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="e855f-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="e855f-321">App Service 文件</span><span class="sxs-lookup"><span data-stu-id="e855f-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="e855f-322">教學課程：將現有的自訂 DNS 名稱對應至 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e855f-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="e855f-323">在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱</span><span class="sxs-lookup"><span data-stu-id="e855f-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="e855f-324">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e855f-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="e855f-325">建議您在變更應用程式、應用程式設定或 Azure 入口網站中的 Azure 服務之後，針對每個應用程式測試回合使用新的私用或 incognito 瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="e855f-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="e855f-326">先前測試回合的延遲 cookie 時間可能會導致在測試網站時驗證或授權失敗，即使網站的設定正確也一樣。</span><span class="sxs-lookup"><span data-stu-id="e855f-326">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="e855f-327">如需如何設定 Visual Studio 以針對每個測試回合開啟新的私用或 incognito 瀏覽器視窗的詳細資訊，請參閱[ Cookie s 和網站資料](#cookies-and-site-data)一節。</span><span class="sxs-lookup"><span data-stu-id="e855f-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="e855f-328">當 Azure 入口網站中的 App Service 設定變更時，更新通常會快速生效但不會立即生效。</span><span class="sxs-lookup"><span data-stu-id="e855f-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="e855f-329">有時候，您必須等候一小段時間，才能讓 App Service 重新開機，設定變更才會生效。</span><span class="sxs-lookup"><span data-stu-id="e855f-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="e855f-330">如果針對憑證載入問題進行疑難排解，請在 Azure 入口網站 [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell 命令 shell 中執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="e855f-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="e855f-331">此命令會提供應用程式可從憑證存放區存取的憑證清單 `My`  >  `CurrentUser` 。</span><span class="sxs-lookup"><span data-stu-id="e855f-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="e855f-332">輸出包含在對應用程式進行偵錯工具時有用的憑證主體和指紋：</span><span class="sxs-lookup"><span data-stu-id="e855f-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e855f-333">其他資源</span><span class="sxs-lookup"><span data-stu-id="e855f-333">Additional resources</span></span>

* [<span data-ttu-id="e855f-334">部署至 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e855f-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="e855f-335">從 Key Vault (Azure 檔匯入憑證) </span><span class="sxs-lookup"><span data-stu-id="e855f-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="e855f-336">具有安全預設用戶端之應用程式中未經驗證或未經授權的 web API 要求</span><span class="sxs-lookup"><span data-stu-id="e855f-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
