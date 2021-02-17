---
title: ASP.NET Core SignalR 生產裝載和調整規模
author: bradygaster
description: 瞭解如何在使用 ASP.NET Core 的應用程式中避免效能和調整問題 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564010"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="ff453-103">ASP.NET Core SignalR 裝載和調整</span><span class="sxs-lookup"><span data-stu-id="ff453-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="ff453-104">[Andrew Stanton-護士](https://twitter.com/anurse)、 [Brady Gaster](https://twitter.com/bradygaster)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="ff453-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="ff453-105">本文說明使用 ASP.NET Core 之高流量應用程式的裝載和調整考慮 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="ff453-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="ff453-106">粘滯話</span><span class="sxs-lookup"><span data-stu-id="ff453-106">Sticky Sessions</span></span>

<span data-ttu-id="ff453-107">SignalR 要求特定連接的所有 HTTP 要求都必須由相同的伺服器進程處理。</span><span class="sxs-lookup"><span data-stu-id="ff453-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="ff453-108">當 SignalR (多部) 伺服器的伺服器陣列上執行時，必須使用「粘滯話」。</span><span class="sxs-lookup"><span data-stu-id="ff453-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="ff453-109">某些負載平衡器也會將「粘滯話」稱為會話親和性。</span><span class="sxs-lookup"><span data-stu-id="ff453-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="ff453-110">Azure App Service 使用 [應用程式要求路由](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) 來路由傳送要求。</span><span class="sxs-lookup"><span data-stu-id="ff453-110">Azure App Service uses [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="ff453-111">啟用 Azure App Service 中的 [ARR 親和性] 設定，將會啟用「粘滯話」。</span><span class="sxs-lookup"><span data-stu-id="ff453-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="ff453-112">不需要執行任何粘滯會話的唯一情況是：</span><span class="sxs-lookup"><span data-stu-id="ff453-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="ff453-113">在單一伺服器上裝載時，在單一進程中。</span><span class="sxs-lookup"><span data-stu-id="ff453-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="ff453-114">使用 Azure 服務時 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="ff453-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="ff453-115">當所有用戶端都設定為 **僅** 使用 websocket， **且** 用戶端設定中已啟用 [SkipNegotiation 設定](xref:signalr/configuration#configure-additional-options) 時。</span><span class="sxs-lookup"><span data-stu-id="ff453-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="ff453-116">在所有其他情況下 (包括何時使用 Redis 後擋板) ，則必須針對粘滯話設定伺服器環境。</span><span class="sxs-lookup"><span data-stu-id="ff453-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="ff453-117">如需設定 Azure App Service 的指引 SignalR ，請參閱 <xref:signalr/publish-to-azure-web-app> 。</span><span class="sxs-lookup"><span data-stu-id="ff453-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="ff453-118">TCP 連接資源</span><span class="sxs-lookup"><span data-stu-id="ff453-118">TCP connection resources</span></span>

<span data-ttu-id="ff453-119">Web 服務器可支援的並行 TCP 連線數目有限。</span><span class="sxs-lookup"><span data-stu-id="ff453-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="ff453-120">標準 HTTP 用戶端會使用 *暫時* 連接。</span><span class="sxs-lookup"><span data-stu-id="ff453-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="ff453-121">當用戶端閒置並在稍後重新開啟時，可以關閉這些連接。</span><span class="sxs-lookup"><span data-stu-id="ff453-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="ff453-122">另一方面， SignalR 連接是 *永久性* 的。</span><span class="sxs-lookup"><span data-stu-id="ff453-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="ff453-123">SignalR 即使用戶端閒置，連線仍會保持開啟。</span><span class="sxs-lookup"><span data-stu-id="ff453-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="ff453-124">在提供許多用戶端的高流量應用程式中，這些持續連線可能會導致伺服器達到其最大連線數目。</span><span class="sxs-lookup"><span data-stu-id="ff453-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="ff453-125">持續性連接也會耗用一些額外的記憶體，以追蹤每個連接。</span><span class="sxs-lookup"><span data-stu-id="ff453-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="ff453-126">大量使用連接相關的資源， SignalR 可能會影響裝載于相同伺服器上的其他 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="ff453-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="ff453-127">當 SignalR 開啟並保存最後一個可用的 TCP 連線時，相同伺服器上的其他 web 應用程式也不會有其他可用的連接。</span><span class="sxs-lookup"><span data-stu-id="ff453-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="ff453-128">如果伺服器用盡連接，您會看到隨機的通訊端錯誤和連線重設錯誤。</span><span class="sxs-lookup"><span data-stu-id="ff453-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="ff453-129">例如：</span><span class="sxs-lookup"><span data-stu-id="ff453-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="ff453-130">若要讓 SignalR 資源使用狀況在其他 web 應用程式中造成錯誤，請 SignalR 在不同于其他 web 應用程式的伺服器上執行。</span><span class="sxs-lookup"><span data-stu-id="ff453-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="ff453-131">若要防止 SignalR 資源使用在應用程式中造成錯誤 SignalR ，請相應放大以限制伺服器必須處理的連接數目。</span><span class="sxs-lookup"><span data-stu-id="ff453-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="ff453-132">擴增</span><span class="sxs-lookup"><span data-stu-id="ff453-132">Scale out</span></span>

<span data-ttu-id="ff453-133">使用的應用程式 SignalR 需要追蹤其所有連接，這會造成伺服器陣列的問題。</span><span class="sxs-lookup"><span data-stu-id="ff453-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="ff453-134">新增伺服器，並取得其他伺服器不知道的新連接。</span><span class="sxs-lookup"><span data-stu-id="ff453-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="ff453-135">例如，在 SignalR 下圖中的每部伺服器上，並不知道其他伺服器上的連接。</span><span class="sxs-lookup"><span data-stu-id="ff453-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="ff453-136">當 SignalR 其中一個伺服器想要將訊息傳送給所有用戶端時，訊息只會傳送到連接到該伺服器的用戶端。</span><span class="sxs-lookup"><span data-stu-id="ff453-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![調整：：：非 loc (SignalR) ：：：沒有背板](scale/_static/scale-no-backplane.png)

<span data-ttu-id="ff453-138">解決此問題的選項是 [Azure SignalR 服務](#azure-signalr-service) 和 [Redis 背板](#redis-backplane)。</span><span class="sxs-lookup"><span data-stu-id="ff453-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="ff453-139">Azure SignalR 服務</span><span class="sxs-lookup"><span data-stu-id="ff453-139">Azure SignalR Service</span></span>

<span data-ttu-id="ff453-140">Azure SignalR 服務是 proxy，而不是背板。</span><span class="sxs-lookup"><span data-stu-id="ff453-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="ff453-141">每次用戶端啟動伺服器的連線時，就會將用戶端重新導向以連接至服務。</span><span class="sxs-lookup"><span data-stu-id="ff453-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="ff453-142">下圖說明該流程：</span><span class="sxs-lookup"><span data-stu-id="ff453-142">That process is illustrated in the following diagram:</span></span>

![建立連至 Azure：：：無 loc (SignalR) ：：： Service 的連線](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="ff453-144">結果是，服務會管理所有用戶端連線，而每個伺服器只需要少量的服務連接，如下圖所示：</span><span class="sxs-lookup"><span data-stu-id="ff453-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![連線至服務的用戶端，連線到服務的伺服器](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="ff453-146">這種向外延展的方法有幾個優點，而不是 Redis 背板的替代方案：</span><span class="sxs-lookup"><span data-stu-id="ff453-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="ff453-147">由於用戶端連線時，會立即將用戶端重新導向至 Azure 服務，因此不需要像是 [用戶端親和性](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)的「粘滯話」 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="ff453-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="ff453-148">SignalR應用程式可以根據傳送的訊息數目進行擴充，而 Azure 服務則會 SignalR 調整以處理任何數量的連線。</span><span class="sxs-lookup"><span data-stu-id="ff453-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="ff453-149">例如，可能有數千個用戶端，但如果每秒只傳送幾個訊息，則 SignalR 應用程式不需要向外延展至多部伺服器，只要處理連線本身即可。</span><span class="sxs-lookup"><span data-stu-id="ff453-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="ff453-150">SignalR應用程式不會使用與 web 應用程式明顯更多的連接資源 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="ff453-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="ff453-151">基於這些理由，我們建議 azure SignalR 服務裝載 SignalR 于 azure 上的所有 ASP.NET Core 應用程式，包括 App Service、vm 和容器。</span><span class="sxs-lookup"><span data-stu-id="ff453-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="ff453-152">如需詳細資訊，請參閱 [Azure SignalR 服務檔](/azure/azure-signalr/signalr-overview)。</span><span class="sxs-lookup"><span data-stu-id="ff453-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="ff453-153">Redis 後擋板</span><span class="sxs-lookup"><span data-stu-id="ff453-153">Redis backplane</span></span>

<span data-ttu-id="ff453-154">[Redis](https://redis.io/) 是記憶體中的索引鍵/值存放區，可支援具有發佈/訂閱模型的訊息系統。</span><span class="sxs-lookup"><span data-stu-id="ff453-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="ff453-155">SignalRRedis 背板使用 pub/sub 功能將訊息轉送至其他伺服器。</span><span class="sxs-lookup"><span data-stu-id="ff453-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="ff453-156">當用戶端建立連接時，會將連接資訊傳遞給後端。</span><span class="sxs-lookup"><span data-stu-id="ff453-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="ff453-157">當伺服器想要將訊息傳送給所有用戶端時，它會傳送至後端。</span><span class="sxs-lookup"><span data-stu-id="ff453-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="ff453-158">後擋板知道所有連線的用戶端，以及它們所在的伺服器。</span><span class="sxs-lookup"><span data-stu-id="ff453-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="ff453-159">它會透過各自的伺服器將訊息傳送給所有用戶端。</span><span class="sxs-lookup"><span data-stu-id="ff453-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="ff453-160">下圖說明此程式：</span><span class="sxs-lookup"><span data-stu-id="ff453-160">This process is illustrated in the following diagram:</span></span>

![Redis 後擋板，從一部伺服器傳送到所有用戶端的訊息](scale/_static/redis-backplane.png)

<span data-ttu-id="ff453-162">針對裝載在您自己的基礎結構上的應用程式，Redis 背板是建議的向外延展方法。</span><span class="sxs-lookup"><span data-stu-id="ff453-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="ff453-163">如果您的資料中心與 Azure 資料中心之間有明顯的連線延遲，則 SignalR 對於低延遲或高輸送量需求的內部部署應用程式，Azure 服務可能不是實際的選項。</span><span class="sxs-lookup"><span data-stu-id="ff453-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="ff453-164">稍早所述的 Azure SignalR 服務優點是 Redis 背板的缺點：</span><span class="sxs-lookup"><span data-stu-id="ff453-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="ff453-165">除非下列 **兩項** 都成立，否則需要有粘滯話（也稱為 [用戶端親和性](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)）：</span><span class="sxs-lookup"><span data-stu-id="ff453-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="ff453-166">所有用戶端都設定為 **僅** 使用 websocket。</span><span class="sxs-lookup"><span data-stu-id="ff453-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="ff453-167">用戶端設定中已啟用 [SkipNegotiation 設定](xref:signalr/configuration#configure-additional-options) 。</span><span class="sxs-lookup"><span data-stu-id="ff453-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="ff453-168">一旦在伺服器上起始連接，連接就必須保持在該伺服器上。</span><span class="sxs-lookup"><span data-stu-id="ff453-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="ff453-169">SignalR應用程式必須根據用戶端的數目進行相應放大，即使傳送的訊息數很少。</span><span class="sxs-lookup"><span data-stu-id="ff453-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="ff453-170">SignalR應用程式在沒有的情況下，會使用比 web 應用程式更多的連接資源 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="ff453-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="ff453-171">Windows 用戶端作業系統上的 IIS 限制</span><span class="sxs-lookup"><span data-stu-id="ff453-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="ff453-172">Windows 10 和 Windows 8. x 是用戶端作業系統。</span><span class="sxs-lookup"><span data-stu-id="ff453-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="ff453-173">用戶端作業系統上的 IIS 有10個並行連接的限制。</span><span class="sxs-lookup"><span data-stu-id="ff453-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="ff453-174">SignalR的連接包括：</span><span class="sxs-lookup"><span data-stu-id="ff453-174">SignalR's connections are:</span></span>

* <span data-ttu-id="ff453-175">暫時性且經常重新建立。</span><span class="sxs-lookup"><span data-stu-id="ff453-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="ff453-176">不再使用時，**不會** 立即處置。</span><span class="sxs-lookup"><span data-stu-id="ff453-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="ff453-177">上述條件讓它有可能達到用戶端作業系統上的10個連接限制。</span><span class="sxs-lookup"><span data-stu-id="ff453-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="ff453-178">當用戶端作業系統用於開發時，建議您：</span><span class="sxs-lookup"><span data-stu-id="ff453-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="ff453-179">避免 IIS。</span><span class="sxs-lookup"><span data-stu-id="ff453-179">Avoid IIS.</span></span>
* <span data-ttu-id="ff453-180">使用 Kestrel 或 IIS Express 作為部署目標。</span><span class="sxs-lookup"><span data-stu-id="ff453-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="ff453-181">使用 Nginx 的 Linux</span><span class="sxs-lookup"><span data-stu-id="ff453-181">Linux with Nginx</span></span>

<span data-ttu-id="ff453-182">以下包含啟用 Websocket、ServerSentEvents 和 LongPolling 的最低必要設定 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="ff453-182">The following contains the minimum required settings to enable WebSockets, ServerSentEvents, and LongPolling for SignalR:</span></span>

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

<span data-ttu-id="ff453-183">使用多個後端伺服器時，必須新增「粘滯話」以防止連線 SignalR 在連接時切換伺服器。</span><span class="sxs-lookup"><span data-stu-id="ff453-183">When multiple backend servers are used, sticky sessions must be added to prevent SignalR connections from switching servers when connecting.</span></span> <span data-ttu-id="ff453-184">有多種方式可以在 Nginx 中新增多個會話。</span><span class="sxs-lookup"><span data-stu-id="ff453-184">There are multiple ways to add sticky sessions in Nginx.</span></span> <span data-ttu-id="ff453-185">下列兩種方法會根據您可用的內容顯示在下方。</span><span class="sxs-lookup"><span data-stu-id="ff453-185">Two approaches are shown below depending on what you have available.</span></span>

<span data-ttu-id="ff453-186">除了先前的設定之外，還會新增下列各項。</span><span class="sxs-lookup"><span data-stu-id="ff453-186">The following is added in addition to the previous configuration.</span></span> <span data-ttu-id="ff453-187">在下列範例中， `backend` 是伺服器群組的名稱。</span><span class="sxs-lookup"><span data-stu-id="ff453-187">In the following examples, `backend` is the name of the group of servers.</span></span>

<span data-ttu-id="ff453-188">使用 [Nginx 開放原始](https://nginx.org/en/)碼時， `ip_hash` 可根據用戶端的 IP 位址，使用將連接路由至伺服器：</span><span class="sxs-lookup"><span data-stu-id="ff453-188">With [Nginx Open Source](https://nginx.org/en/), use `ip_hash` to route connections to a server based on the client's IP address:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

<span data-ttu-id="ff453-189">使用 [Nginx Plus](https://www.nginx.com/products/nginx)，請使用將 `sticky` 新增 cookie 至要求，並將使用者的要求釘選到伺服器：</span><span class="sxs-lookup"><span data-stu-id="ff453-189">With [Nginx Plus](https://www.nginx.com/products/nginx), use `sticky` to add a cookie to requests and pin the user's requests to a server:</span></span>

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

<span data-ttu-id="ff453-190">最後，將 `proxy_pass http://localhost:5000` 區段中的變更 `server` 為 `proxy_pass http://backend` 。</span><span class="sxs-lookup"><span data-stu-id="ff453-190">Finally, change `proxy_pass http://localhost:5000` in the `server` section to `proxy_pass http://backend`.</span></span>

<span data-ttu-id="ff453-191">如需有關 Websocket over Nginx 的詳細資訊，請參閱 [Nginx 作為 Websocket Proxy](https://www.nginx.com/blog/websocket-nginx)。</span><span class="sxs-lookup"><span data-stu-id="ff453-191">For more information on WebSockets over Nginx, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx).</span></span>

<span data-ttu-id="ff453-192">如需負載平衡和粘滯話的詳細資訊，請參閱 [NGINX 負載平衡](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)。</span><span class="sxs-lookup"><span data-stu-id="ff453-192">For more information on load balancing and sticky sessions, see [NGINX load balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).</span></span>

<span data-ttu-id="ff453-193">如需有關 Nginx ASP.NET Core 的詳細資訊，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="ff453-193">For more information about ASP.NET Core with Nginx see the following article:</span></span>
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a><span data-ttu-id="ff453-194">協力廠商 SignalR 背板提供者</span><span class="sxs-lookup"><span data-stu-id="ff453-194">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="ff453-195">NCache</span><span class="sxs-lookup"><span data-stu-id="ff453-195">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="ff453-196">[新奧爾良](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="ff453-196">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>
* <span data-ttu-id="ff453-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span><span class="sxs-lookup"><span data-stu-id="ff453-197">[Rebus](https://github.com/rebus-org/Rebus.SignalR)</span></span>

## <a name="next-steps"></a><span data-ttu-id="ff453-198">後續步驟</span><span class="sxs-lookup"><span data-stu-id="ff453-198">Next steps</span></span>

<span data-ttu-id="ff453-199">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="ff453-199">For more information, see the following resources:</span></span>

* [<span data-ttu-id="ff453-200">Azure SignalR 服務檔</span><span class="sxs-lookup"><span data-stu-id="ff453-200">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="ff453-201">設定 Redis 背板</span><span class="sxs-lookup"><span data-stu-id="ff453-201">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
