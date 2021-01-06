---
title: 在 .NET 上 gRPC 中的記錄和診斷
author: jamesnk
description: 瞭解如何從 .NET 上的 gRPC 應用程式收集診斷資訊。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
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
uid: grpc/diagnostics
ms.openlocfilehash: 1f25ae76e5a480e5e6f247e4ac78d06dd4e778e9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060439"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="3ba4b-103">在 .NET 上 gRPC 中的記錄和診斷</span><span class="sxs-lookup"><span data-stu-id="3ba4b-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="3ba4b-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3ba4b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="3ba4b-105">本文提供從 gRPC 應用程式收集診斷資訊，以協助疑難排解問題的指引。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="3ba4b-106">涵蓋的主題包括：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-106">Topics covered include:</span></span>

* <span data-ttu-id="3ba4b-107">**記錄** -寫入 [.net Core 記錄](xref:fundamentals/logging/index)的結構化記錄。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3ba4b-108"><xref:Microsoft.Extensions.Logging.ILogger> 應用程式架構會使用它來寫入記錄，以及讓使用者在應用程式中進行自己的記錄。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="3ba4b-109">**追蹤** -與使用和撰寫之作業相關的事件 `DiaganosticSource` `Activity` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="3ba4b-110">診斷來源的追蹤通常用來依程式庫（例如 [Application Insights](/azure/azure-monitor/app/asp-net-core) 和 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)）收集應用程式遙測。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="3ba4b-111">**計量** -依時間間隔表示的資料量值，例如每秒要求數。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="3ba4b-112">計量是使用發出的 `EventCounter` ，可使用 [dotnet 計數器](/dotnet/core/diagnostics/dotnet-counters) 命令列工具或 [Application Insights](/azure/azure-monitor/app/eventcounters)來觀察。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="3ba4b-113">記錄</span><span class="sxs-lookup"><span data-stu-id="3ba4b-113">Logging</span></span>

