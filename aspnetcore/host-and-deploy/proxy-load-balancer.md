---
title: 設定 ASP.NET Core 以與 Proxy 伺服器和負載平衡器搭配運作
author: rick-anderson
description: 了解裝載在 Proxy 伺服器和負載平衡器 (通常會遮蔽重要要求資訊) 後方之應用程式的設定。
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e63821743dad565b51d5c2360dcc2fbf0632754f
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530043"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="a4dac-103">設定 ASP.NET Core 以與 Proxy 伺服器和負載平衡器搭配運作</span><span class="sxs-lookup"><span data-stu-id="a4dac-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="a4dac-104">由 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="a4dac-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a4dac-105">在建議的 ASP.NET Core 設定中，是使用 IIS/ASP.NET Core 模組、Nginx 或 Apache 來裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="a4dac-106">Proxy 伺服器、負載平衡器及其他網路設備通常會在要求觸達應用程式之前，遮蔽要求的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="a4dac-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="a4dac-107">透過 HTTP 作為 Proxy 來處理 HTTPS 要求時，原始配置 (HTTPS) 會遺失而必須在標頭中轉送。</span><span class="sxs-lookup"><span data-stu-id="a4dac-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="a4dac-108">由於應用程式會從 Proxy 而不是從網際網路或公司網路上的真實來源收到要求，因此必須也在標頭中轉送原始用戶端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="a4dac-109">此資訊在要求處理方面可能相當重要，例如在重新導向、驗證、連結產生、原則評估及用戶端地理位置方面。</span><span class="sxs-lookup"><span data-stu-id="a4dac-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="a4dac-110">轉送的標頭</span><span class="sxs-lookup"><span data-stu-id="a4dac-110">Forwarded headers</span></span>

<span data-ttu-id="a4dac-111">依照慣例，Proxy 會以 HTTP 標頭轉送資訊。</span><span class="sxs-lookup"><span data-stu-id="a4dac-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="a4dac-112">標頭</span><span class="sxs-lookup"><span data-stu-id="a4dac-112">Header</span></span> | <span data-ttu-id="a4dac-113">描述</span><span class="sxs-lookup"><span data-stu-id="a4dac-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="a4dac-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="a4dac-114">X-Forwarded-For</span></span> | <span data-ttu-id="a4dac-115">針對在 Proxy 鏈結中起始要求及後續 Proxy 的用戶端，保存用戶端的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="a4dac-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="a4dac-116">此參數可能包含 IP 位址 (以及視需要可能會有連接埠號碼)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="a4dac-117">在 Proxy 伺服器鏈結中，第一個參數會指出起始要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4dac-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="a4dac-118">後面接著後續的 Proxy 識別碼。</span><span class="sxs-lookup"><span data-stu-id="a4dac-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="a4dac-119">鏈結中的最後一個 Proxy 並不在參數清單中。</span><span class="sxs-lookup"><span data-stu-id="a4dac-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="a4dac-120">最後一個 Proxy 的 IP 位址 (以及視需要會有連接埠號碼) 會在傳輸層以遠端 IP 位址的形式提供。</span><span class="sxs-lookup"><span data-stu-id="a4dac-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="a4dac-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="a4dac-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="a4dac-122">原始配置的值 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="a4dac-123">如果要求周遊了多個 Proxy，則此值也可能是一個配置清單。</span><span class="sxs-lookup"><span data-stu-id="a4dac-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="a4dac-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="a4dac-124">X-Forwarded-Host</span></span> | <span data-ttu-id="a4dac-125">主機標頭欄位的原始值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-125">The original value of the Host header field.</span></span> <span data-ttu-id="a4dac-126">通常，Proxy 不會修改主機標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="a4dac-127">如需有關權限提高弱點的資訊，請參閱 [Microsoft 資訊安全諮詢 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) \(英文\)，此弱點會影響 Proxy 不會驗證或限制主機標頭為已知有效值的系統。</span><span class="sxs-lookup"><span data-stu-id="a4dac-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="a4dac-128">轉送的標頭中介軟體 (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>) ，會讀取這些標頭，並在上填入相關聯的欄位 <xref:Microsoft.AspNetCore.Http.HttpContext> 。</span><span class="sxs-lookup"><span data-stu-id="a4dac-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="a4dac-129">中介軟體會更新：</span><span class="sxs-lookup"><span data-stu-id="a4dac-129">The middleware updates:</span></span>

* <span data-ttu-id="a4dac-130">[Server.remoteipaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress)：使用 `X-Forwarded-For` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="a4dac-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="a4dac-131">額外的設定會影響中介軟體設定 `RemoteIpAddress`的方式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="a4dac-132">如需詳細資料，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="a4dac-133">[HttpCoNtext. 配置](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme)：使用 `X-Forwarded-Proto` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="a4dac-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="a4dac-134">[HttpcoNtext.current](xref:Microsoft.AspNetCore.Http.HttpRequest.Host)：使用 `X-Forwarded-Host` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="a4dac-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="a4dac-135">您可以設定「轉送的標頭中介軟體」的[預設設定](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="a4dac-136">預設設定值為：</span><span class="sxs-lookup"><span data-stu-id="a4dac-136">The default settings are:</span></span>

