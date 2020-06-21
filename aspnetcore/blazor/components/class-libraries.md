---
title: ASP.NET Core Razor 元件類別庫
author: guardrex
description: 探索如何將元件包含在 Blazor 來自外部元件程式庫的應用程式中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: ecc9873d7f652f27767df98196786d12789518c9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103630"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="ab645-103">ASP.NET Core Razor 元件類別庫</span><span class="sxs-lookup"><span data-stu-id="ab645-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="ab645-104">依[Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="ab645-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="ab645-105">元件可以在各個專案的[ Razor 類別庫（RCL）](xref:razor-pages/ui-class)中共用。</span><span class="sxs-lookup"><span data-stu-id="ab645-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="ab645-106">\* Razor 元件類別庫\*可以包含在：</span><span class="sxs-lookup"><span data-stu-id="ab645-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="ab645-107">方案中的另一個專案。</span><span class="sxs-lookup"><span data-stu-id="ab645-107">Another project in the solution.</span></span>
* <span data-ttu-id="ab645-108">NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="ab645-108">A NuGet package.</span></span>
* <span data-ttu-id="ab645-109">參考的 .NET 程式庫。</span><span class="sxs-lookup"><span data-stu-id="ab645-109">A referenced .NET library.</span></span>

<span data-ttu-id="ab645-110">就像元件是一般的 .NET 類型，RCL 所提供的元件是一般的 .NET 元件。</span><span class="sxs-lookup"><span data-stu-id="ab645-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="ab645-111">建立 RCL</span><span class="sxs-lookup"><span data-stu-id="ab645-111">Create an RCL</span></span>

<span data-ttu-id="ab645-112">請遵循文章中的指導方針 <xref:blazor/get-started> ，為設定您的環境 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="ab645-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab645-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab645-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ab645-114">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="ab645-114">Create a new project.</span></span>
1. <span data-ttu-id="ab645-115">選取 [ \*\* Razor 類別庫\*\*]。</span><span class="sxs-lookup"><span data-stu-id="ab645-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="ab645-116">選取 [下一步] 。</span><span class="sxs-lookup"><span data-stu-id="ab645-116">Select **Next**.</span></span>
1. <span data-ttu-id="ab645-117">在 [**建立新的 Razor 類別庫**] 對話方塊中，選取 [**建立**]。</span><span class="sxs-lookup"><span data-stu-id="ab645-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="ab645-118">在 [專案名稱]\*\*\*\* 欄位中提供專案名稱，或接受預設專案名稱。</span><span class="sxs-lookup"><span data-stu-id="ab645-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ab645-119">本主題中的範例會使用專案名稱 `MyComponentLib1` 。</span><span class="sxs-lookup"><span data-stu-id="ab645-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="ab645-120">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="ab645-120">Select **Create**.</span></span>
1. <span data-ttu-id="ab645-121">將 RCL 新增至方案：</span><span class="sxs-lookup"><span data-stu-id="ab645-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="ab645-122">以滑鼠右鍵按一下方案。</span><span class="sxs-lookup"><span data-stu-id="ab645-122">Right-click the solution.</span></span> <span data-ttu-id="ab645-123">選取 [**加入**  >  **現有專案**]。</span><span class="sxs-lookup"><span data-stu-id="ab645-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="ab645-124">流覽至 RCL 的專案檔。</span><span class="sxs-lookup"><span data-stu-id="ab645-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="ab645-125">選取 RCL 的專案檔（*.csproj*）。</span><span class="sxs-lookup"><span data-stu-id="ab645-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="ab645-126">從應用程式新增參考 RCL：</span><span class="sxs-lookup"><span data-stu-id="ab645-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="ab645-127">以滑鼠右鍵按一下應用程式專案。</span><span class="sxs-lookup"><span data-stu-id="ab645-127">Right-click the app project.</span></span> <span data-ttu-id="ab645-128">選取 [**新增**  >  **參考**]。</span><span class="sxs-lookup"><span data-stu-id="ab645-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="ab645-129">選取 [RCL] 專案。</span><span class="sxs-lookup"><span data-stu-id="ab645-129">Select the RCL project.</span></span> <span data-ttu-id="ab645-130">選取 [確定]。</span><span class="sxs-lookup"><span data-stu-id="ab645-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="ab645-131">從範本產生 RCL 時，如果已選取 [**支援頁面和視圖**] 核取方塊，則也會將 *_Imports razor*檔案新增至產生之專案的根目錄，並具有下列內容以啟用 Razor 元件撰寫：</span><span class="sxs-lookup"><span data-stu-id="ab645-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="ab645-132">以手動方式將檔案新增至所產生專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="ab645-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ab645-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ab645-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="ab645-134">使用\*\* Razor 類別庫\*\*範本（ `razorclasslib` ）搭配命令 shell 中的[dotnet new](/dotnet/core/tools/dotnet-new)命令。</span><span class="sxs-lookup"><span data-stu-id="ab645-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="ab645-135">在下列範例中，會建立名為的 RCL `MyComponentLib1` 。</span><span class="sxs-lookup"><span data-stu-id="ab645-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="ab645-136">`MyComponentLib1`執行命令時，會自動建立保存的資料夾：</span><span class="sxs-lookup"><span data-stu-id="ab645-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="ab645-137">如果 `-s|--support-pages-and-views` 從範本產生 RCL 時使用參數，則也會將 *_Imports razor*檔案新增至所產生專案的根目錄，並具有下列內容以啟用 Razor 元件撰寫：</span><span class="sxs-lookup"><span data-stu-id="ab645-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="ab645-138">以手動方式將檔案新增至所產生專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="ab645-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="ab645-139">若要將程式庫新增至現有的專案，請在命令 shell 中使用[dotnet add reference](/dotnet/core/tools/dotnet-add-reference)命令。</span><span class="sxs-lookup"><span data-stu-id="ab645-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="ab645-140">在下列範例中，會將 RCL 新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="ab645-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="ab645-141">從應用程式的專案資料夾中，使用程式庫的路徑執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="ab645-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="ab645-142">使用程式庫元件</span><span class="sxs-lookup"><span data-stu-id="ab645-142">Consume a library component</span></span>

<span data-ttu-id="ab645-143">若要使用另一個專案中的程式庫中所定義的元件，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="ab645-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="ab645-144">使用命名空間的完整類型名稱。</span><span class="sxs-lookup"><span data-stu-id="ab645-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="ab645-145">使用 Razor 的指示詞 [`@using`](xref:mvc/views/razor#using) 。</span><span class="sxs-lookup"><span data-stu-id="ab645-145">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="ab645-146">個別元件可以依名稱新增。</span><span class="sxs-lookup"><span data-stu-id="ab645-146">Individual components can be added by name.</span></span>

<span data-ttu-id="ab645-147">在下列範例中， `MyComponentLib1` 是包含元件的元件庫 `SalesReport` 。</span><span class="sxs-lookup"><span data-stu-id="ab645-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="ab645-148">`SalesReport`元件可以使用其完整型別名稱和命名空間來加以參考：</span><span class="sxs-lookup"><span data-stu-id="ab645-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="ab645-149">如果使用指示詞將程式庫帶入範圍，也可以參考此元件 `@using` ：</span><span class="sxs-lookup"><span data-stu-id="ab645-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="ab645-150">`@using MyComponentLib1`請在最上層的 *_Import razor*檔案中包含指示詞，以將程式庫的元件提供給整個專案。</span><span class="sxs-lookup"><span data-stu-id="ab645-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="ab645-151">將指示詞新增至任何層級的 *_Import razor*檔案，以將命名空間套用至資料夾中的單一頁面或一組頁面。</span><span class="sxs-lookup"><span data-stu-id="ab645-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="ab645-152">建立 Razor 具有靜態資產的元件類別庫</span><span class="sxs-lookup"><span data-stu-id="ab645-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="ab645-153">RCL 可以包含靜態資產。</span><span class="sxs-lookup"><span data-stu-id="ab645-153">An RCL can include static assets.</span></span> <span data-ttu-id="ab645-154">使用該程式庫的任何應用程式都可以使用靜態資產。</span><span class="sxs-lookup"><span data-stu-id="ab645-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="ab645-155">如需詳細資訊，請參閱 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets> 。</span><span class="sxs-lookup"><span data-stu-id="ab645-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="ab645-156">組建、封裝和寄送至 NuGet</span><span class="sxs-lookup"><span data-stu-id="ab645-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="ab645-157">因為元件程式庫是標準的 .NET 程式庫，所以封裝和傳送至 NuGet 的方式與將任何程式庫封裝和傳送至 NuGet 的方式並無不同。</span><span class="sxs-lookup"><span data-stu-id="ab645-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="ab645-158">封裝是使用命令 shell 中的[dotnet pack](/dotnet/core/tools/dotnet-pack)命令來執行：</span><span class="sxs-lookup"><span data-stu-id="ab645-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="ab645-159">使用命令 shell 中的[dotnet NuGet push](/dotnet/core/tools/dotnet-nuget-push)命令，將套件上傳至 nuget。</span><span class="sxs-lookup"><span data-stu-id="ab645-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab645-160">其他資源</span><span class="sxs-lookup"><span data-stu-id="ab645-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="ab645-161">將 XML 連結器設定檔加入至程式庫</span><span class="sxs-lookup"><span data-stu-id="ab645-161">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)