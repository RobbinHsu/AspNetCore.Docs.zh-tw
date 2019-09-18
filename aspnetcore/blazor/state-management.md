---
title: ASP.NET Core Blazor 狀態管理
author: guardrex
description: 瞭解如何在 Blazor 伺服器應用程式中保存狀態。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/state-management
ms.openlocfilehash: e1c3b030f466a820d49c36839d7ee26bb7cea4d3
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963858"
---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="57068-103">ASP.NET Core Blazor 狀態管理</span><span class="sxs-lookup"><span data-stu-id="57068-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="57068-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="57068-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="57068-105">Blazor 伺服器是可設定狀態的應用程式架構。</span><span class="sxs-lookup"><span data-stu-id="57068-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="57068-106">在大部分的情況下，應用程式會維護與伺服器之間的持續連接。</span><span class="sxs-lookup"><span data-stu-id="57068-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="57068-107">使用者的狀態會保留在伺服器的記憶體中。</span><span class="sxs-lookup"><span data-stu-id="57068-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="57068-108">保留給使用者線路的狀態範例包括：</span><span class="sxs-lookup"><span data-stu-id="57068-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="57068-109">呈現的 UI&mdash;是元件實例和其最新轉譯輸出的階層。</span><span class="sxs-lookup"><span data-stu-id="57068-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="57068-110">元件實例中任何欄位和屬性的值。</span><span class="sxs-lookup"><span data-stu-id="57068-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="57068-111">保留在範圍為線路之相依性[插入（DI）](xref:fundamentals/dependency-injection)服務實例中的資料。</span><span class="sxs-lookup"><span data-stu-id="57068-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="57068-112">本文解決 Blazor 伺服器應用程式中的狀態持續性。</span><span class="sxs-lookup"><span data-stu-id="57068-112">This article addresses state persistence in Blazor Server apps.</span></span> <span data-ttu-id="57068-113">Blazor WebAssembly apps 可以利用[瀏覽器中的用戶端狀態持續](#client-side-in-the-browser)性，但需要的自訂解決方案或協力廠商套件超出本文的範圍。</span><span class="sxs-lookup"><span data-stu-id="57068-113">Blazor WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a><span data-ttu-id="57068-114">Blazor 線路</span><span class="sxs-lookup"><span data-stu-id="57068-114">Blazor circuits</span></span>

<span data-ttu-id="57068-115">如果使用者遇到暫時性的網路連線遺失，Blazor 會嘗試將使用者重新連接到其原始線路，讓他們可以繼續使用應用程式。</span><span class="sxs-lookup"><span data-stu-id="57068-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="57068-116">不過，不一定能夠將使用者重新連接到伺服器記憶體中的原始線路：</span><span class="sxs-lookup"><span data-stu-id="57068-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="57068-117">伺服器無法永久保留中斷連線的線路。</span><span class="sxs-lookup"><span data-stu-id="57068-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="57068-118">伺服器必須在超時之後或伺服器處於記憶體不足的壓力時，釋放中斷連線的線路。</span><span class="sxs-lookup"><span data-stu-id="57068-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="57068-119">在多伺服器、負載平衡的部署環境中，任何指定的時間都可能無法使用任何伺服器處理要求。</span><span class="sxs-lookup"><span data-stu-id="57068-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="57068-120">當不再需要處理要求的整體量時，個別伺服器可能會失敗或自動移除。</span><span class="sxs-lookup"><span data-stu-id="57068-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="57068-121">當使用者嘗試重新連線時，源伺服器可能無法使用。</span><span class="sxs-lookup"><span data-stu-id="57068-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="57068-122">使用者可能會關閉並重新開啟其瀏覽器或重載頁面，這會移除瀏覽器記憶體中保留的任何狀態。</span><span class="sxs-lookup"><span data-stu-id="57068-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="57068-123">例如，透過 JavaScript interop 呼叫所設定的值會遺失。</span><span class="sxs-lookup"><span data-stu-id="57068-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="57068-124">當使用者無法重新連線到其原始線路時，使用者會收到空白狀態的新線路。</span><span class="sxs-lookup"><span data-stu-id="57068-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="57068-125">這相當於關閉和重新開啟桌面應用程式。</span><span class="sxs-lookup"><span data-stu-id="57068-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="57068-126">跨線路保留狀態</span><span class="sxs-lookup"><span data-stu-id="57068-126">Preserve state across circuits</span></span>

<span data-ttu-id="57068-127">在某些情況下，您需要保留各線路的狀態。</span><span class="sxs-lookup"><span data-stu-id="57068-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="57068-128">應用程式可以保留使用者的重要資料，如果：</span><span class="sxs-lookup"><span data-stu-id="57068-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="57068-129">網頁伺服器變得無法使用。</span><span class="sxs-lookup"><span data-stu-id="57068-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="57068-130">使用者的瀏覽器會強制使用新的 web 伺服器來啟動新的線路。</span><span class="sxs-lookup"><span data-stu-id="57068-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="57068-131">一般而言，跨線路維護狀態適用于使用者主動建立資料的情況，而不只是讀取已經存在的資料。</span><span class="sxs-lookup"><span data-stu-id="57068-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="57068-132">若要保留超過單一線路的狀態，*請勿只將資料儲存在伺服器的記憶體中*。</span><span class="sxs-lookup"><span data-stu-id="57068-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="57068-133">應用程式必須將資料保存到其他儲存位置。</span><span class="sxs-lookup"><span data-stu-id="57068-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="57068-134">狀態持續性不&mdash;是自動的，您必須在開發應用程式時採取步驟來執行具狀態的資料持續性。</span><span class="sxs-lookup"><span data-stu-id="57068-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="57068-135">通常只有在高價值的狀態下，使用者才需要建立資料持續性。</span><span class="sxs-lookup"><span data-stu-id="57068-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="57068-136">在下列範例中，保存狀態可以節省商務工作的時間或輔助：</span><span class="sxs-lookup"><span data-stu-id="57068-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="57068-137">多步驟 webform &ndash;當使用者的狀態遺失時，針對多步驟程式的數個已完成步驟重新輸入資料非常耗時。</span><span class="sxs-lookup"><span data-stu-id="57068-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="57068-138">如果使用者離開多步驟表單，並在稍後返回表單，則會在此案例中失去狀態。</span><span class="sxs-lookup"><span data-stu-id="57068-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="57068-139">購物車&ndash;可以維護應用程式中代表潛在收益的任何商業重要元件。</span><span class="sxs-lookup"><span data-stu-id="57068-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="57068-140">失去其狀態的使用者，因此其購物車，可能會在日後返回網站時購買較少的產品或服務。</span><span class="sxs-lookup"><span data-stu-id="57068-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="57068-141">通常不需要保留容易重新建立的狀態，例如輸入登入對話方塊中未提交的使用者名稱。</span><span class="sxs-lookup"><span data-stu-id="57068-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="57068-142">應用程式只能保存*應用程式狀態*。</span><span class="sxs-lookup"><span data-stu-id="57068-142">An app can only persist *app state*.</span></span> <span data-ttu-id="57068-143">Ui 無法保存，例如元件實例和其呈現樹狀結構。</span><span class="sxs-lookup"><span data-stu-id="57068-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="57068-144">元件和轉譯樹狀結構通常不是可序列化的。</span><span class="sxs-lookup"><span data-stu-id="57068-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="57068-145">若要保存類似于 UI 狀態的內容（例如 TreeView 的展開節點），應用程式必須有自訂程式碼，以將行為模型化為可序列化的應用程式狀態。</span><span class="sxs-lookup"><span data-stu-id="57068-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="57068-146">保存狀態的位置</span><span class="sxs-lookup"><span data-stu-id="57068-146">Where to persist state</span></span>

<span data-ttu-id="57068-147">在 Blazor 伺服器應用程式中保存狀態有三個常見的位置。</span><span class="sxs-lookup"><span data-stu-id="57068-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="57068-148">每個方法最適合不同的案例，而且有不同的注意事項：</span><span class="sxs-lookup"><span data-stu-id="57068-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="57068-149">資料庫中的伺服器端</span><span class="sxs-lookup"><span data-stu-id="57068-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="57068-150">URL</span><span class="sxs-lookup"><span data-stu-id="57068-150">URL</span></span>](#url)
* [<span data-ttu-id="57068-151">瀏覽器中的用戶端</span><span class="sxs-lookup"><span data-stu-id="57068-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="57068-152">資料庫中的伺服器端</span><span class="sxs-lookup"><span data-stu-id="57068-152">Server-side in a database</span></span>

<span data-ttu-id="57068-153">針對永久資料保存或任何必須跨越多個使用者或裝置的資料，獨立的伺服器端資料庫幾乎都是最好的選擇。</span><span class="sxs-lookup"><span data-stu-id="57068-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="57068-154">這些選項包括：</span><span class="sxs-lookup"><span data-stu-id="57068-154">Options include:</span></span>

* <span data-ttu-id="57068-155">關係 SQL 資料庫</span><span class="sxs-lookup"><span data-stu-id="57068-155">Relational SQL database</span></span>
* <span data-ttu-id="57068-156">索引鍵/值存放區</span><span class="sxs-lookup"><span data-stu-id="57068-156">Key-value store</span></span>
* <span data-ttu-id="57068-157">Blob 存放區</span><span class="sxs-lookup"><span data-stu-id="57068-157">Blob store</span></span>
* <span data-ttu-id="57068-158">資料表存放區</span><span class="sxs-lookup"><span data-stu-id="57068-158">Table store</span></span>

<span data-ttu-id="57068-159">將資料儲存在資料庫之後，使用者可以隨時啟動新的電路。</span><span class="sxs-lookup"><span data-stu-id="57068-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="57068-160">使用者的資料會保留在任何新的線路中並可供使用。</span><span class="sxs-lookup"><span data-stu-id="57068-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="57068-161">如需 Azure 資料儲存體選項的詳細資訊，請參閱[Azure 儲存體檔](/azure/storage/)和[azure 資料庫](https://azure.microsoft.com/product-categories/databases/)。</span><span class="sxs-lookup"><span data-stu-id="57068-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="57068-162">URL</span><span class="sxs-lookup"><span data-stu-id="57068-162">URL</span></span>

<span data-ttu-id="57068-163">對於代表導覽狀態的暫時性資料，請將資料模型為 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="57068-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="57068-164">在 URL 中模型化的狀態範例包括：</span><span class="sxs-lookup"><span data-stu-id="57068-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="57068-165">已查看之實體的識別碼。</span><span class="sxs-lookup"><span data-stu-id="57068-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="57068-166">分頁方格中的目前頁碼。</span><span class="sxs-lookup"><span data-stu-id="57068-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="57068-167">瀏覽器的網址列內容會保留：</span><span class="sxs-lookup"><span data-stu-id="57068-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="57068-168">如果使用者手動重載頁面。</span><span class="sxs-lookup"><span data-stu-id="57068-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="57068-169">如果 web 伺服器變成無法使用&mdash;，則會強制使用者重載頁面，以便連接到不同的伺服器。</span><span class="sxs-lookup"><span data-stu-id="57068-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="57068-170">如需使用`@page`指示詞定義 URL 模式的詳細資訊<xref:blazor/routing>，請參閱。</span><span class="sxs-lookup"><span data-stu-id="57068-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="57068-171">瀏覽器中的用戶端</span><span class="sxs-lookup"><span data-stu-id="57068-171">Client-side in the browser</span></span>

<span data-ttu-id="57068-172">對於使用者主動建立的暫時性資料，常見的備份存放區是瀏覽器的`localStorage`和`sessionStorage`集合。</span><span class="sxs-lookup"><span data-stu-id="57068-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="57068-173">若已放棄迴圈，則不需要應用程式來管理或清除已儲存的狀態，這是優於伺服器端儲存體的優點。</span><span class="sxs-lookup"><span data-stu-id="57068-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="57068-174">本節中的「用戶端」指的是瀏覽器中的用戶端案例，而不是[Blazor WebAssembly 裝載模型](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="57068-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="57068-175">`localStorage`和`sessionStorage`可以在 Blazor WebAssembly apps 中使用，但只能透過撰寫自訂程式碼或使用協力廠商套件。</span><span class="sxs-lookup"><span data-stu-id="57068-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="57068-176">`localStorage`和`sessionStorage`的差異如下：</span><span class="sxs-lookup"><span data-stu-id="57068-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="57068-177">`localStorage`的範圍設定為使用者的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="57068-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="57068-178">如果使用者重載頁面，或關閉並重新開啟瀏覽器，則狀態會保持不變。</span><span class="sxs-lookup"><span data-stu-id="57068-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="57068-179">如果使用者開啟多個瀏覽器索引標籤，則狀態會在索引標籤之間共用。</span><span class="sxs-lookup"><span data-stu-id="57068-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="57068-180">資料會保存`localStorage`在中，直到明確清除為止。</span><span class="sxs-lookup"><span data-stu-id="57068-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="57068-181">`sessionStorage`的範圍設定為使用者的瀏覽器索引標籤。如果使用者重載索引標籤，則狀態會保持不變。</span><span class="sxs-lookup"><span data-stu-id="57068-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="57068-182">如果使用者關閉索引標籤或瀏覽器，則狀態會遺失。</span><span class="sxs-lookup"><span data-stu-id="57068-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="57068-183">如果使用者開啟多個瀏覽器索引標籤，每個索引標籤都有自己獨立的資料版本。</span><span class="sxs-lookup"><span data-stu-id="57068-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="57068-184">一般而言， `sessionStorage`使用較安全。</span><span class="sxs-lookup"><span data-stu-id="57068-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="57068-185">`sessionStorage`避免使用者開啟多個索引標籤並遇到下列問題的風險：</span><span class="sxs-lookup"><span data-stu-id="57068-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="57068-186">索引標籤上狀態儲存體中的 bug。</span><span class="sxs-lookup"><span data-stu-id="57068-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="57068-187">當索引標籤覆寫其他索引標籤的狀態時，會造成混淆的行為。</span><span class="sxs-lookup"><span data-stu-id="57068-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="57068-188">`localStorage`如果應用程式必須在關閉並重新開啟瀏覽器時保存狀態，這是較好的選擇。</span><span class="sxs-lookup"><span data-stu-id="57068-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="57068-189">使用瀏覽器儲存的注意事項：</span><span class="sxs-lookup"><span data-stu-id="57068-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="57068-190">類似于使用伺服器端資料庫，載入和儲存資料是非同步。</span><span class="sxs-lookup"><span data-stu-id="57068-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="57068-191">與伺服器端資料庫不同的是，預先呈現期間無法使用儲存體，因為在預先呈現階段期間，瀏覽器中不存在要求的頁面。</span><span class="sxs-lookup"><span data-stu-id="57068-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="57068-192">儲存幾 kb 的資料可合理保存 Blazor 伺服器應用程式。</span><span class="sxs-lookup"><span data-stu-id="57068-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="57068-193">除了幾 kb 以外，您還必須考慮效能上的影響，因為資料是透過網路載入和儲存。</span><span class="sxs-lookup"><span data-stu-id="57068-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="57068-194">使用者可能會看到或篡改資料。</span><span class="sxs-lookup"><span data-stu-id="57068-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="57068-195">ASP.NET Core[資料保護](xref:security/data-protection/introduction)可以降低風險。</span><span class="sxs-lookup"><span data-stu-id="57068-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="57068-196">協力廠商瀏覽器儲存解決方案</span><span class="sxs-lookup"><span data-stu-id="57068-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="57068-197">協力廠商 NuGet 套件提供使用`localStorage`和`sessionStorage`的 api。</span><span class="sxs-lookup"><span data-stu-id="57068-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="57068-198">值得考慮選擇以透明方式使用 ASP.NET Core[資料保護](xref:security/data-protection/introduction)的套件。</span><span class="sxs-lookup"><span data-stu-id="57068-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="57068-199">ASP.NET Core 資料保護會將儲存的資料加密，並降低篡改已儲存資料的潛在風險。</span><span class="sxs-lookup"><span data-stu-id="57068-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="57068-200">如果 JSON 序列化資料是以純文字儲存，則使用者可以使用瀏覽器開發人員工具來查看資料，同時也會修改儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="57068-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="57068-201">保護資料不一定會有問題，因為資料在本質上可能是很簡單的。</span><span class="sxs-lookup"><span data-stu-id="57068-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="57068-202">例如，讀取或修改 UI 專案的預存色彩對使用者或組織而言並不會有嚴重的安全性風險。</span><span class="sxs-lookup"><span data-stu-id="57068-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="57068-203">避免允許使用者檢查或篡改*敏感性資料*。</span><span class="sxs-lookup"><span data-stu-id="57068-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="57068-204">受保護的瀏覽器儲存體實驗性封裝</span><span class="sxs-lookup"><span data-stu-id="57068-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="57068-205">提供`localStorage`和[資料保護](xref:security/data-protection/introduction)的 NuGet 套件範例是 AspNetCore.`sessionStorage` [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)。</span><span class="sxs-lookup"><span data-stu-id="57068-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="57068-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`是不支援的實驗性封裝，目前不適用於生產環境使用。</span><span class="sxs-lookup"><span data-stu-id="57068-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="57068-207">安裝</span><span class="sxs-lookup"><span data-stu-id="57068-207">Installation</span></span>

<span data-ttu-id="57068-208">若要安裝`Microsoft.AspNetCore.ProtectedBrowserStorage`套件：</span><span class="sxs-lookup"><span data-stu-id="57068-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="57068-209">在 Blazor 伺服器應用程式專案中，新增[AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)的套件參考。</span><span class="sxs-lookup"><span data-stu-id="57068-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="57068-210">在最上層的 HTML 中（例如，在預設專案範本的*Pages/_Host. cshtml*檔案中），新增下列`<script>`標記：</span><span class="sxs-lookup"><span data-stu-id="57068-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="57068-211">在方法中，呼叫`AddProtectedBrowserStorage`以將`localStorage`和`sessionStorage`服務新增至服務集合： `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="57068-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="57068-212">儲存和載入元件中的資料</span><span class="sxs-lookup"><span data-stu-id="57068-212">Save and load data within a component</span></span>

<span data-ttu-id="57068-213">在需要將資料載入或儲存至瀏覽器儲存體的任何元件[@inject](xref:blazor/dependency-injection#request-a-service-in-a-component)中，使用來插入下列任一項的實例：</span><span class="sxs-lookup"><span data-stu-id="57068-213">In any component that requires loading or saving data to browser storage, use [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="57068-214">選擇取決於您想要使用的備份存放區。</span><span class="sxs-lookup"><span data-stu-id="57068-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="57068-215">在下列範例中， `sessionStorage`會使用：</span><span class="sxs-lookup"><span data-stu-id="57068-215">In the following example, `sessionStorage` is used:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="57068-216">語句可以放入 _Imports 的 razor 檔案，而不是元件中的。 `@using`</span><span class="sxs-lookup"><span data-stu-id="57068-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="57068-217">使用 *_Imports*檔案可讓應用程式或整個應用程式的較大區段使用命名空間。</span><span class="sxs-lookup"><span data-stu-id="57068-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="57068-218">若要將`currentCount`值保存`Counter`在專案範本的元件中，請修改`IncrementCount`要使用`ProtectedSessionStore.SetAsync`的方法：</span><span class="sxs-lookup"><span data-stu-id="57068-218">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="57068-219">在更大、更實際的應用程式中，個別欄位的儲存是不太可能發生的情況。</span><span class="sxs-lookup"><span data-stu-id="57068-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="57068-220">應用程式較可能儲存包含複雜狀態的整個模型物件。</span><span class="sxs-lookup"><span data-stu-id="57068-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="57068-221">`ProtectedSessionStore`自動序列化和還原序列化 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="57068-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="57068-222">在上述程式碼範例中， `currentCount`資料會在使用者`sessionStorage['count']`的瀏覽器中儲存為。</span><span class="sxs-lookup"><span data-stu-id="57068-222">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="57068-223">資料不會以純文字儲存，而是使用 ASP.NET Core 的[資料保護](xref:security/data-protection/introduction)來保護。</span><span class="sxs-lookup"><span data-stu-id="57068-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="57068-224">如果`sessionStorage['count']`是在瀏覽器的開發人員主控台中評估，則可以看到加密的資料。</span><span class="sxs-lookup"><span data-stu-id="57068-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="57068-225">若要在`currentCount`使用者`Counter`稍後回到元件時復原資料（包括它們是否在全新的線路上），請使用`ProtectedSessionStore.GetAsync`：</span><span class="sxs-lookup"><span data-stu-id="57068-225">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="57068-226">如果元件的參數包含導覽狀態，請呼叫`ProtectedSessionStore.GetAsync`並在中`OnParametersSetAsync`指派結果，而`OnInitializedAsync`不是。</span><span class="sxs-lookup"><span data-stu-id="57068-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="57068-227">`OnInitializedAsync`只有在第一次具現化元件時，才會呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="57068-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="57068-228">`OnInitializedAsync`如果使用者流覽至不同的 URL，但仍在相同頁面上，則不會再呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="57068-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span>

> [!WARNING]
> <span data-ttu-id="57068-229">此章節中的範例僅適用于伺服器未啟用預先安裝的情況。</span><span class="sxs-lookup"><span data-stu-id="57068-229">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="57068-230">啟用預入功能時，會產生類似下列的錯誤：</span><span class="sxs-lookup"><span data-stu-id="57068-230">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="57068-231">目前無法發出 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="57068-231">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="57068-232">這是因為正在資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="57068-232">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="57068-233">請停用已選擇的，或加入額外的程式碼以使用可處理的。</span><span class="sxs-lookup"><span data-stu-id="57068-233">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="57068-234">若要深入瞭解如何撰寫可搭配已預呈現運作的程式碼，請參閱[處理預呈現](#handle-prerendering)一節。</span><span class="sxs-lookup"><span data-stu-id="57068-234">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="57068-235">處理載入狀態</span><span class="sxs-lookup"><span data-stu-id="57068-235">Handle the loading state</span></span>

<span data-ttu-id="57068-236">由於瀏覽器儲存體是非同步（透過網路連線存取），因此在載入資料並可供元件使用之前，一律會有一段時間。</span><span class="sxs-lookup"><span data-stu-id="57068-236">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="57068-237">若要獲得最佳結果，請在載入進行時轉譯載入狀態訊息，而不是顯示空白或預設的資料。</span><span class="sxs-lookup"><span data-stu-id="57068-237">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="57068-238">其中一種方法是追蹤資料是否為`null` （仍在載入中）。</span><span class="sxs-lookup"><span data-stu-id="57068-238">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="57068-239">在預設`Counter`元件中，計數會保留`int`在中。</span><span class="sxs-lookup"><span data-stu-id="57068-239">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="57068-240">將`currentCount`問號（`?`）新增至類型（`int`），使其成為可為 null：</span><span class="sxs-lookup"><span data-stu-id="57068-240">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="57068-241">不是無條件地顯示 [計數] 和 [**遞增**] 按鈕，而是只有在載入資料時，才選擇顯示這些元素：</span><span class="sxs-lookup"><span data-stu-id="57068-241">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="57068-242">處理已預呈現</span><span class="sxs-lookup"><span data-stu-id="57068-242">Handle prerendering</span></span>

<span data-ttu-id="57068-243">在預做期間：</span><span class="sxs-lookup"><span data-stu-id="57068-243">During prerendering:</span></span>

* <span data-ttu-id="57068-244">與使用者的瀏覽器之間的互動連接不存在。</span><span class="sxs-lookup"><span data-stu-id="57068-244">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="57068-245">瀏覽器還沒有可執行 JavaScript 程式碼的頁面。</span><span class="sxs-lookup"><span data-stu-id="57068-245">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="57068-246">`localStorage`或`sessionStorage`在預做期間無法使用。</span><span class="sxs-lookup"><span data-stu-id="57068-246">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="57068-247">如果元件嘗試與存放裝置互動，則會產生類似下列的錯誤：</span><span class="sxs-lookup"><span data-stu-id="57068-247">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="57068-248">目前無法發出 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="57068-248">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="57068-249">這是因為正在資源清單元件。</span><span class="sxs-lookup"><span data-stu-id="57068-249">This is because the component is being prerendered.</span></span>

<span data-ttu-id="57068-250">解決錯誤的其中一種方法是停用已處理的。</span><span class="sxs-lookup"><span data-stu-id="57068-250">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="57068-251">如果應用程式大量使用以瀏覽器為基礎的存放裝置，這通常是最佳的選擇。</span><span class="sxs-lookup"><span data-stu-id="57068-251">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="57068-252">因為應用程式無法提供任何有用的內容，直到`localStorage`或`sessionStorage`可供使用，因此會增加複雜性並不會讓應用程式受益。</span><span class="sxs-lookup"><span data-stu-id="57068-252">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="57068-253">若要停用已修訂，請開啟*Pages/_Host. cshtml*檔案，然後`Html.RenderComponentAsync<App>(RenderMode.Server)`將的呼叫變更為。</span><span class="sxs-lookup"><span data-stu-id="57068-253">To disable prerendering, open the *Pages/_Host.cshtml* file and change the call to `Html.RenderComponentAsync<App>(RenderMode.Server)`.</span></span>

<span data-ttu-id="57068-254">如果是其他未使用`localStorage`或的頁面，則`sessionStorage`會進行預呈現。</span><span class="sxs-lookup"><span data-stu-id="57068-254">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="57068-255">若要保持已啟用的已啟用狀態，請延遲載入作業，直到瀏覽器連線到線路為止。</span><span class="sxs-lookup"><span data-stu-id="57068-255">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="57068-256">以下是儲存計數器值的範例：</span><span class="sxs-lookup"><span data-stu-id="57068-256">The following is an example for storing a counter value:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore
@inject IComponentContext ComponentContext

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isWaitingForConnection;

    protected override async Task OnInitializedAsync()
    {
        if (ComponentContext.IsConnected)
        {
            // It looks like the app isn't prerendering, so the data can be
            // immediately loaded from browser storage.
            await LoadStateAsync();
        }
        else
        {
            // Prerendering is in progress, so the app defers the load operation
            // until later.
            isWaitingForConnection = true;
        }
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        // By this stage, the client has connected back to the server, and
        // browser services are available. If the app didn't load the data earlier,
        // the app should do so now and then trigger a new render.
        if (firstRender && isWaitingForConnection)
        {
            isWaitingForConnection = false;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="57068-257">將狀態保留分解為一般位置</span><span class="sxs-lookup"><span data-stu-id="57068-257">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="57068-258">如果許多元件都依賴以瀏覽器為基礎的儲存體，則重新執行狀態提供者程式碼很多次會建立程式碼重複。</span><span class="sxs-lookup"><span data-stu-id="57068-258">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="57068-259">避免程式碼重複的其中一個選項是建立一個可封裝狀態提供者邏輯的*狀態供應器父元件*。</span><span class="sxs-lookup"><span data-stu-id="57068-259">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="57068-260">子元件可以使用持續性資料，而不考慮狀態持續性機制。</span><span class="sxs-lookup"><span data-stu-id="57068-260">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="57068-261">在下列`CounterStateProvider`元件範例中，會保存計數器資料：</span><span class="sxs-lookup"><span data-stu-id="57068-261">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="57068-262">`CounterStateProvider`元件在載入完成之前，不會呈現其子內容來處理載入階段。</span><span class="sxs-lookup"><span data-stu-id="57068-262">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="57068-263">若要使用`CounterStateProvider`元件，請將元件的實例包裝在需要存取計數器狀態的任何其他元件周圍。</span><span class="sxs-lookup"><span data-stu-id="57068-263">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="57068-264">若要讓應用程式中的所有元件都能存取該狀態`CounterStateProvider` ，請將`Router`元件包裝`App`在元件中的周圍（*razor*）：</span><span class="sxs-lookup"><span data-stu-id="57068-264">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="57068-265">包裝的元件會接收並可修改保存的計數器狀態。</span><span class="sxs-lookup"><span data-stu-id="57068-265">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="57068-266">下列`Counter`元件會執行模式：</span><span class="sxs-lookup"><span data-stu-id="57068-266">The following `Counter` component implements the pattern:</span></span>

```cshtml
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="57068-267">先前的元件不需要與互動`ProtectedBrowserStorage`，也不會處理「載入」階段。</span><span class="sxs-lookup"><span data-stu-id="57068-267">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="57068-268">如先前所述，若要處理`CounterStateProvider`已進行的已修訂，可以修改，讓取用計數器資料的所有元件都能自動以可處理方式使用。</span><span class="sxs-lookup"><span data-stu-id="57068-268">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="57068-269">如需詳細資訊，請參閱處理預進行[處理](#handle-prerendering)一節。</span><span class="sxs-lookup"><span data-stu-id="57068-269">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="57068-270">一般情況下，建議使用*狀態供應器父元件*模式：</span><span class="sxs-lookup"><span data-stu-id="57068-270">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="57068-271">使用許多其他元件中的狀態。</span><span class="sxs-lookup"><span data-stu-id="57068-271">To consume state in many other components.</span></span>
* <span data-ttu-id="57068-272">如果只有一個最上層狀態物件要保存，則為。</span><span class="sxs-lookup"><span data-stu-id="57068-272">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="57068-273">若要保存許多不同的狀態物件，並在不同位置取用不同的物件子集，最好避免在全域處理狀態的載入和儲存。</span><span class="sxs-lookup"><span data-stu-id="57068-273">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>