* <span data-ttu-id="a4dac-137">在應用程式與要求的來源之間只有「一個 Proxy」。</span><span class="sxs-lookup"><span data-stu-id="a4dac-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="a4dac-138">針對已知的 Proxy 和已知的網路，只會設定回送位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="a4dac-139">轉送標頭名稱為 `X-Forwarded-For` 和 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="a4dac-140">並非所有網路設備在新增 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭時都不含額外組態。</span><span class="sxs-lookup"><span data-stu-id="a4dac-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="a4dac-141">如果透過 Proxy 傳送的要求在觸達應用程式時未包含這些標頭，請向您的設備製造商尋求指引。</span><span class="sxs-lookup"><span data-stu-id="a4dac-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="a4dac-142">如果設備使用的名稱不是 `X-Forwarded-For` 和 `X-Forwarded-Proto`，請設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 與 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 選項，以符合設備使用的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="a4dac-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="a4dac-143">如需詳細資訊，請參閱[轉送標頭中介軟體選項](#forwarded-headers-middleware-options)和[使用不同標頭名稱之 Proxy 的組態](#configuration-for-a-proxy-that-uses-different-header-names)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="a4dac-144">IIS/IIS Express 和 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="a4dac-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="a4dac-145">當應用程式是在 IIS 和 ASP.NET Core 模組的後方進行[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)裝載時，[IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)預設會啟用「轉送的標頭中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="a4dac-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="a4dac-146">由於有與轉送標頭相關的信任考量 (例如 [IP 詐騙](https://www.iplocation.net/ip-spoofing))，因此「轉送的標頭中介軟體」在啟用後會先在中介軟體管線中搭配 ASP.NET Core 模組限定的設定來執行。</span><span class="sxs-lookup"><span data-stu-id="a4dac-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="a4dac-147">中介軟體會經設定來轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭，並限制成單一 localhost Proxy。</span><span class="sxs-lookup"><span data-stu-id="a4dac-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="a4dac-148">如果需要額外的設定，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a4dac-149">其他 Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="a4dac-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a4dac-150">除了在[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)裝載時使用 [IIS 整合](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)之外，都未預設啟用「轉送的標頭中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="a4dac-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="a4dac-151">必須啟用「轉送的標頭中介軟體」，應用程式才能使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 來處理轉送的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="a4dac-152">啟用此中介軟體之後，如果未將任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 指定給中介軟體，則預設的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 會是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="a4dac-153">搭配 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 設定中介軟體，以在 `Startup.ConfigureServices` 中轉送 `X-Forwarded-For` 與 `X-Forwarded-Proto` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="a4dac-154">轉送標頭中介軟體順序</span><span class="sxs-lookup"><span data-stu-id="a4dac-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="a4dac-155">轉送的標頭中介軟體應該在其他中介軟體之前執行。</span><span class="sxs-lookup"><span data-stu-id="a4dac-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="a4dac-156">這種排序可確保依賴轉送標頭資訊的中介軟體可以耗用用於處理的標頭值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="a4dac-157">轉送的標頭中介軟體可在診斷和錯誤處理之後執行，但必須在呼叫之前執行 `UseHsts` ：</span><span class="sxs-lookup"><span data-stu-id="a4dac-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="a4dac-158">或者， `UseForwardedHeaders` 在診斷之前呼叫：</span><span class="sxs-lookup"><span data-stu-id="a4dac-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="a4dac-159">若未在 `Startup.ConfigureServices` 中指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，或未使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 直接指定到擴充方法，則要轉送的預設標頭是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="a4dac-160">必須使用要轉送的標頭設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 屬性。</span><span class="sxs-lookup"><span data-stu-id="a4dac-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="a4dac-161">Nginx 組態</span><span class="sxs-lookup"><span data-stu-id="a4dac-161">Nginx configuration</span></span>

<span data-ttu-id="a4dac-162">若要轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭，請參閱 <xref:host-and-deploy/linux-nginx#configure-nginx>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="a4dac-163">如需詳細資訊，請參閱 [NGINX：使用轉送的標頭](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="a4dac-164">Apache 組態</span><span class="sxs-lookup"><span data-stu-id="a4dac-164">Apache configuration</span></span>

<span data-ttu-id="a4dac-165">`X-Forwarded-For` 會自動新增 (請參閱 [Apache 模組 mod_proxy：反向 Proxy 要求標頭](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers))。</span><span class="sxs-lookup"><span data-stu-id="a4dac-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="a4dac-166">如需如何轉送 `X-Forwarded-Proto` 標頭的資訊，請參閱 <xref:host-and-deploy/linux-apache#configure-apache>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="a4dac-167">轉送的標頭中介軟體選項</span><span class="sxs-lookup"><span data-stu-id="a4dac-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="a4dac-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 會控制轉送標頭中介軟體的行為。</span><span class="sxs-lookup"><span data-stu-id="a4dac-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a4dac-169">下列範例會變更預設值：</span><span class="sxs-lookup"><span data-stu-id="a4dac-169">The following example changes the default values:</span></span>

* <span data-ttu-id="a4dac-170">將轉送標頭中的項目數限制為 `2`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="a4dac-171">新增已知的 Proxy 位址 `127.0.10.1`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="a4dac-172">將轉送標頭名稱從預設的 `X-Forwarded-For` 變更為 `X-Forwarded-For-My-Custom-Header-Name`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="a4dac-173">選項</span><span class="sxs-lookup"><span data-stu-id="a4dac-173">Option</span></span> | <span data-ttu-id="a4dac-174">描述</span><span class="sxs-lookup"><span data-stu-id="a4dac-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="a4dac-175">依據 `X-Forwarded-Host` 標頭將主機限制成所提供的值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="a4dac-176">比較值時，會使用序數忽略大小寫的方式來比較。</span><span class="sxs-lookup"><span data-stu-id="a4dac-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="a4dac-177">必須排除連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="a4dac-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="a4dac-178">如果清單空白，即表示允許所有主機。</span><span class="sxs-lookup"><span data-stu-id="a4dac-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="a4dac-179">最上層的萬用字元 `*` 代表會允許所有非空白的主機。</span><span class="sxs-lookup"><span data-stu-id="a4dac-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="a4dac-180">允許使用子網域萬用字元，但不會比對出根網域。</span><span class="sxs-lookup"><span data-stu-id="a4dac-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="a4dac-181">例如，`*.contoso.com` 會比對出子網域 `foo.contoso.com`，但不會比對出根網域 `contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="a4dac-182">允許使用 Unicode 主機名稱，但會轉換成 [Punycode](https://tools.ietf.org/html/rfc3492) 來進行比對。</span><span class="sxs-lookup"><span data-stu-id="a4dac-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="a4dac-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) 必須包含週框方括號，並採用[慣例格式](https://tools.ietf.org/html/rfc4291#section-2.2) (例如 `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="a4dac-184">IPv6 位址並未特別設計成會檢查不同格式間是否具有邏輯相等性，因此不會執行標準化。</span><span class="sxs-lookup"><span data-stu-id="a4dac-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="a4dac-185">如果無法限制可允許的主機，可能會讓攻擊者偽造服務所產生的連結。</span><span class="sxs-lookup"><span data-stu-id="a4dac-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="a4dac-186">預設值是空的 `IList<string>`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="a4dac-187">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="a4dac-188">當 Proxy/轉寄站未使用 `X-Forwarded-For` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="a4dac-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a4dac-189">預設為 `X-Forwarded-For`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="a4dac-190">識別應該處理哪個轉送子。</span><span class="sxs-lookup"><span data-stu-id="a4dac-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="a4dac-191">如需適用的欄位清單，請參閱 [ForwardedHeaders 列舉](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="a4dac-192">指派給此屬性的一般值為 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="a4dac-193">預設值為 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="a4dac-194">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="a4dac-195">當 Proxy/轉寄站未使用 `X-Forwarded-Host` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="a4dac-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a4dac-196">預設為 `X-Forwarded-Host`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="a4dac-197">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="a4dac-198">當 Proxy/轉寄站未使用 `X-Forwarded-Proto` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="a4dac-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a4dac-199">預設為 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="a4dac-200">限制所處理標頭中的項目數。</span><span class="sxs-lookup"><span data-stu-id="a4dac-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="a4dac-201">設定為 `null` 可停用限制，但應該只有在已設定 `KnownProxies` 或 `KnownNetworks` 的情況下，才這樣做。</span><span class="sxs-lookup"><span data-stu-id="a4dac-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="a4dac-202">設置非 `null` 值是一種預防措施 (但不是保證)，以防止設定不正確的 Proxy 和來自網路上的旁路惡意要求。</span><span class="sxs-lookup"><span data-stu-id="a4dac-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="a4dac-203">「轉送的標頭中介軟體」會以相反順序 (從右至左) 處理標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a4dac-204">如果使用預設值 (`1`)，除非 `ForwardLimit` 的值增加，否則只會處理標頭中最右邊的值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="a4dac-205">預設為 `1`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="a4dac-206">可從中接受轉送標頭的已知網路位址範圍。</span><span class="sxs-lookup"><span data-stu-id="a4dac-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="a4dac-207">請使用無類別網域間路由 (CIDR) 標記法來提供 IP 範圍。</span><span class="sxs-lookup"><span data-stu-id="a4dac-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="a4dac-208">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a4dac-209">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a4dac-210">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="a4dac-211">預設值為包含單一  項目的 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>`IPAddress.Loopback`>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-211">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="a4dac-212">可從中接受轉送標頭的已知 Proxy 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-212">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="a4dac-213">請使用 `KnownProxies` 來指定確切的相符 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-213">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="a4dac-214">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-214">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a4dac-215">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-215">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a4dac-216">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-216">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="a4dac-217">預設值為包含單一  項目的 `IList`\<<xref:System.Net.IPAddress>`IPAddress.IPv6Loopback`>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-217">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="a4dac-218">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="a4dac-219">預設為 `X-Original-For`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-219">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="a4dac-220">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="a4dac-221">預設為 `X-Original-Host`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-221">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="a4dac-222">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="a4dac-223">預設為 `X-Original-Proto`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-223">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="a4dac-224">要求所處理 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 的標頭值數目必須同步。</span><span class="sxs-lookup"><span data-stu-id="a4dac-224">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="a4dac-225">ASP.NET Core 1.x 中的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-225">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="a4dac-226">ASP.NET Core 2.0 或更新版本中的預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-226">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="a4dac-227">情節和使用案例</span><span class="sxs-lookup"><span data-stu-id="a4dac-227">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="a4dac-228">當可以新增轉送的標頭且所有要求都安全時</span><span class="sxs-lookup"><span data-stu-id="a4dac-228">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="a4dac-229">在某些情況下，可能無法將轉送的標頭新增至透過 Proxy 傳送給應用程式的要求。</span><span class="sxs-lookup"><span data-stu-id="a4dac-229">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="a4dac-230">如果 Proxy 強制要求所有公用外部要求都必須是 HTTPS，您可以在使用任何類型的中介軟體之前，先手動在 `Startup.Configure` 中設定該配置：</span><span class="sxs-lookup"><span data-stu-id="a4dac-230">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="a4dac-231">在開發或預備環境中，可以使用環境變數或其他組態設定來停用此程式碼。</span><span class="sxs-lookup"><span data-stu-id="a4dac-231">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="a4dac-232">處理路徑基底和變更要求路徑的 Proxy</span><span class="sxs-lookup"><span data-stu-id="a4dac-232">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="a4dac-233">有些 Proxy 會原封不動傳送路徑，但其中含有應移除才能正確路由傳送的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="a4dac-233">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="a4dac-234">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 中介軟體會將該路徑分割成 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)，以及將應用程式基底路徑分割成 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-234">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="a4dac-235">如果 `/foo` 是以 `/foo/api/1` 形式傳送之 Proxy 路徑的應用程式基底路徑，中介軟體就會使用下列命令，將 `Request.PathBase` 設定為 `/foo`，以及將 `Request.Path` 設定為 `/api/1`：</span><span class="sxs-lookup"><span data-stu-id="a4dac-235">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="a4dac-236">反向再次呼叫應用程式時，則會重新套用原始路徑和路徑基底。</span><span class="sxs-lookup"><span data-stu-id="a4dac-236">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="a4dac-237">如需中介軟體順序處理的詳細資訊，請參閱 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-237">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="a4dac-238">如果 Proxy 會修剪路徑 (例如，將 `/foo/api/1` 轉送給 `/api/1`)，請設定要求的 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 屬性來修正重新導向和連結：</span><span class="sxs-lookup"><span data-stu-id="a4dac-238">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="a4dac-239">如果 Proxy 會新增路徑資料，請使用 <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> 並指派給 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 屬性，以捨棄部分路徑來修正重新導向和連結：</span><span class="sxs-lookup"><span data-stu-id="a4dac-239">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="a4dac-240">使用不同標頭名稱之 Proxy 的組態</span><span class="sxs-lookup"><span data-stu-id="a4dac-240">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="a4dac-241">如果 Proxy 未使用名為 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的標頭轉送 Proxy 位址/連接埠與原始配置資訊，請設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 與 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 選項，以符合 Proxy 使用的標頭名稱：</span><span class="sxs-lookup"><span data-stu-id="a4dac-241">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="a4dac-242">轉送 Linux 和非 IIS 反向 Proxy 的配置</span><span class="sxs-lookup"><span data-stu-id="a4dac-242">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="a4dac-243">如果部署至 Azure Linux App Service、Azure Linux 虛擬機器 (VM)，或 IIS 之外的任何其他反向 Proxy 後端，則呼叫 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 和 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 的這些應用程式會使站台進入無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="a4dac-243">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="a4dac-244">反向 Proxy 會終止 TLS，且正確的要求配置不知道有 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="a4dac-244">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="a4dac-245">OAuth 和 OIDC 在此組態中也失敗，因為它們會產生不正確的重新導向。</span><span class="sxs-lookup"><span data-stu-id="a4dac-245">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="a4dac-246"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 在 IIS 後端執行時，會新增並設定轉送標頭中介軟體，但沒有相符的 Linux (Apache 或 Nginx 整合) 自動組態。</span><span class="sxs-lookup"><span data-stu-id="a4dac-246"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="a4dac-247">若要從非 IIS 案例的 Proxy 轉送配置，請新增並設定轉送標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="a4dac-247">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="a4dac-248">在 `Startup.ConfigureServices` 中，使用下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="a4dac-248">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="a4dac-249">憑證轉送</span><span class="sxs-lookup"><span data-stu-id="a4dac-249">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="a4dac-250">Azure</span><span class="sxs-lookup"><span data-stu-id="a4dac-250">Azure</span></span>

<span data-ttu-id="a4dac-251">若要設定憑證轉送 Azure App Service，請參閱 [設定 Azure App Service 的 TLS 相互驗證](/azure/app-service/app-service-web-configure-tls-mutual-auth)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-251">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="a4dac-252">下列指導方針適用于設定 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-252">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="a4dac-253">在中 `Startup.Configure` ，在呼叫之前加入下列程式碼 `app.UseAuthentication();` ：</span><span class="sxs-lookup"><span data-stu-id="a4dac-253">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="a4dac-254">設定憑證轉送中介軟體以指定 Azure 所使用的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="a4dac-254">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="a4dac-255">在中 `Startup.ConfigureServices` ，新增下列程式碼，以設定中介軟體用來建立憑證的標頭：</span><span class="sxs-lookup"><span data-stu-id="a4dac-255">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="a4dac-256">其他 web proxy</span><span class="sxs-lookup"><span data-stu-id="a4dac-256">Other web proxies</span></span>

<span data-ttu-id="a4dac-257">如果使用不是 IIS 或 Azure App Service 的應用程式要求路由 (ARR) 的 proxy，請將 proxy 設定為轉寄它在 HTTP 標頭中收到的憑證。</span><span class="sxs-lookup"><span data-stu-id="a4dac-257">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="a4dac-258">在中 `Startup.Configure` ，在呼叫之前加入下列程式碼 `app.UseAuthentication();` ：</span><span class="sxs-lookup"><span data-stu-id="a4dac-258">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="a4dac-259">設定憑證轉送中介軟體以指定標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="a4dac-259">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="a4dac-260">在中 `Startup.ConfigureServices` ，新增下列程式碼，以設定中介軟體用來建立憑證的標頭：</span><span class="sxs-lookup"><span data-stu-id="a4dac-260">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="a4dac-261">如果 proxy 不是以 base64 編碼憑證 (與 Nginx) 的情況相同，請設定此 `HeaderConverter` 選項。</span><span class="sxs-lookup"><span data-stu-id="a4dac-261">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="a4dac-262">請考慮 `Startup.ConfigureServices` 中的下列範例：</span><span class="sxs-lookup"><span data-stu-id="a4dac-262">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="a4dac-263">疑難排解</span><span class="sxs-lookup"><span data-stu-id="a4dac-263">Troubleshoot</span></span>

<span data-ttu-id="a4dac-264">當標頭未如預期般傳送時，請啟用[記錄功能](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-264">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a4dac-265">如果記錄提供的資訊不足，無法針對問題進行疑難排解，則請列舉伺服器所收到的要求標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-265">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="a4dac-266">使用內嵌中介軟體將要求標頭寫入應用程式回應或記錄標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-266">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="a4dac-267">若要將標頭寫入至應用程式的回應，將下列終端機內嵌中介軟體緊接著放置於 `Startup.Configure` 中對 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的呼叫之後：</span><span class="sxs-lookup"><span data-stu-id="a4dac-267">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="a4dac-268">您可以寫入至記錄，而不是回應本文。</span><span class="sxs-lookup"><span data-stu-id="a4dac-268">You can write to logs instead of the response body.</span></span> <span data-ttu-id="a4dac-269">寫入至記錄可讓網站在偵錯時正常運作。</span><span class="sxs-lookup"><span data-stu-id="a4dac-269">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="a4dac-270">若要寫入記錄而不是回應本文：</span><span class="sxs-lookup"><span data-stu-id="a4dac-270">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="a4dac-271">將 `ILogger<Startup>` 插入至 `Startup` 類別，如[在啟動中建立記錄](xref:fundamentals/logging/index#create-logs-in-startup)中所述。</span><span class="sxs-lookup"><span data-stu-id="a4dac-271">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="a4dac-272">將下列終端機內嵌中介軟體緊接著放置於 `Startup.Configure` 中對 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的呼叫之後。</span><span class="sxs-lookup"><span data-stu-id="a4dac-272">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="a4dac-273">處理後，`X-Forwarded-{For|Proto|Host}` 值會移至 `X-Original-{For|Proto|Host}`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-273">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="a4dac-274">如果指定的標頭中有多個值，「轉送的標頭中介軟體」會以相反順序 (從右至左) 處理標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-274">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a4dac-275">預設的 `ForwardLimit` 是 `1` (一)，因此除非 `ForwardLimit` 的值增加，否則只會處理標頭中最右邊的值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-275">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="a4dac-276">要求的原始遠端 IP 必須符合 `KnownProxies` 或 `KnownNetworks` 清單中的項目，系統才會處理轉送的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-276">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="a4dac-277">這可藉由不接受來自不受信任 Proxy 的轉送子，以限制標頭詐騙行為。</span><span class="sxs-lookup"><span data-stu-id="a4dac-277">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="a4dac-278">當偵測到未知的 Proxy 時，記錄會指出 Proxy 的位址：</span><span class="sxs-lookup"><span data-stu-id="a4dac-278">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="a4dac-279">在上述範例中，10.0.0.100 是 Proxy 伺服器。</span><span class="sxs-lookup"><span data-stu-id="a4dac-279">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="a4dac-280">如果伺服器是信任的 Proxy，請將伺服器的 IP 位址新增至 `Startup.ConfigureServices` 中的 `KnownProxies` (或將信任的網路新增至 `KnownNetworks`)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-280">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4dac-281">如需詳細資訊，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)一節。</span><span class="sxs-lookup"><span data-stu-id="a4dac-281">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="a4dac-282">只允許信任的 Proxy 以及網路轉送標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-282">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="a4dac-283">否則，[IP 詐騙](https://www.iplocation.net/ip-spoofing)攻擊有可能發生。</span><span class="sxs-lookup"><span data-stu-id="a4dac-283">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4dac-284">其他資源</span><span class="sxs-lookup"><span data-stu-id="a4dac-284">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="a4dac-285">[Microsoft Security Advisory CVE-2018-0787：ASP.NET Core 權限提高弱點](https://github.com/aspnet/Announcements/issues/295) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a4dac-285">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a4dac-286">在建議的 ASP.NET Core 設定中，是使用 IIS/ASP.NET Core 模組、Nginx 或 Apache 來裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-286">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="a4dac-287">Proxy 伺服器、負載平衡器及其他網路設備通常會在要求觸達應用程式之前，遮蔽要求的相關資訊：</span><span class="sxs-lookup"><span data-stu-id="a4dac-287">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="a4dac-288">透過 HTTP 作為 Proxy 來處理 HTTPS 要求時，原始配置 (HTTPS) 會遺失而必須在標頭中轉送。</span><span class="sxs-lookup"><span data-stu-id="a4dac-288">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="a4dac-289">由於應用程式會從 Proxy 而不是從網際網路或公司網路上的真實來源收到要求，因此必須也在標頭中轉送原始用戶端 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-289">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="a4dac-290">此資訊在要求處理方面可能相當重要，例如在重新導向、驗證、連結產生、原則評估及用戶端地理位置方面。</span><span class="sxs-lookup"><span data-stu-id="a4dac-290">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="a4dac-291">轉送的標頭</span><span class="sxs-lookup"><span data-stu-id="a4dac-291">Forwarded headers</span></span>

<span data-ttu-id="a4dac-292">依照慣例，Proxy 會以 HTTP 標頭轉送資訊。</span><span class="sxs-lookup"><span data-stu-id="a4dac-292">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="a4dac-293">標頭</span><span class="sxs-lookup"><span data-stu-id="a4dac-293">Header</span></span> | <span data-ttu-id="a4dac-294">描述</span><span class="sxs-lookup"><span data-stu-id="a4dac-294">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="a4dac-295">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="a4dac-295">X-Forwarded-For</span></span> | <span data-ttu-id="a4dac-296">針對在 Proxy 鏈結中起始要求及後續 Proxy 的用戶端，保存用戶端的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="a4dac-296">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="a4dac-297">此參數可能包含 IP 位址 (以及視需要可能會有連接埠號碼)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-297">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="a4dac-298">在 Proxy 伺服器鏈結中，第一個參數會指出起始要求的用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4dac-298">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="a4dac-299">後面接著後續的 Proxy 識別碼。</span><span class="sxs-lookup"><span data-stu-id="a4dac-299">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="a4dac-300">鏈結中的最後一個 Proxy 並不在參數清單中。</span><span class="sxs-lookup"><span data-stu-id="a4dac-300">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="a4dac-301">最後一個 Proxy 的 IP 位址 (以及視需要會有連接埠號碼) 會在傳輸層以遠端 IP 位址的形式提供。</span><span class="sxs-lookup"><span data-stu-id="a4dac-301">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="a4dac-302">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="a4dac-302">X-Forwarded-Proto</span></span> | <span data-ttu-id="a4dac-303">原始配置的值 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-303">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="a4dac-304">如果要求周遊了多個 Proxy，則此值也可能是一個配置清單。</span><span class="sxs-lookup"><span data-stu-id="a4dac-304">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="a4dac-305">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="a4dac-305">X-Forwarded-Host</span></span> | <span data-ttu-id="a4dac-306">主機標頭欄位的原始值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-306">The original value of the Host header field.</span></span> <span data-ttu-id="a4dac-307">通常，Proxy 不會修改主機標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-307">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="a4dac-308">如需有關權限提高弱點的資訊，請參閱 [Microsoft 資訊安全諮詢 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) \(英文\)，此弱點會影響 Proxy 不會驗證或限制主機標頭為已知有效值的系統。</span><span class="sxs-lookup"><span data-stu-id="a4dac-308">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="a4dac-309">來自 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 套件的「轉送的標頭中介軟體」會讀取這些標頭，並填入 <xref:Microsoft.AspNetCore.Http.HttpContext> 上相關聯的欄位。</span><span class="sxs-lookup"><span data-stu-id="a4dac-309">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="a4dac-310">中介軟體會更新：</span><span class="sxs-lookup"><span data-stu-id="a4dac-310">The middleware updates:</span></span>

* <span data-ttu-id="a4dac-311">[Server.remoteipaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress)：使用 `X-Forwarded-For` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="a4dac-311">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="a4dac-312">額外的設定會影響中介軟體設定 `RemoteIpAddress`的方式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-312">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="a4dac-313">如需詳細資料，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-313">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="a4dac-314">[HttpCoNtext. 配置](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme)：使用 `X-Forwarded-Proto` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="a4dac-314">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="a4dac-315">[HttpcoNtext.current](xref:Microsoft.AspNetCore.Http.HttpRequest.Host)：使用 `X-Forwarded-Host` 標頭值設定。</span><span class="sxs-lookup"><span data-stu-id="a4dac-315">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="a4dac-316">您可以設定「轉送的標頭中介軟體」的[預設設定](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-316">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="a4dac-317">預設設定值為：</span><span class="sxs-lookup"><span data-stu-id="a4dac-317">The default settings are:</span></span>

* <span data-ttu-id="a4dac-318">在應用程式與要求的來源之間只有「一個 Proxy」。</span><span class="sxs-lookup"><span data-stu-id="a4dac-318">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="a4dac-319">針對已知的 Proxy 和已知的網路，只會設定回送位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-319">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="a4dac-320">轉送標頭名稱為 `X-Forwarded-For` 和 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-320">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="a4dac-321">並非所有網路設備在新增 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭時都不含額外組態。</span><span class="sxs-lookup"><span data-stu-id="a4dac-321">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="a4dac-322">如果透過 Proxy 傳送的要求在觸達應用程式時未包含這些標頭，請向您的設備製造商尋求指引。</span><span class="sxs-lookup"><span data-stu-id="a4dac-322">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="a4dac-323">如果設備使用的名稱不是 `X-Forwarded-For` 和 `X-Forwarded-Proto`，請設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 與 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 選項，以符合設備使用的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="a4dac-323">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="a4dac-324">如需詳細資訊，請參閱[轉送標頭中介軟體選項](#forwarded-headers-middleware-options)和[使用不同標頭名稱之 Proxy 的組態](#configuration-for-a-proxy-that-uses-different-header-names)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-324">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="a4dac-325">IIS/IIS Express 和 ASP.NET Core 模組</span><span class="sxs-lookup"><span data-stu-id="a4dac-325">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="a4dac-326">當應用程式是在 IIS 和 ASP.NET Core 模組的後方進行[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)裝載時，[IIS 整合中介軟體](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)預設會啟用「轉送的標頭中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="a4dac-326">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="a4dac-327">由於有與轉送標頭相關的信任考量 (例如 [IP 詐騙](https://www.iplocation.net/ip-spoofing))，因此「轉送的標頭中介軟體」在啟用後會先在中介軟體管線中搭配 ASP.NET Core 模組限定的設定來執行。</span><span class="sxs-lookup"><span data-stu-id="a4dac-327">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="a4dac-328">中介軟體會經設定來轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭，並限制成單一 localhost Proxy。</span><span class="sxs-lookup"><span data-stu-id="a4dac-328">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="a4dac-329">如果需要額外的設定，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-329">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a4dac-330">其他 Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="a4dac-330">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a4dac-331">除了在[處理序外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)裝載時使用 [IIS 整合](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)之外，都未預設啟用「轉送的標頭中介軟體」。</span><span class="sxs-lookup"><span data-stu-id="a4dac-331">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="a4dac-332">必須啟用「轉送的標頭中介軟體」，應用程式才能使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 來處理轉送的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-332">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="a4dac-333">啟用此中介軟體之後，如果未將任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 指定給中介軟體，則預設的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 會是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-333">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="a4dac-334">搭配 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 設定中介軟體，以在 `Startup.ConfigureServices` 中轉送 `X-Forwarded-For` 與 `X-Forwarded-Proto` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-334">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4dac-335">在呼叫其他中介軟體之前，請先在 `Startup.Configure` 中叫用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 方法：</span><span class="sxs-lookup"><span data-stu-id="a4dac-335">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="a4dac-336">若未在 `Startup.ConfigureServices` 中指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，或未使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 直接指定到擴充方法，則要轉送的預設標頭是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-336">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="a4dac-337">必須使用要轉送的標頭設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 屬性。</span><span class="sxs-lookup"><span data-stu-id="a4dac-337">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="a4dac-338">Nginx 組態</span><span class="sxs-lookup"><span data-stu-id="a4dac-338">Nginx configuration</span></span>

<span data-ttu-id="a4dac-339">若要轉送 `X-Forwarded-For` 和 `X-Forwarded-Proto` 標頭，請參閱 <xref:host-and-deploy/linux-nginx#configure-nginx>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-339">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="a4dac-340">如需詳細資訊，請參閱 [NGINX：使用轉送的標頭](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-340">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="a4dac-341">Apache 組態</span><span class="sxs-lookup"><span data-stu-id="a4dac-341">Apache configuration</span></span>

<span data-ttu-id="a4dac-342">`X-Forwarded-For` 會自動新增 (請參閱 [Apache 模組 mod_proxy：反向 Proxy 要求標頭](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers))。</span><span class="sxs-lookup"><span data-stu-id="a4dac-342">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="a4dac-343">如需如何轉送 `X-Forwarded-Proto` 標頭的資訊，請參閱 <xref:host-and-deploy/linux-apache#configure-apache>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-343">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="a4dac-344">轉送的標頭中介軟體選項</span><span class="sxs-lookup"><span data-stu-id="a4dac-344">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="a4dac-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 會控制轉送標頭中介軟體的行為。</span><span class="sxs-lookup"><span data-stu-id="a4dac-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a4dac-346">下列範例會變更預設值：</span><span class="sxs-lookup"><span data-stu-id="a4dac-346">The following example changes the default values:</span></span>

* <span data-ttu-id="a4dac-347">將轉送標頭中的項目數限制為 `2`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-347">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="a4dac-348">新增已知的 Proxy 位址 `127.0.10.1`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-348">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="a4dac-349">將轉送標頭名稱從預設的 `X-Forwarded-For` 變更為 `X-Forwarded-For-My-Custom-Header-Name`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-349">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="a4dac-350">選項</span><span class="sxs-lookup"><span data-stu-id="a4dac-350">Option</span></span> | <span data-ttu-id="a4dac-351">描述</span><span class="sxs-lookup"><span data-stu-id="a4dac-351">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="a4dac-352">依據 `X-Forwarded-Host` 標頭將主機限制成所提供的值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-352">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="a4dac-353">比較值時，會使用序數忽略大小寫的方式來比較。</span><span class="sxs-lookup"><span data-stu-id="a4dac-353">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="a4dac-354">必須排除連接埠號碼。</span><span class="sxs-lookup"><span data-stu-id="a4dac-354">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="a4dac-355">如果清單空白，即表示允許所有主機。</span><span class="sxs-lookup"><span data-stu-id="a4dac-355">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="a4dac-356">最上層的萬用字元 `*` 代表會允許所有非空白的主機。</span><span class="sxs-lookup"><span data-stu-id="a4dac-356">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="a4dac-357">允許使用子網域萬用字元，但不會比對出根網域。</span><span class="sxs-lookup"><span data-stu-id="a4dac-357">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="a4dac-358">例如，`*.contoso.com` 會比對出子網域 `foo.contoso.com`，但不會比對出根網域 `contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-358">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="a4dac-359">允許使用 Unicode 主機名稱，但會轉換成 [Punycode](https://tools.ietf.org/html/rfc3492) 來進行比對。</span><span class="sxs-lookup"><span data-stu-id="a4dac-359">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="a4dac-360">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) 必須包含週框方括號，並採用[慣例格式](https://tools.ietf.org/html/rfc4291#section-2.2) (例如 `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-360">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="a4dac-361">IPv6 位址並未特別設計成會檢查不同格式間是否具有邏輯相等性，因此不會執行標準化。</span><span class="sxs-lookup"><span data-stu-id="a4dac-361">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="a4dac-362">如果無法限制可允許的主機，可能會讓攻擊者偽造服務所產生的連結。</span><span class="sxs-lookup"><span data-stu-id="a4dac-362">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="a4dac-363">預設值是空的 `IList<string>`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-363">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="a4dac-364">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-364">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="a4dac-365">當 Proxy/轉寄站未使用 `X-Forwarded-For` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="a4dac-365">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a4dac-366">預設為 `X-Forwarded-For`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-366">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="a4dac-367">識別應該處理哪個轉送子。</span><span class="sxs-lookup"><span data-stu-id="a4dac-367">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="a4dac-368">如需適用的欄位清單，請參閱 [ForwardedHeaders 列舉](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-368">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="a4dac-369">指派給此屬性的一般值為 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-369">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="a4dac-370">預設值為 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-370">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="a4dac-371">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-371">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="a4dac-372">當 Proxy/轉寄站未使用 `X-Forwarded-Host` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="a4dac-372">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a4dac-373">預設為 `X-Forwarded-Host`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-373">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="a4dac-374">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-374">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="a4dac-375">當 Proxy/轉寄站未使用 `X-Forwarded-Proto` 標頭，而使用其他標頭轉送資訊時，會使用此選項。</span><span class="sxs-lookup"><span data-stu-id="a4dac-375">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a4dac-376">預設為 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-376">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="a4dac-377">限制所處理標頭中的項目數。</span><span class="sxs-lookup"><span data-stu-id="a4dac-377">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="a4dac-378">設定為 `null` 可停用限制，但應該只有在已設定 `KnownProxies` 或 `KnownNetworks` 的情況下，才這樣做。</span><span class="sxs-lookup"><span data-stu-id="a4dac-378">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="a4dac-379">設置非 `null` 值是一種預防措施 (但不是保證)，以防止設定不正確的 Proxy 和來自網路上的旁路惡意要求。</span><span class="sxs-lookup"><span data-stu-id="a4dac-379">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="a4dac-380">「轉送的標頭中介軟體」會以相反順序 (從右至左) 處理標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-380">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a4dac-381">如果使用預設值 (`1`)，除非 `ForwardLimit` 的值增加，否則只會處理標頭中最右邊的值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-381">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="a4dac-382">預設為 `1`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-382">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="a4dac-383">可從中接受轉送標頭的已知網路位址範圍。</span><span class="sxs-lookup"><span data-stu-id="a4dac-383">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="a4dac-384">請使用無類別網域間路由 (CIDR) 標記法來提供 IP 範圍。</span><span class="sxs-lookup"><span data-stu-id="a4dac-384">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="a4dac-385">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-385">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a4dac-386">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-386">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a4dac-387">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-387">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a4dac-388">如需詳細資訊，請參閱[以 IPv6 位址表示的 IPv4 位址設定](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)一節。</span><span class="sxs-lookup"><span data-stu-id="a4dac-388">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a4dac-389">預設值為包含單一  項目的 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>`IPAddress.Loopback`>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-389">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="a4dac-390">可從中接受轉送標頭的已知 Proxy 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-390">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="a4dac-391">請使用 `KnownProxies` 來指定確切的相符 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-391">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="a4dac-392">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-392">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a4dac-393">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-393">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a4dac-394">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-394">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a4dac-395">如需詳細資訊，請參閱[以 IPv6 位址表示的 IPv4 位址設定](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)一節。</span><span class="sxs-lookup"><span data-stu-id="a4dac-395">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a4dac-396">預設值為包含單一  項目的 `IList`\<<xref:System.Net.IPAddress>`IPAddress.IPv6Loopback`>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-396">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="a4dac-397">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-397">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="a4dac-398">預設為 `X-Original-For`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-398">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="a4dac-399">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-399">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="a4dac-400">預設為 `X-Original-Host`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-400">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="a4dac-401">使用此屬性所指定的標頭，而不是 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) 所指定的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-401">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="a4dac-402">預設為 `X-Original-Proto`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-402">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="a4dac-403">要求所處理 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 的標頭值數目必須同步。</span><span class="sxs-lookup"><span data-stu-id="a4dac-403">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="a4dac-404">ASP.NET Core 1.x 中的預設值為 `true`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-404">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="a4dac-405">ASP.NET Core 2.0 或更新版本中的預設值為 `false`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-405">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="a4dac-406">情節和使用案例</span><span class="sxs-lookup"><span data-stu-id="a4dac-406">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="a4dac-407">當可以新增轉送的標頭且所有要求都安全時</span><span class="sxs-lookup"><span data-stu-id="a4dac-407">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="a4dac-408">在某些情況下，可能無法將轉送的標頭新增至透過 Proxy 傳送給應用程式的要求。</span><span class="sxs-lookup"><span data-stu-id="a4dac-408">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="a4dac-409">如果 Proxy 強制要求所有公用外部要求都必須是 HTTPS，您可以在使用任何類型的中介軟體之前，先手動在 `Startup.Configure` 中設定該配置：</span><span class="sxs-lookup"><span data-stu-id="a4dac-409">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="a4dac-410">在開發或預備環境中，可以使用環境變數或其他組態設定來停用此程式碼。</span><span class="sxs-lookup"><span data-stu-id="a4dac-410">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="a4dac-411">處理路徑基底和變更要求路徑的 Proxy</span><span class="sxs-lookup"><span data-stu-id="a4dac-411">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="a4dac-412">有些 Proxy 會原封不動傳送路徑，但其中含有應移除才能正確路由傳送的應用程式基底路徑。</span><span class="sxs-lookup"><span data-stu-id="a4dac-412">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="a4dac-413">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 中介軟體會將該路徑分割成 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)，以及將應用程式基底路徑分割成 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-413">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="a4dac-414">如果 `/foo` 是以 `/foo/api/1` 形式傳送之 Proxy 路徑的應用程式基底路徑，中介軟體就會使用下列命令，將 `Request.PathBase` 設定為 `/foo`，以及將 `Request.Path` 設定為 `/api/1`：</span><span class="sxs-lookup"><span data-stu-id="a4dac-414">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="a4dac-415">反向再次呼叫應用程式時，則會重新套用原始路徑和路徑基底。</span><span class="sxs-lookup"><span data-stu-id="a4dac-415">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="a4dac-416">如需中介軟體順序處理的詳細資訊，請參閱 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="a4dac-416">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="a4dac-417">如果 Proxy 會修剪路徑 (例如，將 `/foo/api/1` 轉送給 `/api/1`)，請設定要求的 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 屬性來修正重新導向和連結：</span><span class="sxs-lookup"><span data-stu-id="a4dac-417">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="a4dac-418">如果 Proxy 會新增路徑資料，請使用 <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> 並指派給 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 屬性，以捨棄部分路徑來修正重新導向和連結：</span><span class="sxs-lookup"><span data-stu-id="a4dac-418">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="a4dac-419">使用不同標頭名稱之 Proxy 的組態</span><span class="sxs-lookup"><span data-stu-id="a4dac-419">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="a4dac-420">如果 Proxy 未使用名為 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的標頭轉送 Proxy 位址/連接埠與原始配置資訊，請設定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 與 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 選項，以符合 Proxy 使用的標頭名稱：</span><span class="sxs-lookup"><span data-stu-id="a4dac-420">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="a4dac-421">以 IPv6 位址表示的 IPv4 位址設定</span><span class="sxs-lookup"><span data-stu-id="a4dac-421">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="a4dac-422">若伺服器使用雙模式通訊端，會以 IPv6 格式 (例如，IPv4 中的 `10.0.0.1` 在 IPv6 中以 `::ffff:10.0.0.1` 表示) 或 `::ffff:a00:1` 提供 IPv4 位址。</span><span class="sxs-lookup"><span data-stu-id="a4dac-422">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="a4dac-423">請參閱 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-423">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a4dac-424">透過查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 以判斷是否需要此格式。</span><span class="sxs-lookup"><span data-stu-id="a4dac-424">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="a4dac-425">在下列範例中，提供轉寄標頭的網路位址已以 IPv6 格式新增到 `KnownNetworks` 清單中。</span><span class="sxs-lookup"><span data-stu-id="a4dac-425">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="a4dac-426">IPv4 位址：`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="a4dac-426">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="a4dac-427">轉換的 IPv6 位址：`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="a4dac-427">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="a4dac-428">轉換的首碼長度：104</span><span class="sxs-lookup"><span data-stu-id="a4dac-428">Converted prefix length: 104</span></span>

<span data-ttu-id="a4dac-429">您也能以十六進位格式提供位址 (`10.11.12.1` 在 IPv6 中以 `::ffff:0a0b:0c01` 表示)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-429">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="a4dac-430">當您將 IPv4 位址轉換為 IPv6 時，請加入 96 到 CIDR 首碼長度 (在此範例中為 `8`) 以將額外的 `::ffff:` IPv6 首碼 (8 + 96 = 104) 納入考慮。</span><span class="sxs-lookup"><span data-stu-id="a4dac-430">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="a4dac-431">轉送 Linux 和非 IIS 反向 Proxy 的配置</span><span class="sxs-lookup"><span data-stu-id="a4dac-431">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="a4dac-432">如果部署至 Azure Linux App Service、Azure Linux 虛擬機器 (VM)，或 IIS 之外的任何其他反向 Proxy 後端，則呼叫 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 和 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 的這些應用程式會使站台進入無限迴圈。</span><span class="sxs-lookup"><span data-stu-id="a4dac-432">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="a4dac-433">反向 Proxy 會終止 TLS，且正確的要求配置不知道有 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="a4dac-433">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="a4dac-434">OAuth 和 OIDC 在此組態中也失敗，因為它們會產生不正確的重新導向。</span><span class="sxs-lookup"><span data-stu-id="a4dac-434">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="a4dac-435"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 在 IIS 後端執行時，會新增並設定轉送標頭中介軟體，但沒有相符的 Linux (Apache 或 Nginx 整合) 自動組態。</span><span class="sxs-lookup"><span data-stu-id="a4dac-435"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="a4dac-436">若要從非 IIS 案例的 Proxy 轉送配置，請新增並設定轉送標頭中介軟體。</span><span class="sxs-lookup"><span data-stu-id="a4dac-436">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="a4dac-437">在 `Startup.ConfigureServices` 中，使用下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="a4dac-437">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="a4dac-438">疑難排解</span><span class="sxs-lookup"><span data-stu-id="a4dac-438">Troubleshoot</span></span>

<span data-ttu-id="a4dac-439">當標頭未如預期般傳送時，請啟用[記錄功能](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-439">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a4dac-440">如果記錄提供的資訊不足，無法針對問題進行疑難排解，則請列舉伺服器所收到的要求標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-440">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="a4dac-441">使用內嵌中介軟體將要求標頭寫入應用程式回應或記錄標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-441">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="a4dac-442">若要將標頭寫入至應用程式的回應，將下列終端機內嵌中介軟體緊接著放置於 `Startup.Configure` 中對 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的呼叫之後：</span><span class="sxs-lookup"><span data-stu-id="a4dac-442">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="a4dac-443">您可以寫入至記錄，而不是回應本文。</span><span class="sxs-lookup"><span data-stu-id="a4dac-443">You can write to logs instead of the response body.</span></span> <span data-ttu-id="a4dac-444">寫入至記錄可讓網站在偵錯時正常運作。</span><span class="sxs-lookup"><span data-stu-id="a4dac-444">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="a4dac-445">若要寫入記錄而不是回應本文：</span><span class="sxs-lookup"><span data-stu-id="a4dac-445">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="a4dac-446">將 `ILogger<Startup>` 插入至 `Startup` 類別，如[在啟動中建立記錄](xref:fundamentals/logging/index#create-logs-in-startup)中所述。</span><span class="sxs-lookup"><span data-stu-id="a4dac-446">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="a4dac-447">將下列終端機內嵌中介軟體緊接著放置於 `Startup.Configure` 中對 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的呼叫之後。</span><span class="sxs-lookup"><span data-stu-id="a4dac-447">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="a4dac-448">處理後，`X-Forwarded-{For|Proto|Host}` 值會移至 `X-Original-{For|Proto|Host}`。</span><span class="sxs-lookup"><span data-stu-id="a4dac-448">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="a4dac-449">如果指定的標頭中有多個值，「轉送的標頭中介軟體」會以相反順序 (從右至左) 處理標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-449">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a4dac-450">預設的 `ForwardLimit` 是 `1` (一)，因此除非 `ForwardLimit` 的值增加，否則只會處理標頭中最右邊的值。</span><span class="sxs-lookup"><span data-stu-id="a4dac-450">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="a4dac-451">要求的原始遠端 IP 必須符合 `KnownProxies` 或 `KnownNetworks` 清單中的項目，系統才會處理轉送的標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-451">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="a4dac-452">這可藉由不接受來自不受信任 Proxy 的轉送子，以限制標頭詐騙行為。</span><span class="sxs-lookup"><span data-stu-id="a4dac-452">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="a4dac-453">當偵測到未知的 Proxy 時，記錄會指出 Proxy 的位址：</span><span class="sxs-lookup"><span data-stu-id="a4dac-453">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="a4dac-454">在上述範例中，10.0.0.100 是 Proxy 伺服器。</span><span class="sxs-lookup"><span data-stu-id="a4dac-454">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="a4dac-455">如果伺服器是信任的 Proxy，請將伺服器的 IP 位址新增至 `Startup.ConfigureServices` 中的 `KnownProxies` (或將信任的網路新增至 `KnownNetworks`)。</span><span class="sxs-lookup"><span data-stu-id="a4dac-455">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4dac-456">如需詳細資訊，請參閱[轉送的標頭中介軟體選項](#forwarded-headers-middleware-options)一節。</span><span class="sxs-lookup"><span data-stu-id="a4dac-456">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="a4dac-457">只允許信任的 Proxy 以及網路轉送標頭。</span><span class="sxs-lookup"><span data-stu-id="a4dac-457">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="a4dac-458">否則，[IP 詐騙](https://www.iplocation.net/ip-spoofing)攻擊有可能發生。</span><span class="sxs-lookup"><span data-stu-id="a4dac-458">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4dac-459">其他資源</span><span class="sxs-lookup"><span data-stu-id="a4dac-459">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="a4dac-460">[Microsoft Security Advisory CVE-2018-0787：ASP.NET Core 權限提高弱點](https://github.com/aspnet/Announcements/issues/295) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a4dac-460">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
