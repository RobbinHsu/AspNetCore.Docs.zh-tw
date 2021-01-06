---
title: ASP.NET Core 3.1 的新功能
author: rick-anderson
description: 瞭解 ASP.NET Core 3.1 中的新功能。
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: dd012a2104f574865ed577ab3c0e81dc9cc9596d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94431013"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="2773f-103">ASP.NET Core 3.1 的新功能</span><span class="sxs-lookup"><span data-stu-id="2773f-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="2773f-104">本文將重點說明 ASP.NET Core 3.1 中最重要的變更，以及相關檔的連結。</span><span class="sxs-lookup"><span data-stu-id="2773f-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-no-locrazor-components"></a><span data-ttu-id="2773f-105">元件的部分類別支援 Razor</span><span class="sxs-lookup"><span data-stu-id="2773f-105">Partial class support for Razor components</span></span>

<span data-ttu-id="2773f-106">Razor 元件現在會以部分類別的形式產生。</span><span class="sxs-lookup"><span data-stu-id="2773f-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="2773f-107">您 Razor 可以使用定義為部分類別的程式碼後端檔案來撰寫元件的程式碼，而不是在單一檔案中定義元件的所有程式碼。</span><span class="sxs-lookup"><span data-stu-id="2773f-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="2773f-108">如需詳細資訊，請參閱 [部分類別支援](xref:blazor/components/index#partial-class-support)。</span><span class="sxs-lookup"><span data-stu-id="2773f-108">For more information, see [Partial class support](xref:blazor/components/index#partial-class-support).</span></span>

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a><span data-ttu-id="2773f-109">Blazor 元件標記協助程式並將參數傳遞給最上層元件</span><span class="sxs-lookup"><span data-stu-id="2773f-109">Blazor Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="2773f-110">在 Blazor ASP.NET Core 3.0 中，元件是使用 HTML 協助程式 () 轉譯成頁面和瀏覽器 `Html.RenderComponentAsync` 。</span><span class="sxs-lookup"><span data-stu-id="2773f-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="2773f-111">在 ASP.NET Core 3.1 中，使用新的元件標記協助程式從頁面或視圖轉譯元件：</span><span class="sxs-lookup"><span data-stu-id="2773f-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="2773f-112">ASP.NET Core 3.1 仍支援 HTML Helper，但建議使用元件標記協助程式。</span><span class="sxs-lookup"><span data-stu-id="2773f-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

<span data-ttu-id="2773f-113">Blazor Server 應用程式現在可以在初始轉譯期間，將參數傳遞至最上層元件。</span><span class="sxs-lookup"><span data-stu-id="2773f-113">Blazor Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="2773f-114">之前，您只能使用 [RenderMode](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static)將參數傳遞至最上層元件。</span><span class="sxs-lookup"><span data-stu-id="2773f-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="2773f-115">在此版本中，支援 [RenderMode](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) 和 [RenderMode](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) 。</span><span class="sxs-lookup"><span data-stu-id="2773f-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="2773f-116">任何指定的參數值會序列化為 JSON，並包含在初始回應中。</span><span class="sxs-lookup"><span data-stu-id="2773f-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="2773f-117">例如， `Counter` 具有 () 遞增數量的元件 `IncrementAmount` ：</span><span class="sxs-lookup"><span data-stu-id="2773f-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="2773f-118">如需詳細資訊，請參閱將 [元件整合至 Razor 頁面和 MVC 應用程式](xref:blazor/components/prerendering-and-integration)。</span><span class="sxs-lookup"><span data-stu-id="2773f-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/components/prerendering-and-integration).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="2773f-119">支援 HTTP.sys 中的共用佇列</span><span class="sxs-lookup"><span data-stu-id="2773f-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="2773f-120">[HTTP.sys](xref:fundamentals/servers/httpsys) 支援建立匿名要求佇列。</span><span class="sxs-lookup"><span data-stu-id="2773f-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="2773f-121">在 ASP.NET Core 3.1 中，我們已新增建立或附加至現有命名 HTTP.sys 要求佇列的功能。</span><span class="sxs-lookup"><span data-stu-id="2773f-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="2773f-122">建立或附加至現有的已命名 HTTP.sys 要求佇列，可讓擁有佇列 HTTP.sys 控制器進程與接聽程式進程無關的情況。</span><span class="sxs-lookup"><span data-stu-id="2773f-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="2773f-123">這種獨立性讓您能夠在接聽程式進程重新開機之間保留現有的連接和排入佇列的要求：</span><span class="sxs-lookup"><span data-stu-id="2773f-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a><span data-ttu-id="2773f-124">SameSite s 的重大變更 cookie</span><span class="sxs-lookup"><span data-stu-id="2773f-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="2773f-125">SameSite s 的行為 cookie 已變更，以反映即將進行的瀏覽器變更。</span><span class="sxs-lookup"><span data-stu-id="2773f-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="2773f-126">這可能會影響 AzureAd、OpenIdConnect 或 WsFederation 等驗證案例。</span><span class="sxs-lookup"><span data-stu-id="2773f-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="2773f-127">如需詳細資訊，請參閱 <xref:security/samesite> 。</span><span class="sxs-lookup"><span data-stu-id="2773f-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a><span data-ttu-id="2773f-128">防止應用程式中事件的預設動作 Blazor</span><span class="sxs-lookup"><span data-stu-id="2773f-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="2773f-129">使用指示詞 `@on{EVENT}:preventDefault` 屬性以防止事件的預設動作。</span><span class="sxs-lookup"><span data-stu-id="2773f-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="2773f-130">在下列範例中，會防止在文字方塊中顯示金鑰字元的預設動作：</span><span class="sxs-lookup"><span data-stu-id="2773f-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="2773f-131">如需詳細資訊，請參閱 [防止預設動作](xref:blazor/components/event-handling#prevent-default-actions)。</span><span class="sxs-lookup"><span data-stu-id="2773f-131">For more information, see [Prevent default actions](xref:blazor/components/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-no-locblazor-apps"></a><span data-ttu-id="2773f-132">停止應用程式中的事件傳播 Blazor</span><span class="sxs-lookup"><span data-stu-id="2773f-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="2773f-133">使用指示詞 `@on{EVENT}:stopPropagation` 屬性停止事件傳播。</span><span class="sxs-lookup"><span data-stu-id="2773f-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="2773f-134">在下列範例中，選取此核取方塊可防止從子系的 click 事件 `<div>` 傳播至父系 `<div>` ：</span><span class="sxs-lookup"><span data-stu-id="2773f-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="2773f-135">如需詳細資訊，請參閱 [停止事件傳播](xref:blazor/components/event-handling#stop-event-propagation)。</span><span class="sxs-lookup"><span data-stu-id="2773f-135">For more information, see [Stop event propagation](xref:blazor/components/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-no-locblazor-app-development"></a><span data-ttu-id="2773f-136">應用程式開發期間的詳細錯誤 Blazor</span><span class="sxs-lookup"><span data-stu-id="2773f-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="2773f-137">當 Blazor 應用程式在開發期間無法正常運作時，從應用程式接收詳細的錯誤資訊有助於疑難排解和修正問題。</span><span class="sxs-lookup"><span data-stu-id="2773f-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="2773f-138">發生錯誤時， Blazor 應用程式會在畫面底部顯示金色橫條：</span><span class="sxs-lookup"><span data-stu-id="2773f-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="2773f-139">在開發期間，金線會將您導向瀏覽器主控台，您可以在其中查看例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2773f-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="2773f-140">在生產環境中，金級橫條會通知使用者發生錯誤，建議您重新整理瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="2773f-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="2773f-141">如需詳細資訊，請參閱 [開發期間的詳細錯誤](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development)。</span><span class="sxs-lookup"><span data-stu-id="2773f-141">For more information, see [Detailed errors during development](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span></span>