<span data-ttu-id="3ba4b-114">gRPC services 和 gRPC 用戶端會使用 [.Net Core 記錄](xref:fundamentals/logging/index)來寫入記錄。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3ba4b-115">當您需要在應用程式中偵測非預期的行為時，記錄是很好的開始位置。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="3ba4b-116">gRPC services 記錄</span><span class="sxs-lookup"><span data-stu-id="3ba4b-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="3ba4b-117">伺服器端記錄檔可能包含來自您應用程式的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3ba4b-118">**切勿** 將未經處理的記錄從生產環境應用程式張貼到 GitHub 之類的公用論壇。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3ba4b-119">由於 gRPC 服務是裝載在 ASP.NET Core 上，因此會使用 ASP.NET Core 記錄系統。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="3ba4b-120">在預設設定中，gRPC 會記錄極少的資訊，但這可以設定。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="3ba4b-121">如需有關設定 ASP.NET Core 記錄的詳細資訊，請參閱 [ASP.NET Core 記錄](xref:fundamentals/logging/index#configuration) 檔。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="3ba4b-122">gRPC 會在類別下新增記錄 `Grpc` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="3ba4b-123">若要啟用 gRPC 的詳細記錄，請 `Grpc` `Debug` 將下列專案新增至您檔案中的層級， *appsettings.json* 方法是將下列專案新增至 `LogLevel` 中的子區段 `Logging` ：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="3ba4b-124">您也可以在 *Startup.cs* 中使用下列方式進行設定 `ConfigureLogging` ：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="3ba4b-125">如果您未使用以 JSON 為基礎的設定，請在設定系統中設定下列設定值：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="3ba4b-126">請檢查設定系統的檔，以判斷如何指定嵌套設定值。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="3ba4b-127">例如，使用環境變數時， `_` 會使用兩個字元，而不是 `:` (例如 `Logging__LogLevel__Grpc`) 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="3ba4b-128">`Debug`針對您的應用程式收集更詳細的診斷時，建議使用此層級。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="3ba4b-129">`Trace`層級會產生非常低層級的診斷，而且很少需要診斷您應用程式中的問題。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="3ba4b-130">範例記錄輸出</span><span class="sxs-lookup"><span data-stu-id="3ba4b-130">Sample logging output</span></span>

<span data-ttu-id="3ba4b-131">以下是 `Debug` gRPC 服務層級的主控台輸出範例：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="3ba4b-132">存取伺服器端記錄</span><span class="sxs-lookup"><span data-stu-id="3ba4b-132">Access server-side logs</span></span>

<span data-ttu-id="3ba4b-133">您存取伺服器端記錄的方式取決於您執行的環境。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="3ba4b-134">作為主控台應用程式</span><span class="sxs-lookup"><span data-stu-id="3ba4b-134">As a console app</span></span>

<span data-ttu-id="3ba4b-135">如果您是在主控台應用程式中執行，則預設應啟用 [主控台記錄器](xref:fundamentals/logging/index#console) 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="3ba4b-136">gRPC 記錄將會出現在主控台中。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="3ba4b-137">其他環境</span><span class="sxs-lookup"><span data-stu-id="3ba4b-137">Other environments</span></span>

<span data-ttu-id="3ba4b-138">如果應用程式部署到另一個環境 (例如 Docker、Kubernetes 或 Windows 服務) ，請參閱， <xref:fundamentals/logging/index> 以取得如何設定適用于環境的記錄提供者的詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="3ba4b-139">gRPC 用戶端記錄</span><span class="sxs-lookup"><span data-stu-id="3ba4b-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="3ba4b-140">用戶端記錄可能包含來自您應用程式的機密資訊。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3ba4b-141">**切勿** 將未經處理的記錄從生產環境應用程式張貼到 GitHub 之類的公用論壇。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3ba4b-142">若要從 .NET 用戶端取得記錄，您可以 `GrpcChannelOptions.LoggerFactory` 在建立用戶端通道時設定屬性。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="3ba4b-143">如果您是從 ASP.NET Core 應用程式呼叫 gRPC 服務，則可以從 (DI) 的相依性插入來解析記錄器 factory：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="3ba4b-144">啟用用戶端記錄的替代方式是使用 [gRPC 用戶端 factory](xref:grpc/clientfactory) 來建立用戶端。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="3ba4b-145">向用戶端處理站註冊並從 DI 解析的 gRPC 用戶端會自動使用應用程式設定的記錄。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="3ba4b-146">如果您的應用程式不使用 DI，則可以 `ILoggerFactory` 使用 [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)建立新的實例。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="3ba4b-147">若要存取此方法，請將 [Microsoft Extensions. 記錄](https://www.nuget.org/packages/microsoft.extensions.logging/) 套件新增至您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="3ba4b-148">gRPC 用戶端記錄範圍</span><span class="sxs-lookup"><span data-stu-id="3ba4b-148">gRPC client log scopes</span></span>

<span data-ttu-id="3ba4b-149">GRPC 用戶端會將 [記錄範圍](../fundamentals/logging/index.md#log-scopes) 新增至在 gRPC 呼叫期間進行的記錄。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-149">The gRPC client adds a [logging scope](../fundamentals/logging/index.md#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="3ba4b-150">此範圍具有與 gRPC 呼叫相關的中繼資料：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="3ba4b-151">**GrpcMethodType** -gRPC 方法類型。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="3ba4b-152">可能的值是列舉的名稱 `Grpc.Core.MethodType` ，例如一元</span><span class="sxs-lookup"><span data-stu-id="3ba4b-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="3ba4b-153">**GrpcUri** -gRPC 方法的相對 URI，例如/greet。Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="3ba4b-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="3ba4b-154">範例記錄輸出</span><span class="sxs-lookup"><span data-stu-id="3ba4b-154">Sample logging output</span></span>

<span data-ttu-id="3ba4b-155">以下是 `Debug` gRPC 用戶端層級的主控台輸出範例：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="3ba4b-156">追蹤</span><span class="sxs-lookup"><span data-stu-id="3ba4b-156">Tracing</span></span>

<span data-ttu-id="3ba4b-157">gRPC services 和 gRPC 用戶端會提供使用 [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) 和 [活動](/dotnet/api/system.diagnostics.activity)進行 gRPC 呼叫的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="3ba4b-158">.NET gRPC 會使用活動來代表 gRPC 的呼叫。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="3ba4b-159">追蹤事件會在 gRPC 呼叫活動開始和停止時寫入診斷來源。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="3ba4b-160">追蹤不會在 gRPC 串流呼叫的存留期傳送訊息時，捕捉相關資訊。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="3ba4b-161">gRPC 服務追蹤</span><span class="sxs-lookup"><span data-stu-id="3ba4b-161">gRPC service tracing</span></span>

<span data-ttu-id="3ba4b-162">gRPC 服務裝載于 ASP.NET Core，可報告傳入 HTTP 要求的相關事件。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="3ba4b-163">gRPC 特定的中繼資料會新增至 ASP.NET Core 提供的現有 HTTP 要求診斷。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="3ba4b-164">診斷來源名稱為 `Microsoft.AspNetCore` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="3ba4b-165">活動名稱為 `Microsoft.AspNetCore.Hosting.HttpRequestIn` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="3ba4b-166">GRPC 呼叫所叫用之 gRPC 方法的名稱會加入為具有名稱的標記 `grpc.method` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="3ba4b-167">GRPC 呼叫完成時的狀態碼會新增為具有名稱的標記 `grpc.status_code` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="3ba4b-168">gRPC 用戶端追蹤</span><span class="sxs-lookup"><span data-stu-id="3ba4b-168">gRPC client tracing</span></span>

<span data-ttu-id="3ba4b-169">.NET gRPC 用戶端會使用 `HttpClient` 來進行 gRPC 呼叫。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="3ba4b-170">雖然 `HttpClient` 會寫入診斷事件，但是 .Net gRPC 用戶端會提供自訂的診斷來源、活動和事件，以便收集 gRPC 呼叫的完整資訊。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="3ba4b-171">診斷來源名稱為 `Grpc.Net.Client` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="3ba4b-172">活動名稱為 `Grpc.Net.Client.GrpcOut` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="3ba4b-173">GRPC 呼叫所叫用之 gRPC 方法的名稱會加入為具有名稱的標記 `grpc.method` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="3ba4b-174">GRPC 呼叫完成時的狀態碼會新增為具有名稱的標記 `grpc.status_code` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="3ba4b-175">收集追蹤</span><span class="sxs-lookup"><span data-stu-id="3ba4b-175">Collecting tracing</span></span>

<span data-ttu-id="3ba4b-176">最簡單的使用方式 `DiagnosticSource` 是在您的應用程式中設定遙測程式庫（例如 [Application Insights](/azure/azure-monitor/app/asp-net-core) 或 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) ）。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="3ba4b-177">此程式庫會處理 gRPC 呼叫的相關資訊，以及其他應用程式遙測。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="3ba4b-178">您可以在受管理的服務（例如 Application Insights）中查看追蹤，也可以選擇執行您自己的分散式追蹤系統。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="3ba4b-179">OpenTelemetry 支援將追蹤資料匯出至 [Jaeger](https://www.jaegertracing.io/) 和 [Zipkin](https://zipkin.io/)。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="3ba4b-180">`DiagnosticSource` 可以使用程式碼中的追蹤事件 `DiagnosticListener` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="3ba4b-181">如需有關使用程式碼接聽診斷來源的詳細資訊，請參閱 [DiagnosticSource 使用者手冊](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="3ba4b-182">遙測程式庫目前不會捕獲 gRPC 特定 `Grpc.Net.Client.GrpcOut` 的遙測。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="3ba4b-183">改善遙測程式庫的工作正在進行中。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="3ba4b-184">計量</span><span class="sxs-lookup"><span data-stu-id="3ba4b-184">Metrics</span></span>

<span data-ttu-id="3ba4b-185">計量是在一段時間內的資料量值標記法，例如每秒要求數。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="3ba4b-186">計量資料可讓您在高階層級觀察應用程式的狀態。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="3ba4b-187">.NET gRPC 計量是使用發出的 `EventCounter` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="3ba4b-188">gRPC 服務計量</span><span class="sxs-lookup"><span data-stu-id="3ba4b-188">gRPC service metrics</span></span>

<span data-ttu-id="3ba4b-189">系統會報告事件來源的 gRPC 伺服器度量 `Grpc.AspNetCore.Server` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="3ba4b-190">名稱</span><span class="sxs-lookup"><span data-stu-id="3ba4b-190">Name</span></span>                      | <span data-ttu-id="3ba4b-191">描述</span><span class="sxs-lookup"><span data-stu-id="3ba4b-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="3ba4b-192">呼叫總數</span><span class="sxs-lookup"><span data-stu-id="3ba4b-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="3ba4b-193">目前的呼叫</span><span class="sxs-lookup"><span data-stu-id="3ba4b-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="3ba4b-194">呼叫總數失敗</span><span class="sxs-lookup"><span data-stu-id="3ba4b-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="3ba4b-195">總呼叫期限超過</span><span class="sxs-lookup"><span data-stu-id="3ba4b-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="3ba4b-196">傳送的郵件總數</span><span class="sxs-lookup"><span data-stu-id="3ba4b-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="3ba4b-197">已接收的訊息總數</span><span class="sxs-lookup"><span data-stu-id="3ba4b-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="3ba4b-198">未實現的呼叫總數</span><span class="sxs-lookup"><span data-stu-id="3ba4b-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="3ba4b-199">ASP.NET Core 也會提供自己的 `Microsoft.AspNetCore.Hosting` 事件來源計量。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="3ba4b-200">gRPC 用戶端計量</span><span class="sxs-lookup"><span data-stu-id="3ba4b-200">gRPC client metrics</span></span>

<span data-ttu-id="3ba4b-201">系統會報告事件來源的 gRPC 用戶端計量 `Grpc.Net.Client` 。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="3ba4b-202">名稱</span><span class="sxs-lookup"><span data-stu-id="3ba4b-202">Name</span></span>                      | <span data-ttu-id="3ba4b-203">描述</span><span class="sxs-lookup"><span data-stu-id="3ba4b-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="3ba4b-204">呼叫總數</span><span class="sxs-lookup"><span data-stu-id="3ba4b-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="3ba4b-205">目前的呼叫</span><span class="sxs-lookup"><span data-stu-id="3ba4b-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="3ba4b-206">呼叫總數失敗</span><span class="sxs-lookup"><span data-stu-id="3ba4b-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="3ba4b-207">總呼叫期限超過</span><span class="sxs-lookup"><span data-stu-id="3ba4b-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="3ba4b-208">傳送的郵件總數</span><span class="sxs-lookup"><span data-stu-id="3ba4b-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="3ba4b-209">已接收的訊息總數</span><span class="sxs-lookup"><span data-stu-id="3ba4b-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="3ba4b-210">觀察計量</span><span class="sxs-lookup"><span data-stu-id="3ba4b-210">Observe metrics</span></span>

<span data-ttu-id="3ba4b-211">[dotnet-計數器](/dotnet/core/diagnostics/dotnet-counters) 是一種效能監視工具，適用于臨機操作健全狀況監視和第一層效能調查。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="3ba4b-212">使用 `Grpc.AspNetCore.Server` 或 `Grpc.Net.Client` 作為提供者名稱監視 .net 應用程式。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="3ba4b-213">另一種觀察 gRPC 計量的方式，就是使用 Application Insights 的 [ApplicationInsights EventCounterCollector 套件](/azure/azure-monitor/app/eventcounters)來捕捉計數器資料。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="3ba4b-214">一旦安裝之後，Application Insights 會在執行時間收集常見的 .NET 計數器。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="3ba4b-215">預設不會收集 gRPC 的計數器，但可以自訂 App Insights [以包含額外的計數器](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)。</span><span class="sxs-lookup"><span data-stu-id="3ba4b-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="3ba4b-216">針對要在 *Startup.cs* 中收集的應用程式見解指定 gRPC 計數器：</span><span class="sxs-lookup"><span data-stu-id="3ba4b-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="3ba4b-217">其他資源</span><span class="sxs-lookup"><span data-stu-id="3ba4b-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>