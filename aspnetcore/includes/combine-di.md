---
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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536277"
---
<a name="csc"></a>

<span data-ttu-id="98c4a-101">請考慮下列 `ConfigureServices` 方法，它會註冊服務並設定選項：</span><span class="sxs-lookup"><span data-stu-id="98c4a-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="98c4a-102">您可以將相關的註冊群組移至擴充方法，以註冊服務。</span><span class="sxs-lookup"><span data-stu-id="98c4a-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="98c4a-103">例如，設定服務會新增至下列類別：</span><span class="sxs-lookup"><span data-stu-id="98c4a-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="98c4a-104">其餘的服務會註冊在類似的類別中。</span><span class="sxs-lookup"><span data-stu-id="98c4a-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="98c4a-105">下列 `ConfigureServices` 方法會使用新的擴充方法來註冊服務：</span><span class="sxs-lookup"><span data-stu-id="98c4a-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="98c4a-106">**_注意：_** 每個 `services.Add{GROUP_NAME}` 擴充方法會新增並可能設定服務。</span><span class="sxs-lookup"><span data-stu-id="98c4a-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="98c4a-107">例如， <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> 新增具有 views 需求的服務 MVC 控制器，並 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 新增服務頁面所 Razor 需的服務。</span><span class="sxs-lookup"><span data-stu-id="98c4a-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="98c4a-108">建議應用程式遵循此命名慣例。</span><span class="sxs-lookup"><span data-stu-id="98c4a-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="98c4a-109">在 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 命名空間中放置擴充方法，以封裝服務註冊群組。</span><span class="sxs-lookup"><span data-stu-id="98c4a-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
