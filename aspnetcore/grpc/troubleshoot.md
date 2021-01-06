---
title: 針對 .NET Core 上的 gRPC 進行疑難排解
author: jamesnk
description: 針對在 .NET Core 上使用 gRPC 時發生的錯誤進行疑難排解。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: cbce85caf7ba792253ba62c6be084c8905acd00f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058710"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="895f5-103">針對 .NET Core 上的 gRPC 進行疑難排解</span><span class="sxs-lookup"><span data-stu-id="895f5-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="895f5-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="895f5-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="895f5-105">本檔討論在 .NET 上開發 gRPC 應用程式時常見的問題。</span><span class="sxs-lookup"><span data-stu-id="895f5-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="895f5-106">用戶端與服務 SSL/TLS 設定不符</span><span class="sxs-lookup"><span data-stu-id="895f5-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="895f5-107">GRPC 範本和範例預設會使用 [傳輸層安全性 (TLS) ](https://tools.ietf.org/html/rfc5246) ，以保護 gRPC 服務的安全。</span><span class="sxs-lookup"><span data-stu-id="895f5-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="895f5-108">gRPC 用戶端必須使用安全連線，才能成功呼叫安全的 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="895f5-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="895f5-109">您可以在應用程式啟動時，確認 ASP.NET Core gRPC 服務是否正在使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="895f5-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="895f5-110">服務將接聽 HTTPS 端點：</span><span class="sxs-lookup"><span data-stu-id="895f5-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="895f5-111">.NET Core 用戶端必須 `https` 在伺服器位址中使用，才能使用安全的連接進行呼叫：</span><span class="sxs-lookup"><span data-stu-id="895f5-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="895f5-112">所有 gRPC 用戶端都支援 TLS。</span><span class="sxs-lookup"><span data-stu-id="895f5-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="895f5-113">從其他語言 gRPC 用戶端通常需要使用設定的通道 `SslCredentials` 。</span><span class="sxs-lookup"><span data-stu-id="895f5-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="895f5-114">`SslCredentials` 指定用戶端將使用的憑證，而且必須使用它，而不是不安全的認證。</span><span class="sxs-lookup"><span data-stu-id="895f5-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="895f5-115">如需將不同的 gRPC 用戶端執行設定為使用 TLS 的範例，請參閱 [GRPC Authentication](https://www.grpc.io/docs/guides/auth/)。</span><span class="sxs-lookup"><span data-stu-id="895f5-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="895f5-116">使用未受信任/不正確憑證呼叫 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="895f5-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="895f5-117">.NET gRPC 用戶端要求服務必須有受信任的憑證。</span><span class="sxs-lookup"><span data-stu-id="895f5-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="895f5-118">呼叫沒有受信任憑證的 gRPC 服務時，會傳回下列錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="895f5-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="895f5-119">未處理的例外狀況。</span><span class="sxs-lookup"><span data-stu-id="895f5-119">Unhandled exception.</span></span> <span data-ttu-id="895f5-120">System.net.HTTP.HTTPrequestexception：無法建立 SSL 連線，請參閱內部例外狀況。</span><span class="sxs-lookup"><span data-stu-id="895f5-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="895f5-121">---> System.Security.Authentication.AuthenticationException: 根據驗證程序，遠端憑證是無效的。</span><span class="sxs-lookup"><span data-stu-id="895f5-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="895f5-122">如果您要在本機測試您的應用程式，且 ASP.NET Core HTTPS 開發憑證不受信任，您可能會看到此錯誤。</span><span class="sxs-lookup"><span data-stu-id="895f5-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="895f5-123">如需修正此問題的指示，請參閱[信任 Windows 和 macOS上的 ASP.NET Core HTTPS 開發憑證](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。</span><span class="sxs-lookup"><span data-stu-id="895f5-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="895f5-124">如果您在另一部電腦上呼叫 gRPC 服務，但無法信任該憑證，則可以設定 gRPC 用戶端忽略不正確憑證。</span><span class="sxs-lookup"><span data-stu-id="895f5-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="895f5-125">下列程式碼會使用 [HttpClientHandler ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) 來允許沒有受信任憑證的呼叫：</span><span class="sxs-lookup"><span data-stu-id="895f5-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="895f5-126">未受信任的憑證應該只在應用程式開發期間使用。</span><span class="sxs-lookup"><span data-stu-id="895f5-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="895f5-127">生產環境應用程式應該一律使用有效的憑證。</span><span class="sxs-lookup"><span data-stu-id="895f5-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="895f5-128">使用 .NET Core 用戶端呼叫不安全的 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="895f5-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="895f5-129">當應用程式使用 .NET Core 3.x 時，需要進行其他設定，才能使用 .NET Core 用戶端呼叫不安全的 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="895f5-129">When an app is using .NET Core 3.x, additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="895f5-130">GRPC 用戶端必須將 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 參數設定為 `true` ，並 `http` 在伺服器位址中使用：</span><span class="sxs-lookup"><span data-stu-id="895f5-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="895f5-131">.NET 5 應用程式不需要額外的設定，但若要呼叫不安全的 gRPC 服務，則必須使用 `Grpc.Net.Client` 版本2.32.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="895f5-131">.NET 5 apps don't need additional configuration, but to call insecure gRPC services they must use `Grpc.Net.Client` version 2.32.0 or later.</span></span>

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="895f5-132">無法在 macOS 上啟動 ASP.NET Core gRPC 應用程式</span><span class="sxs-lookup"><span data-stu-id="895f5-132">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="895f5-133">Kestrel 在 macOS 和舊版 Windows （例如 Windows 7）上不支援具有 TLS 的 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="895f5-133">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="895f5-134">ASP.NET Core 的 gRPC 範本和範例預設會使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="895f5-134">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="895f5-135">當您嘗試啟動 gRPC 伺服器時，將會看到下列錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="895f5-135">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="895f5-136">無法 https://localhost:5001 在 IPv4 回送介面上系結至： macOS 因為缺少 ALPN 支援，所以不支援透過 TLS 的 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="895f5-136">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="895f5-137">若要解決此問題，請將 Kestrel 和 gRPC 用戶端設定為使用 *沒有* TLS 的 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="895f5-137">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="895f5-138">您應該只在開發期間執行此動作。</span><span class="sxs-lookup"><span data-stu-id="895f5-138">You should only do this during development.</span></span> <span data-ttu-id="895f5-139">不使用 TLS 會導致傳送 gRPC 的訊息，而不加密。</span><span class="sxs-lookup"><span data-stu-id="895f5-139">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="895f5-140">Kestrel 必須在 *Program.cs* 中設定不含 TLS 的 HTTP/2 端點：</span><span class="sxs-lookup"><span data-stu-id="895f5-140">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="895f5-141">當 HTTP/2 端點設定為沒有 TLS 時，端點的 [>listenoptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 必須設定為 `HttpProtocols.Http2` 。</span><span class="sxs-lookup"><span data-stu-id="895f5-141">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="895f5-142">`HttpProtocols.Http1AndHttp2` 因為必須有 TLS 才能協商 HTTP/2，所以無法使用。</span><span class="sxs-lookup"><span data-stu-id="895f5-142">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="895f5-143">如果沒有 TLS，端點的所有連接都會預設為 HTTP/1.1，而 gRPC 呼叫會失敗。</span><span class="sxs-lookup"><span data-stu-id="895f5-143">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="895f5-144">GRPC 用戶端也必須設定為不使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="895f5-144">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="895f5-145">如需詳細資訊，請參閱 [使用 .Net Core 用戶端呼叫不安全的 gRPC 服務](#call-insecure-grpc-services-with-net-core-client)。</span><span class="sxs-lookup"><span data-stu-id="895f5-145">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="895f5-146">只有在應用程式開發期間，才應該使用沒有 TLS 的 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="895f5-146">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="895f5-147">生產環境應用程式應該一律使用傳輸安全性。</span><span class="sxs-lookup"><span data-stu-id="895f5-147">Production apps should always use transport security.</span></span> <span data-ttu-id="895f5-148">如需詳細資訊，請參閱 [gRPC 中的安全性考慮，以瞭解 ASP.NET Core](xref:grpc/security#transport-security)。</span><span class="sxs-lookup"><span data-stu-id="895f5-148">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="895f5-149">gRPC c # 資產不是從 proto 檔案產生的程式碼</span><span class="sxs-lookup"><span data-stu-id="895f5-149">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="895f5-150">實體用戶端和服務基類的 gRPC 程式碼產生需要從專案參考 protobuf 檔案和工具。</span><span class="sxs-lookup"><span data-stu-id="895f5-150">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="895f5-151">您必須包含：</span><span class="sxs-lookup"><span data-stu-id="895f5-151">You must include:</span></span>

* <span data-ttu-id="895f5-152">您要在專案群組中使用的 *proto* 檔。 `<Protobuf>`</span><span class="sxs-lookup"><span data-stu-id="895f5-152">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="895f5-153">專案必須參考匯 [入的 *. proto*](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)檔案。</span><span class="sxs-lookup"><span data-stu-id="895f5-153">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="895f5-154">GRPC 工具套件 [gRPC](https://www.nuget.org/packages/Grpc.Tools/)的套件參考。</span><span class="sxs-lookup"><span data-stu-id="895f5-154">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="895f5-155">如需產生 gRPC c # 資產的詳細資訊，請參閱 <xref:grpc/basics> 。</span><span class="sxs-lookup"><span data-stu-id="895f5-155">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="895f5-156">裝載 gRPC 服務的 ASP.NET Core web 應用程式只需要產生的服務基類：</span><span class="sxs-lookup"><span data-stu-id="895f5-156">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="895f5-157">發出 gRPC 呼叫的 gRPC 用戶端應用程式只需要產生具體用戶端：</span><span class="sxs-lookup"><span data-stu-id="895f5-157">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="895f5-158">WPF 專案無法從 proto 檔案產生 gRPC c # 資產</span><span class="sxs-lookup"><span data-stu-id="895f5-158">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="895f5-159">WPF 專案有 [已知的問題](https://github.com/dotnet/wpf/issues/810) ，導致 gRPC 程式碼產生無法正常運作。</span><span class="sxs-lookup"><span data-stu-id="895f5-159">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="895f5-160">藉由參考和 proto 檔案，在 WPF 專案中產生的任何 gRPC 類型 `Grpc.Tools` 都會在使用時建立編譯錯誤： </span><span class="sxs-lookup"><span data-stu-id="895f5-160">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="895f5-161">錯誤 CS0246：找不到類型或命名空間名稱 ' MyGrpcServices ' (您是否遺漏 using 指示詞或元件參考？ ) </span><span class="sxs-lookup"><span data-stu-id="895f5-161">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="895f5-162">您可以透過下列方式解決此問題：</span><span class="sxs-lookup"><span data-stu-id="895f5-162">You can workaround this issue by:</span></span>

1. <span data-ttu-id="895f5-163">建立新的 .NET Core 類別庫專案。</span><span class="sxs-lookup"><span data-stu-id="895f5-163">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="895f5-164">在新的專案中，新增參考以 [從 *\* proto* 檔案啟用 c # 程式碼產生](xref:grpc/basics#generated-c-assets)：</span><span class="sxs-lookup"><span data-stu-id="895f5-164">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="895f5-165">將套件參考新增至 [Grpc 工具](https://www.nuget.org/packages/Grpc.Tools/) 套件。</span><span class="sxs-lookup"><span data-stu-id="895f5-165">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="895f5-166">將 *\* proto* 檔新增至 `<Protobuf>` 專案群組。</span><span class="sxs-lookup"><span data-stu-id="895f5-166">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="895f5-167">在 WPF 應用程式中，加入新專案的參考。</span><span class="sxs-lookup"><span data-stu-id="895f5-167">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="895f5-168">WPF 應用程式可以從新的類別庫專案使用 gRPC 產生的類型。</span><span class="sxs-lookup"><span data-stu-id="895f5-168">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
