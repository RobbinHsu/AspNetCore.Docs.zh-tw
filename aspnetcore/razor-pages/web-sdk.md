---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: 《 Web.config 簡介」。
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 8cc0a51d3d917300f3add31f5884b4784dd573dd
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059750"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="1a1c5-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="1a1c5-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="1a1c5-104">總覽</span><span class="sxs-lookup"><span data-stu-id="1a1c5-104">Overview</span></span>

<span data-ttu-id="1a1c5-105">`Microsoft.NET.Sdk.Web` 是用來建立 ASP.NET Core 應用程式的 [MSBuild 專案 SDK](/visualstudio/msbuild/how-to-use-project-sdk) 。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="1a1c5-106">您可以建立不含此 SDK 的 ASP.NET Core 應用程式，不過，Web SDK 是：</span><span class="sxs-lookup"><span data-stu-id="1a1c5-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="1a1c5-107">專為提供最先進的體驗量身打造。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="1a1c5-108">大部分使用者的建議目標。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-108">The recommended target for most users.</span></span>

<span data-ttu-id="1a1c5-109">在專案中使用 Web. SDK：</span><span class="sxs-lookup"><span data-stu-id="1a1c5-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="1a1c5-110">使用 Web SDK 啟用的功能：</span><span class="sxs-lookup"><span data-stu-id="1a1c5-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="1a1c5-111">以 .NET Core 3.0 或更新版本為目標的專案會隱含參考：</span><span class="sxs-lookup"><span data-stu-id="1a1c5-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="1a1c5-112">[ASP.NET Core 的共用架構](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="1a1c5-113">專為建立 ASP.NET Core 應用程式而設計的[分析器](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="1a1c5-114">Web SDK 會匯入可讓您使用發行設定檔，並使用 WebDeploy 發行的 MSBuild 目標。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="1a1c5-115">屬性</span><span class="sxs-lookup"><span data-stu-id="1a1c5-115">Properties</span></span>

| <span data-ttu-id="1a1c5-116">屬性</span><span class="sxs-lookup"><span data-stu-id="1a1c5-116">Property</span></span> | <span data-ttu-id="1a1c5-117">描述</span><span class="sxs-lookup"><span data-stu-id="1a1c5-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="1a1c5-118">停用共用架構的隱含參考 `Microsoft.AspNetCore.App` 。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="1a1c5-119">停用 ASP.NET Core 分析器的隱含參考。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="1a1c5-120">Razor建立 Blazor (伺服器) 應用程式時，停用元件分析器的隱含參考。</span><span class="sxs-lookup"><span data-stu-id="1a1c5-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |