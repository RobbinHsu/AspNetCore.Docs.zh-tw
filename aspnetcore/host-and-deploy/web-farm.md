---
title: 在 Web 伺服陣列上裝載 ASP.NET Core
author: rick-anderson
description: 了解如何在 Web 伺服陣列環境中裝載具有共用資源之 ASP.NET Core 應用程式的多個執行個體。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/web-farm
ms.openlocfilehash: ee78e80a4eda3089943765700aa6bb62c6c1e07d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057514"
---
# <a name="host-aspnet-core-in-a-web-farm"></a><span data-ttu-id="79f6f-103">在 Web 伺服陣列上裝載 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79f6f-103">Host ASP.NET Core in a web farm</span></span>

<span data-ttu-id="79f6f-104">由 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="79f6f-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="79f6f-105">*Web 伺服陣列* 是一組兩個或多個 Web 伺服器 (或稱為「節點」)，用於裝載應用程式的多個執行個體。</span><span class="sxs-lookup"><span data-stu-id="79f6f-105">A *web farm* is a group of two or more web servers (or *nodes*) that host multiple instances of an app.</span></span> <span data-ttu-id="79f6f-106">當使用者的要求抵達 Web 伺服陣列時，「負載平衡器」會將要求分散到 Web 伺服陣列的節點。</span><span class="sxs-lookup"><span data-stu-id="79f6f-106">When requests from users arrive to a web farm, a *load balancer* distributes the requests to the web farm's nodes.</span></span> <span data-ttu-id="79f6f-107">Web 伺服陣列改善：</span><span class="sxs-lookup"><span data-stu-id="79f6f-107">Web farms improve:</span></span>

* <span data-ttu-id="79f6f-108">**可靠性/可用性**：當一或多個節點失敗時，負載平衡器可以將要求路由至其他正常運作的節點，以繼續處理要求。</span><span class="sxs-lookup"><span data-stu-id="79f6f-108">**Reliability/availability**: When one or more nodes fail, the load balancer can route requests to other functioning nodes to continue processing requests.</span></span>
* <span data-ttu-id="79f6f-109">**容量/效能**：多個節點可以處理比單一伺服器更多的要求。</span><span class="sxs-lookup"><span data-stu-id="79f6f-109">**Capacity/performance**: Multiple nodes can process more requests than a single server.</span></span> <span data-ttu-id="79f6f-110">負載平衡器可以將要求分散到節點，藉以平衡工作負載。</span><span class="sxs-lookup"><span data-stu-id="79f6f-110">The load balancer balances the workload by distributing requests to the nodes.</span></span>
* <span data-ttu-id="79f6f-111">擴充 **性**：需要更多或更少的容量時，可以增加或減少作用中節點的數目以符合工作負載。</span><span class="sxs-lookup"><span data-stu-id="79f6f-111">**Scalability**: When more or less capacity is required, the number of active nodes can be increased or decreased to match the workload.</span></span> <span data-ttu-id="79f6f-112">Web 伺服陣列的平台技術 (例如 [Azure App Service](https://azure.microsoft.com/services/app-service/)) 可以依系統管理員的要求自動新增或移除節點，也可以在沒有人為介入的情況下自動新增或移除節點。</span><span class="sxs-lookup"><span data-stu-id="79f6f-112">Web farm platform technologies, such as [Azure App Service](https://azure.microsoft.com/services/app-service/), can automatically add or remove nodes at the request of the system administrator or automatically without human intervention.</span></span>
* <span data-ttu-id="79f6f-113">可 **維護性**： web 伺服陣列的節點可以依賴一組共用服務，這會使得系統管理更容易。</span><span class="sxs-lookup"><span data-stu-id="79f6f-113">**Maintainability**: Nodes of a web farm can rely on a set of shared services, which results in easier system management.</span></span> <span data-ttu-id="79f6f-114">例如，Web 伺服陣列的節點可以依賴單一資料庫伺服器和靜態資源的通用網路位置，例如影像和可下載檔案。</span><span class="sxs-lookup"><span data-stu-id="79f6f-114">For example, the nodes of a web farm can rely upon a single database server and a common network location for static resources, such as images and downloadable files.</span></span>

<span data-ttu-id="79f6f-115">本主題針對依賴共用資源的 Web 伺服陣列，說明其中所裝載 ASP.NET Core 應用程式的設定和相依性。</span><span class="sxs-lookup"><span data-stu-id="79f6f-115">This topic describes configuration and dependencies for ASP.NET core apps hosted in a web farm that rely upon shared resources.</span></span>

## <a name="general-configuration"></a><span data-ttu-id="79f6f-116">一般設定</span><span class="sxs-lookup"><span data-stu-id="79f6f-116">General configuration</span></span>

<xref:host-and-deploy/index>  
<span data-ttu-id="79f6f-117">了解如何設定裝載環境及部署 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="79f6f-117">Learn how to set up hosting environments and deploy ASP.NET Core apps.</span></span> <span data-ttu-id="79f6f-118">在 Web 伺服陣列的每個節點上設定處理序管理員，以自動啟動和重新啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="79f6f-118">Configure a process manager on each node of the web farm to automate app starts and restarts.</span></span> <span data-ttu-id="79f6f-119">每個節點都需要 ASP.NET Core 執行階段。</span><span class="sxs-lookup"><span data-stu-id="79f6f-119">Each node requires the ASP.NET Core runtime.</span></span> <span data-ttu-id="79f6f-120">如需詳細資訊，請參閱文件的[主機和部署](xref:host-and-deploy/index)區段中的主題。</span><span class="sxs-lookup"><span data-stu-id="79f6f-120">For more information, see the topics in the [Host and deploy](xref:host-and-deploy/index) area of the documentation.</span></span>

<xref:host-and-deploy/proxy-load-balancer>  
<span data-ttu-id="79f6f-121">了解裝載在 Proxy 伺服器和負載平衡器 (通常會遮蔽重要要求資訊) 後方之應用程式的設定。</span><span class="sxs-lookup"><span data-stu-id="79f6f-121">Learn about configuration for apps hosted behind proxy servers and load balancers, which often obscure important request information.</span></span>

<xref:host-and-deploy/azure-apps/index>  
<span data-ttu-id="79f6f-122">[Azure App Service](https://azure.microsoft.com/services/app-service/) 是 [Microsoft 雲端運算平台服務](https://azure.microsoft.com/)，用於裝載 Web 應用程式，包括 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="79f6f-122">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="79f6f-123">App Service 是受到完整管理的平台，可提供自動調整規模、負載平衡、修補和持續部署等功能。</span><span class="sxs-lookup"><span data-stu-id="79f6f-123">App Service is a fully managed platform that provides automatic scaling, load balancing, patching, and continuous deployment.</span></span>

## <a name="app-data"></a><span data-ttu-id="79f6f-124">應用程式資料</span><span class="sxs-lookup"><span data-stu-id="79f6f-124">App data</span></span>

<span data-ttu-id="79f6f-125">當應用程式擴展到多個執行個體時，便可能需要跨節點共用的應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="79f6f-125">When an app is scaled to multiple instances, there might be app state that requires sharing across nodes.</span></span> <span data-ttu-id="79f6f-126">如果狀態是暫時性的，請考慮共用 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache)。</span><span class="sxs-lookup"><span data-stu-id="79f6f-126">If the state is transient, consider sharing an [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache).</span></span> <span data-ttu-id="79f6f-127">如果共用狀態需要持續性，請考慮將共用狀態儲存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="79f6f-127">If the shared state requires persistence, consider storing the shared state in a database.</span></span>

## <a name="required-configuration"></a><span data-ttu-id="79f6f-128">必要設定</span><span class="sxs-lookup"><span data-stu-id="79f6f-128">Required configuration</span></span>

<span data-ttu-id="79f6f-129">資料保護和快取需要部署到 Web 伺服陣列的應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="79f6f-129">Data Protection and Caching require configuration for apps deployed to a web farm.</span></span>

### <a name="data-protection"></a><span data-ttu-id="79f6f-130">資料保護</span><span class="sxs-lookup"><span data-stu-id="79f6f-130">Data Protection</span></span>

<span data-ttu-id="79f6f-131">應用程式使用 [ASP.NET Core 資料保護系統](xref:security/data-protection/introduction)來保護資料。</span><span class="sxs-lookup"><span data-stu-id="79f6f-131">The [ASP.NET Core Data Protection system](xref:security/data-protection/introduction) is used by apps to protect data.</span></span> <span data-ttu-id="79f6f-132">資料保護會依賴一組儲存在 *金鑰環* 中的密碼編譯金鑰。</span><span class="sxs-lookup"><span data-stu-id="79f6f-132">Data Protection relies upon a set of cryptographic keys stored in a *key ring*.</span></span> <span data-ttu-id="79f6f-133">初始化資料保護系統時，會套用在本機儲存金鑰環的[預設設定](xref:security/data-protection/configuration/default-settings)。</span><span class="sxs-lookup"><span data-stu-id="79f6f-133">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) that store the key ring locally.</span></span> <span data-ttu-id="79f6f-134">在預設設定下，Web 伺服陣列的每個節點上都會儲存一個唯一的金鑰環。</span><span class="sxs-lookup"><span data-stu-id="79f6f-134">Under the default configuration, a unique key ring is stored on each node of the web farm.</span></span> <span data-ttu-id="79f6f-135">因此，每個 Web 伺服陣列節點都無法解密由任何其他節點上的應用程式加密的資料。</span><span class="sxs-lookup"><span data-stu-id="79f6f-135">Consequently, each web farm node can't decrypt data that's encrypted by an app on any other node.</span></span> <span data-ttu-id="79f6f-136">預設設定通常不適用於在 Web 伺服陣列中裝載應用程式。</span><span class="sxs-lookup"><span data-stu-id="79f6f-136">The default configuration isn't generally appropriate for hosting apps in a web farm.</span></span> <span data-ttu-id="79f6f-137">實作共用金鑰環的替代方式，是一律將使用者要求路由至相同的節點。</span><span class="sxs-lookup"><span data-stu-id="79f6f-137">An alternative to implementing a shared key ring is to always route user requests to the same node.</span></span> <span data-ttu-id="79f6f-138">如需有關 Web 伺服陣列部署的資料保護系統設定詳細資訊，請參閱 <xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="79f6f-138">For more information on Data Protection system configuration for web farm deployments, see <xref:security/data-protection/configuration/overview>.</span></span>

### <a name="caching"></a><span data-ttu-id="79f6f-139">Caching</span><span class="sxs-lookup"><span data-stu-id="79f6f-139">Caching</span></span>

<span data-ttu-id="79f6f-140">在 Web 伺服陣列環境中，快取機制必須跨 Web 伺服陣列節點共用快取項目。</span><span class="sxs-lookup"><span data-stu-id="79f6f-140">In a web farm environment, the caching mechanism must share cached items across the web farm's nodes.</span></span> <span data-ttu-id="79f6f-141">快取必須依賴於通用 Redis 快取、共用的 SQL Server 資料庫，或跨 Web 伺服陣列共用快取項目的自訂快取實作。</span><span class="sxs-lookup"><span data-stu-id="79f6f-141">Caching must either rely upon a common Redis cache, a shared SQL Server database, or a custom caching implementation that shares cached items across the web farm.</span></span> <span data-ttu-id="79f6f-142">如需詳細資訊，請參閱 <xref:performance/caching/distributed> 。</span><span class="sxs-lookup"><span data-stu-id="79f6f-142">For more information, see <xref:performance/caching/distributed>.</span></span>

## <a name="dependent-components"></a><span data-ttu-id="79f6f-143">相依元件</span><span class="sxs-lookup"><span data-stu-id="79f6f-143">Dependent components</span></span>

<span data-ttu-id="79f6f-144">下列案例不需要額外的設定，但它們取決於需要 Web 伺服陣列設定的技術。</span><span class="sxs-lookup"><span data-stu-id="79f6f-144">The following scenarios don't require additional configuration, but they depend on technologies that require configuration for web farms.</span></span>

| <span data-ttu-id="79f6f-145">案例</span><span class="sxs-lookup"><span data-stu-id="79f6f-145">Scenario</span></span> | <span data-ttu-id="79f6f-146">相依於 &hellip;</span><span class="sxs-lookup"><span data-stu-id="79f6f-146">Depends on &hellip;</span></span> |
| -------- | ------------------- |
| <span data-ttu-id="79f6f-147">驗證</span><span class="sxs-lookup"><span data-stu-id="79f6f-147">Authentication</span></span> | <span data-ttu-id="79f6f-148">資料保護 (請參閱 <xref:security/data-protection/configuration/overview>)。</span><span class="sxs-lookup"><span data-stu-id="79f6f-148">Data Protection (see <xref:security/data-protection/configuration/overview>).</span></span><br><br><span data-ttu-id="79f6f-149">如需詳細資訊，請參閱 <xref:security/authentication/cookie> 和 <xref:security/cookie-sharing>。</span><span class="sxs-lookup"><span data-stu-id="79f6f-149">For more information, see <xref:security/authentication/cookie> and <xref:security/cookie-sharing>.</span></span> |
| Identity | <span data-ttu-id="79f6f-150">驗證及資料庫設定。</span><span class="sxs-lookup"><span data-stu-id="79f6f-150">Authentication and database configuration.</span></span><br><br><span data-ttu-id="79f6f-151">如需詳細資訊，請參閱 <xref:security/authentication/identity> 。</span><span class="sxs-lookup"><span data-stu-id="79f6f-151">For more information, see <xref:security/authentication/identity>.</span></span> |
| <span data-ttu-id="79f6f-152">工作階段</span><span class="sxs-lookup"><span data-stu-id="79f6f-152">Session</span></span> | <span data-ttu-id="79f6f-153">資料保護 (加密 cookie 的)  (查看 <xref:security/data-protection/configuration/overview>) 和快取 (請參閱 <xref:performance/caching/distributed>) 。</span><span class="sxs-lookup"><span data-stu-id="79f6f-153">Data Protection (encrypted cookies) (see <xref:security/data-protection/configuration/overview>) and Caching (see <xref:performance/caching/distributed>).</span></span><br><br><span data-ttu-id="79f6f-154">如需詳細資訊，請參閱 [會話和狀態管理：會話狀態](xref:fundamentals/app-state#session-state)。</span><span class="sxs-lookup"><span data-stu-id="79f6f-154">For more information, see [Session and state management: Session state](xref:fundamentals/app-state#session-state).</span></span> |
| <span data-ttu-id="79f6f-155">TempData</span><span class="sxs-lookup"><span data-stu-id="79f6f-155">TempData</span></span> | <span data-ttu-id="79f6f-156"> (加密的資料保護 cookie)  (查看 <xref:security/data-protection/configuration/overview>) 或會話 (查看 [會話和狀態管理：會話狀態](xref:fundamentals/app-state#session-state)) 。</span><span class="sxs-lookup"><span data-stu-id="79f6f-156">Data Protection (encrypted cookies) (see <xref:security/data-protection/configuration/overview>) or Session (see [Session and state management: Session state](xref:fundamentals/app-state#session-state)).</span></span><br><br><span data-ttu-id="79f6f-157">如需詳細資訊，請參閱 [會話和狀態管理： TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="79f6f-157">For more information, see [Session and state management: TempData](xref:fundamentals/app-state#tempdata).</span></span> |
| <span data-ttu-id="79f6f-158">防偽</span><span class="sxs-lookup"><span data-stu-id="79f6f-158">Anti-forgery</span></span> | <span data-ttu-id="79f6f-159">資料保護 (請參閱 <xref:security/data-protection/configuration/overview>)。</span><span class="sxs-lookup"><span data-stu-id="79f6f-159">Data Protection (see <xref:security/data-protection/configuration/overview>).</span></span><br><br><span data-ttu-id="79f6f-160">如需詳細資訊，請參閱 <xref:security/anti-request-forgery> 。</span><span class="sxs-lookup"><span data-stu-id="79f6f-160">For more information, see <xref:security/anti-request-forgery>.</span></span> |

## <a name="troubleshoot"></a><span data-ttu-id="79f6f-161">疑難排解</span><span class="sxs-lookup"><span data-stu-id="79f6f-161">Troubleshoot</span></span>

### <a name="data-protection-and-caching"></a><span data-ttu-id="79f6f-162">資料保護和快取</span><span class="sxs-lookup"><span data-stu-id="79f6f-162">Data Protection and caching</span></span>

<span data-ttu-id="79f6f-163">如果未為 Web 伺服陣列環境設定資料保護或快取，則在處理要求時，會發生間歇性的錯誤。</span><span class="sxs-lookup"><span data-stu-id="79f6f-163">When Data Protection or caching isn't configured for a web farm environment, intermittent errors occur when requests are processed.</span></span> <span data-ttu-id="79f6f-164">發生這種情況是因為節點不會共用相同的資源，且使用者的要求並不是一律路由傳送回相同的節點。</span><span class="sxs-lookup"><span data-stu-id="79f6f-164">This occurs because nodes don't share the same resources and user requests aren't always routed back to the same node.</span></span>

<span data-ttu-id="79f6f-165">請考慮使用驗證登入應用程式的使用者 cookie 。</span><span class="sxs-lookup"><span data-stu-id="79f6f-165">Consider a user who signs into the app using cookie authentication.</span></span> <span data-ttu-id="79f6f-166">使用者在某個 Web 伺服陣列節點上登入應用程式。</span><span class="sxs-lookup"><span data-stu-id="79f6f-166">The user signs into the app on one web farm node.</span></span> <span data-ttu-id="79f6f-167">如果其下一個要求抵達其登入的相同節點，則應用程式可以將驗證解密 cookie ，並允許存取應用程式的資源。</span><span class="sxs-lookup"><span data-stu-id="79f6f-167">If their next request arrives at the same node where they signed in, the app is able to decrypt the authentication cookie and allows access to the app's resource.</span></span> <span data-ttu-id="79f6f-168">如果其下一個要求抵達不同的節點，應用程式就無法 cookie 從使用者登入的節點解密驗證，且要求的資源的授權會失敗。</span><span class="sxs-lookup"><span data-stu-id="79f6f-168">If their next request arrives at a different node, the app can't decrypt the authentication cookie from the node where the user signed in, and authorization for the requested resource fails.</span></span>

<span data-ttu-id="79f6f-169">如果 **間歇性** 發生下列任何徵兆，則問題通常可追溯到 Web 伺服陣列環境的不正確資料保護或快取設定：</span><span class="sxs-lookup"><span data-stu-id="79f6f-169">When any of the following symptoms occur **intermittently**, the problem is usually traced to improper Data Protection or caching configuration for a web farm environment:</span></span>

* <span data-ttu-id="79f6f-170">驗證中斷：驗證設定 cookie 不正確或無法解密。</span><span class="sxs-lookup"><span data-stu-id="79f6f-170">Authentication breaks: The authentication cookie is misconfigured or can't be decrypted.</span></span> <span data-ttu-id="79f6f-171">OAuth (Facebook、Microsoft、Twitter) 或 OpenIdConnect 登入失敗並出現錯誤「相互關聯失敗」。</span><span class="sxs-lookup"><span data-stu-id="79f6f-171">OAuth (Facebook, Microsoft, Twitter) or OpenIdConnect logins fail with the error "Correlation failed."</span></span>
* <span data-ttu-id="79f6f-172">授權中斷： Identity 會遺失。</span><span class="sxs-lookup"><span data-stu-id="79f6f-172">Authorization breaks: Identity is lost.</span></span>
* <span data-ttu-id="79f6f-173">工作階段狀態將會遺失資料。</span><span class="sxs-lookup"><span data-stu-id="79f6f-173">Session state loses data.</span></span>
* <span data-ttu-id="79f6f-174">快取項目會消失。</span><span class="sxs-lookup"><span data-stu-id="79f6f-174">Cached items disappear.</span></span>
* <span data-ttu-id="79f6f-175">TempData 將會失敗。</span><span class="sxs-lookup"><span data-stu-id="79f6f-175">TempData fails.</span></span>
* <span data-ttu-id="79f6f-176">文章失敗：防偽檢查失敗。</span><span class="sxs-lookup"><span data-stu-id="79f6f-176">POSTs fail: The anti-forgery check fails.</span></span>

<span data-ttu-id="79f6f-177">如需有關 Web 伺服陣列部署的資料保護設定詳細資訊，請參閱 <xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="79f6f-177">For more information on Data Protection configuration for web farm deployments, see <xref:security/data-protection/configuration/overview>.</span></span> <span data-ttu-id="79f6f-178">如需有關 Web 伺服陣列部署的快取設定詳細資訊，請參閱 <xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="79f6f-178">For more information on caching configuration for web farm deployments, see <xref:performance/caching/distributed>.</span></span>

## <a name="obtain-data-from-apps"></a><span data-ttu-id="79f6f-179">從應用程式取得資料</span><span class="sxs-lookup"><span data-stu-id="79f6f-179">Obtain data from apps</span></span>

<span data-ttu-id="79f6f-180">若 Web 伺服器陣列應用程式能夠回應要求、請使用終端機內嵌中介軟體從應用程式取得要求、連線與額外資料。</span><span class="sxs-lookup"><span data-stu-id="79f6f-180">If the web farm apps are capable of responding to requests, obtain request, connection, and additional data from the apps using terminal inline middleware.</span></span> <span data-ttu-id="79f6f-181">如如需詳細資訊與範例程式碼，請參閱 <xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="79f6f-181">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79f6f-182">其他資源</span><span class="sxs-lookup"><span data-stu-id="79f6f-182">Additional resources</span></span>

* <span data-ttu-id="79f6f-183">[適用于 Windows 的自訂腳本擴充](/azure/virtual-machines/extensions/custom-script-windows)功能：在 Azure 虛擬機器上下載並執行腳本，這些腳本適用于部署後設定和軟體安裝。</span><span class="sxs-lookup"><span data-stu-id="79f6f-183">[Custom Script Extension for Windows](/azure/virtual-machines/extensions/custom-script-windows): Downloads and executes scripts on Azure virtual machines, which is useful for post-deployment configuration and software installation.</span></span>
* <xref:host-and-deploy/proxy-load-balancer>
 