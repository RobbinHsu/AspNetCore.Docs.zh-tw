---
title: 第2部分： Razor ASP.NET Core 中有 EF Core 的頁面-CRUD
author: rick-anderson
description: 頁面第2部分 Razor 和 Entity Framework 教學課程系列。
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/crud
ms.openlocfilehash: 4a48fb094888d51aa6f881c82e4f20ffbc84c8e2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901167"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a><span data-ttu-id="8d567-103">第2部分： Razor ASP.NET Core 中有 EF Core 的頁面-CRUD</span><span class="sxs-lookup"><span data-stu-id="8d567-103">Part 2, Razor Pages with EF Core in ASP.NET Core - CRUD</span></span>

<span data-ttu-id="8d567-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8d567-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8d567-105">在本教學課程中，將會檢閱並自訂 Scaffold CRUD (建立、讀取、更新、刪除)。</span><span class="sxs-lookup"><span data-stu-id="8d567-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="8d567-106">沒有任何存放庫</span><span class="sxs-lookup"><span data-stu-id="8d567-106">No repository</span></span>

<span data-ttu-id="8d567-107">某些開發人員會使用服務層或儲存機制模式，在 UI (Razor 頁面) 和資料存取層之間建立抽象層。</span><span class="sxs-lookup"><span data-stu-id="8d567-107">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span> <span data-ttu-id="8d567-108">本教學課程不會這麼做。</span><span class="sxs-lookup"><span data-stu-id="8d567-108">This tutorial doesn't do that.</span></span> <span data-ttu-id="8d567-109">為降低複雜性並將教學課程聚焦於 EF Core，EF Core 程式碼會直接新增至頁面模型類別中。</span><span class="sxs-lookup"><span data-stu-id="8d567-109">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="8d567-110">更新 [詳細資料] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-110">Update the Details page</span></span>

<span data-ttu-id="8d567-111">Students 頁面的 Scaffold 程式碼不包含註冊資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-111">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="8d567-112">在本節中，您會將註冊新增至 [詳細資料] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-112">In this section, you add enrollments to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="8d567-113">讀取註冊</span><span class="sxs-lookup"><span data-stu-id="8d567-113">Read enrollments</span></span>

<span data-ttu-id="8d567-114">若要在頁面上顯示學生的註冊資料，您必須讀取該資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-114">To display a student's enrollment data on the page, you need to read it.</span></span> <span data-ttu-id="8d567-115">*Pages/Students/Details.cshtml.cs* 中的 scaffold 程式碼只會讀取 Students 資料，而不包含 Enrollment 資料：</span><span class="sxs-lookup"><span data-stu-id="8d567-115">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="8d567-116">將 `OnGetAsync` 方法取代為下列程式碼，以讀取所選學生的註冊資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-116">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="8d567-117">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="8d567-117">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="8d567-118">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 及 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 方法會使內容載入 `Student.Enrollments` 導覽屬性，以及位於每一個註冊中的 `Enrollment.Course` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-118">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="8d567-119">這些方法會在「 [讀取相關資料](xref:data/ef-rp/read-related-data) 」教學課程中詳細檢查。</span><span class="sxs-lookup"><span data-stu-id="8d567-119">These methods are examined in detail in the [Read related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="8d567-120">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 方法在傳回實體未在目前內容中更新的情況下可改善效能。</span><span class="sxs-lookup"><span data-stu-id="8d567-120">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="8d567-121">`AsNoTracking` 稍後在本教學課程中將會討論。</span><span class="sxs-lookup"><span data-stu-id="8d567-121">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="8d567-122">顯示註冊</span><span class="sxs-lookup"><span data-stu-id="8d567-122">Display enrollments</span></span>

<span data-ttu-id="8d567-123">將 *Pages/Students/Details.cshtml* 中的程式碼取代為下列程式碼以顯示註冊清單。</span><span class="sxs-lookup"><span data-stu-id="8d567-123">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="8d567-124">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="8d567-124">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="8d567-125">上述程式碼會以迴圈逐一巡覽 `Enrollments` 導覽屬性中的實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-125">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="8d567-126">針對每個註冊，會顯示課程標題及成績。</span><span class="sxs-lookup"><span data-stu-id="8d567-126">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="8d567-127">課程標題會從儲存於 Enrollments 實體之 `Course` 導覽屬性中的課程 (Course) 實體擷取。</span><span class="sxs-lookup"><span data-stu-id="8d567-127">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="8d567-128">執行應用程式，選取 [Students] 索引標籤，然後按一下學生的 [詳細資料] 連結。</span><span class="sxs-lookup"><span data-stu-id="8d567-128">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="8d567-129">將會顯示所選取學生的課程及成績清單。</span><span class="sxs-lookup"><span data-stu-id="8d567-129">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="8d567-130">讀取單一實體的方式</span><span class="sxs-lookup"><span data-stu-id="8d567-130">Ways to read one entity</span></span>

<span data-ttu-id="8d567-131">產生的程式碼會使用 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) 來讀取單一實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-131">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="8d567-132">如果找不到任何內容，則此方法會傳回 Null；否則會傳回符合查詢篩選準則的第一個資料列。</span><span class="sxs-lookup"><span data-stu-id="8d567-132">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="8d567-133">`FirstOrDefaultAsync` 通常是比下列替代選項更好的選擇：</span><span class="sxs-lookup"><span data-stu-id="8d567-133">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="8d567-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - 如果有超過一個滿足查詢篩選準則的實體，就會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8d567-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="8d567-135">為了判斷查詢是否可能傳回超過一個資料列，`SingleOrDefaultAsync` 會嘗試提取多個資料列。</span><span class="sxs-lookup"><span data-stu-id="8d567-135">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="8d567-136">如果查詢只能傳回單一實體 (如同在搜尋唯一索引鍵時一樣)，則此額外工作是不必要的。</span><span class="sxs-lookup"><span data-stu-id="8d567-136">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="8d567-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 尋找含有主索引鍵 (PK) 的實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="8d567-138">如果內容正在追蹤具有主索引鍵的實體，則會在沒有傳送要求至資料庫的情況下傳回。</span><span class="sxs-lookup"><span data-stu-id="8d567-138">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="8d567-139">此方法已經過最佳化以便查閱單一實體，但是您無法使用 `FindAsync` 呼叫 `Include`。</span><span class="sxs-lookup"><span data-stu-id="8d567-139">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="8d567-140">因此如果需要相關資料，`FirstOrDefaultAsync` 是較好的選擇。</span><span class="sxs-lookup"><span data-stu-id="8d567-140">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="8d567-141">路由資料與查詢字串</span><span class="sxs-lookup"><span data-stu-id="8d567-141">Route data vs. query string</span></span>

<span data-ttu-id="8d567-142">[詳細資料] 頁面的 URL 是 `https://localhost:<port>/Students/Details?id=1`。</span><span class="sxs-lookup"><span data-stu-id="8d567-142">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="8d567-143">實體的主要索引鍵值位於查詢字串中。</span><span class="sxs-lookup"><span data-stu-id="8d567-143">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="8d567-144">某些開發人員偏好在路由資料中傳遞索引鍵值：`https://localhost:<port>/Students/Details/1`。</span><span class="sxs-lookup"><span data-stu-id="8d567-144">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="8d567-145">如需詳細資訊，請參閱[更新產生的程式碼](xref:tutorials/razor-pages/da1#update-the-generated-code)。</span><span class="sxs-lookup"><span data-stu-id="8d567-145">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="8d567-146">更新 [建立] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-146">Update the Create page</span></span>

<span data-ttu-id="8d567-147">[建立] 頁面的 scaffold `OnPostAsync` 程式碼容易受到[大量指派](#overposting)攻擊。</span><span class="sxs-lookup"><span data-stu-id="8d567-147">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="8d567-148">使用下列程式碼取代 *Pages/Students/Create.cshtml.cs* 中的 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-148">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="8d567-149">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="8d567-149">TryUpdateModelAsync</span></span>

<span data-ttu-id="8d567-150">上述程式碼會建立 Student 物件，然後使用張貼的表單欄位來更新 Student 物件屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-150">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="8d567-151">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 方法：</span><span class="sxs-lookup"><span data-stu-id="8d567-151">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="8d567-152">使用 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 中 [PageCoNtext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 屬性的張貼表單值。</span><span class="sxs-lookup"><span data-stu-id="8d567-152">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="8d567-153">僅更新列出的屬性 (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="8d567-153">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="8d567-154">尋找具有 "student" 前置詞的表單欄位。</span><span class="sxs-lookup"><span data-stu-id="8d567-154">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="8d567-155">例如： `Student.FirstMidName` 。</span><span class="sxs-lookup"><span data-stu-id="8d567-155">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="8d567-156">不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="8d567-156">It's not case sensitive.</span></span>
* <span data-ttu-id="8d567-157">使用[模型繫結](xref:mvc/models/model-binding)系統，將字串中表單值轉換成 `Student` 模型中的型別。</span><span class="sxs-lookup"><span data-stu-id="8d567-157">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="8d567-158">例如， `EnrollmentDate` 會轉換成 `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="8d567-158">For example, `EnrollmentDate` is converted to `DateTime`.</span></span>

<span data-ttu-id="8d567-159">執行應用程式，並建立 Student 實體來測試 [建立] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-159">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="8d567-160">大量指派 (overposting)</span><span class="sxs-lookup"><span data-stu-id="8d567-160">Overposting</span></span>

<span data-ttu-id="8d567-161">使用 `TryUpdateModel` 來更新具有已張貼值欄位是最安全的做法，因為它會防止大量指派 (overposting)。</span><span class="sxs-lookup"><span data-stu-id="8d567-161">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="8d567-162">例如，假設 Student 實體包含了一個此網頁不應更新或新增的 `Secret` 屬性：</span><span class="sxs-lookup"><span data-stu-id="8d567-162">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="8d567-163">即使應用程式在 `Secret` [建立或更新] 頁面上沒有欄位 Razor ，駭客也可以藉 `Secret` 由大量指派來設定值。</span><span class="sxs-lookup"><span data-stu-id="8d567-163">Even if the app doesn't have a `Secret` field on the create or update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="8d567-164">駭客仍可能使用 Fiddler 等工具，或是撰寫 JavaScript，來張貼 `Secret` 表單值。</span><span class="sxs-lookup"><span data-stu-id="8d567-164">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="8d567-165">原始程式碼並不會限制模型繫結器在建立 Student 執行個體時所使用的欄位。</span><span class="sxs-lookup"><span data-stu-id="8d567-165">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="8d567-166">無論駭客在 `Secret` 表單欄位中指定了什麼值，該值都會更新到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="8d567-166">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="8d567-167">下圖顯示 Fiddler 工具，將值為 `Secret` "OverPost" 的欄位新增至張貼的表單值。</span><span class="sxs-lookup"><span data-stu-id="8d567-167">The following image shows the Fiddler tool adding the `Secret` field, with the value "OverPost", to the posted form values.</span></span>

![Fiddler 新增 [Secret] 欄位](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="8d567-169">"OverPost" 值已成功新增至插入資料列的 `Secret` 屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-169">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="8d567-170">即使應用程式設計師從未打算在 [建立] 頁面設定 `Secret` 屬性，還是會發生此情況。</span><span class="sxs-lookup"><span data-stu-id="8d567-170">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="8d567-171">檢視模型</span><span class="sxs-lookup"><span data-stu-id="8d567-171">View model</span></span>

<span data-ttu-id="8d567-172">檢視模型提供防止大量指派 (overposting) 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-172">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="8d567-173">應用程式模型通常稱為網域模型。</span><span class="sxs-lookup"><span data-stu-id="8d567-173">The application model is often called the domain model.</span></span> <span data-ttu-id="8d567-174">網域模型通常會包含資料庫中對應實體所需要的所有屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-174">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="8d567-175">視圖模型只包含 UI 頁面所需的屬性，例如 [建立] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-175">The view model contains only the properties needed for the UI page, for example, the Create page.</span></span>

<span data-ttu-id="8d567-176">除了 view 模型之外，有些應用程式會使用系結模型或輸入模型，在 Razor 頁面頁面模型類別和瀏覽器之間傳遞資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-176">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> 

<span data-ttu-id="8d567-177">請看看下列 `StudentVM` 檢視模型：</span><span class="sxs-lookup"><span data-stu-id="8d567-177">Consider the following `StudentVM` view model:</span></span>

[!code-csharp[Main](intro/samples/cu50/ViewModels/StudentVM.cs?name=snippet)]

<span data-ttu-id="8d567-178">下列程式碼會使用 `StudentVM` 檢視模型來建立一名新學生：</span><span class="sxs-lookup"><span data-stu-id="8d567-178">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml.cs?name=snippet)]

<span data-ttu-id="8d567-179">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 會設定這個物件的值，方法是藉由從其他 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 物件中讀取值。</span><span class="sxs-lookup"><span data-stu-id="8d567-179">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="8d567-180">`SetValues` 使用屬性名稱比對。</span><span class="sxs-lookup"><span data-stu-id="8d567-180">`SetValues` uses property name matching.</span></span> <span data-ttu-id="8d567-181">視圖模型類型：</span><span class="sxs-lookup"><span data-stu-id="8d567-181">The view model type:</span></span>

* <span data-ttu-id="8d567-182">不需要與模型類型相關。</span><span class="sxs-lookup"><span data-stu-id="8d567-182">Doesn't need to be related to the model type.</span></span>
* <span data-ttu-id="8d567-183">需要有符合的屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-183">Needs to have properties that match.</span></span>

<span data-ttu-id="8d567-184">使用 `StudentVM` 需要建立頁面使用， `StudentVM` 而不是 `Student` ：</span><span class="sxs-lookup"><span data-stu-id="8d567-184">Using `StudentVM` requires the Create page use `StudentVM` rather than `Student`:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml)]

## <a name="update-the-edit-page"></a><span data-ttu-id="8d567-185">更新 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-185">Update the Edit page</span></span>

<span data-ttu-id="8d567-186">在 *Pages/Students/Edit.cshtml.cs* 中，使用下列程式碼取代 `OnGetAsync` 和 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-186">In *Pages/Students/Edit.cshtml.cs*, replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="8d567-187">程式碼的變更與 [建立] 頁面相似，但有一些例外狀況：</span><span class="sxs-lookup"><span data-stu-id="8d567-187">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="8d567-188">`FirstOrDefaultAsync` 已取代為 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)。</span><span class="sxs-lookup"><span data-stu-id="8d567-188">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="8d567-189">如果您不需要包含相關資料，則 `FindAsync` 效率較高。</span><span class="sxs-lookup"><span data-stu-id="8d567-189">When you don't have to include related data, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="8d567-190">`OnPostAsync` 具有 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="8d567-190">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="8d567-191">會從資料庫中擷取出目前的學生，而不會建立一個空白的學生資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-191">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="8d567-192">執行應用程式，並藉由建立和編輯學生來進行測試。</span><span class="sxs-lookup"><span data-stu-id="8d567-192">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="8d567-193">實體狀態</span><span class="sxs-lookup"><span data-stu-id="8d567-193">Entity States</span></span>

<span data-ttu-id="8d567-194">無論記憶體中實體是否與資料庫中相對應資料列同步，資料庫內容都會持續追蹤。</span><span class="sxs-lookup"><span data-stu-id="8d567-194">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="8d567-195">呼叫 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 時，此追蹤資訊會決定該怎麼做。</span><span class="sxs-lookup"><span data-stu-id="8d567-195">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="8d567-196">例如，當傳遞一個新的實體到 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 方法時，該實體的狀態便會設定為 [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)。</span><span class="sxs-lookup"><span data-stu-id="8d567-196">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="8d567-197">當 `SaveChangesAsync` 呼叫時，資料庫內容會發出 SQL `INSERT` 命令。</span><span class="sxs-lookup"><span data-stu-id="8d567-197">When `SaveChangesAsync` is called, the database context issues a SQL `INSERT` command.</span></span>

<span data-ttu-id="8d567-198">實體可為[下列狀態](/dotnet/api/microsoft.entityframeworkcore.entitystate)中的其中一個：</span><span class="sxs-lookup"><span data-stu-id="8d567-198">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="8d567-199">`Added`：實體尚未存在於資料庫中。</span><span class="sxs-lookup"><span data-stu-id="8d567-199">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="8d567-200">`SaveChanges`方法會發出 `INSERT` 語句。</span><span class="sxs-lookup"><span data-stu-id="8d567-200">The `SaveChanges` method issues an `INSERT` statement.</span></span>

* <span data-ttu-id="8d567-201">`Unchanged`：此實體沒有需要儲存的變更。</span><span class="sxs-lookup"><span data-stu-id="8d567-201">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="8d567-202">從資料庫讀取時，此實體將會有這個狀態。</span><span class="sxs-lookup"><span data-stu-id="8d567-202">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="8d567-203">`Modified`：實體中一部分或全部的屬性值已經過修改。</span><span class="sxs-lookup"><span data-stu-id="8d567-203">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="8d567-204">`SaveChanges`方法會發出 `UPDATE` 語句。</span><span class="sxs-lookup"><span data-stu-id="8d567-204">The `SaveChanges` method issues an `UPDATE` statement.</span></span>

* <span data-ttu-id="8d567-205">`Deleted`：實體已遭標示刪除。</span><span class="sxs-lookup"><span data-stu-id="8d567-205">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="8d567-206">`SaveChanges`方法會發出 `DELETE` 語句。</span><span class="sxs-lookup"><span data-stu-id="8d567-206">The `SaveChanges` method issues a `DELETE` statement.</span></span>

* <span data-ttu-id="8d567-207">`Detached`：實體未被資料庫內容追蹤。</span><span class="sxs-lookup"><span data-stu-id="8d567-207">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="8d567-208">在桌面應用程式中，狀態變更通常會自動進行設定。</span><span class="sxs-lookup"><span data-stu-id="8d567-208">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="8d567-209">實體已讀取、已進行變更，且實體狀態會自動變更為 `Modified`。</span><span class="sxs-lookup"><span data-stu-id="8d567-209">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="8d567-210">呼叫 `SaveChanges` 會產生 SQL `UPDATE` 語句，只更新已變更的屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-210">Calling `SaveChanges` generates a SQL `UPDATE` statement that updates only the changed properties.</span></span>

<span data-ttu-id="8d567-211">在 Web 應用程式中，讀取實體並顯示其資料以供編輯之用的 `DbContext` 會在頁面轉譯之後遭到處置。</span><span class="sxs-lookup"><span data-stu-id="8d567-211">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="8d567-212">當呼叫頁面的 `OnPostAsync` 方法時，會發出新的 Web 要求，並且會擁有一個新的 `DbContext` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="8d567-212">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="8d567-213">在新的內容中重新讀取實體時，會模擬桌面處理流程。</span><span class="sxs-lookup"><span data-stu-id="8d567-213">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="8d567-214">更新 [刪除] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-214">Update the Delete page</span></span>

<span data-ttu-id="8d567-215">在本節中，當呼叫失敗時，會執行自訂錯誤訊息 `SaveChanges` 。</span><span class="sxs-lookup"><span data-stu-id="8d567-215">In this section, a custom error message is implemented when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="8d567-216">使用下列程式碼取代 *Pages/Students/Delete.cshtml.cs* 中的程式碼。</span><span class="sxs-lookup"><span data-stu-id="8d567-216">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="8d567-217">所做的變更已醒目提示：</span><span class="sxs-lookup"><span data-stu-id="8d567-217">The changes are highlighted:</span></span>

[!code-csharp[Main](intro/samples/cu50/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=12-14,22,30-33,45-99)]

<span data-ttu-id="8d567-218">上述程式碼會將選擇性參數 `saveChangesError` 新增至 `OnGetAsync` 方法簽章。</span><span class="sxs-lookup"><span data-stu-id="8d567-218">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="8d567-219">`saveChangesError` 會顯示出此方法是否已在刪除學生物件失敗後呼叫。</span><span class="sxs-lookup"><span data-stu-id="8d567-219">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="8d567-220">刪除作業可能會因為暫時性的網路問題而失敗。</span><span class="sxs-lookup"><span data-stu-id="8d567-220">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="8d567-221">資料庫位於雲端時，較可能發生暫時性的網路錯誤。</span><span class="sxs-lookup"><span data-stu-id="8d567-221">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="8d567-222">`saveChangesError`參數是 `false` `OnGetAsync` 從 UI 呼叫刪除頁面時。</span><span class="sxs-lookup"><span data-stu-id="8d567-222">The `saveChangesError` parameter is `false` when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="8d567-223">當 `OnGetAsync` `OnPostAsync` 因為刪除作業失敗而呼叫時， `saveChangesError` 參數為 `true` 。</span><span class="sxs-lookup"><span data-stu-id="8d567-223">When `OnGetAsync` is called by `OnPostAsync` because the delete operation failed, the `saveChangesError` parameter is `true`.</span></span>

<span data-ttu-id="8d567-224">`OnPostAsync` 方法會擷取選取的實體，然後呼叫 [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 方法來將實體的狀態設定為 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="8d567-224">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="8d567-225">當 `SaveChanges` 呼叫時， `DELETE` 會產生 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="8d567-225">When `SaveChanges` is called, a SQL `DELETE` command is generated.</span></span> <span data-ttu-id="8d567-226">如果 `Remove` 失敗：</span><span class="sxs-lookup"><span data-stu-id="8d567-226">If `Remove` fails:</span></span>

* <span data-ttu-id="8d567-227">攔截到資料庫例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8d567-227">The database exception is caught.</span></span>
* <span data-ttu-id="8d567-228">[刪除] 頁面的 `OnGetAsync` 方法會以 `saveChangesError=true` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="8d567-228">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="8d567-229">將錯誤訊息加入至 *Pages/student/Delete。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="8d567-229">Add an error message to *Pages/Students/Delete.cshtml*:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="8d567-230">執行應用程式並刪除學生以測試 [刪除] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-230">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8d567-231">後續步驟</span><span class="sxs-lookup"><span data-stu-id="8d567-231">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8d567-232">[上一個教學](xref:data/ef-rp/intro) 
>  課程[下一個教學](xref:data/ef-rp/sort-filter-page)課程</span><span class="sxs-lookup"><span data-stu-id="8d567-232">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="8d567-233">在本教學課程中，將會檢閱並自訂 Scaffold CRUD (建立、讀取、更新、刪除)。</span><span class="sxs-lookup"><span data-stu-id="8d567-233">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="8d567-234">沒有任何存放庫</span><span class="sxs-lookup"><span data-stu-id="8d567-234">No repository</span></span>

<span data-ttu-id="8d567-235">某些開發人員會使用服務層或儲存機制模式，在 UI (Razor 頁面) 和資料存取層之間建立抽象層。</span><span class="sxs-lookup"><span data-stu-id="8d567-235">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span> <span data-ttu-id="8d567-236">本教學課程不會這麼做。</span><span class="sxs-lookup"><span data-stu-id="8d567-236">This tutorial doesn't do that.</span></span> <span data-ttu-id="8d567-237">為降低複雜性並將教學課程聚焦於 EF Core，EF Core 程式碼會直接新增至頁面模型類別中。</span><span class="sxs-lookup"><span data-stu-id="8d567-237">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="8d567-238">更新 [詳細資料] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-238">Update the Details page</span></span>

<span data-ttu-id="8d567-239">Students 頁面的 Scaffold 程式碼不包含註冊資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-239">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="8d567-240">在本節中，會將註冊新增至 [詳細資料] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-240">In this section, enrollments are added to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="8d567-241">讀取註冊</span><span class="sxs-lookup"><span data-stu-id="8d567-241">Read enrollments</span></span>

<span data-ttu-id="8d567-242">若要在頁面上顯示學生的註冊資料，必須讀取 enrollment 資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-242">To display a student's enrollment data on the page, the enrollement data needs to be read.</span></span> <span data-ttu-id="8d567-243">*Pages/Students/Details.cshtml.cs* 中的 scaffold 程式碼只會讀取 Students 資料，而不包含 Enrollment 資料：</span><span class="sxs-lookup"><span data-stu-id="8d567-243">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="8d567-244">將 `OnGetAsync` 方法取代為下列程式碼，以讀取所選學生的註冊資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-244">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="8d567-245">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="8d567-245">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="8d567-246">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 及 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 方法會使內容載入 `Student.Enrollments` 導覽屬性，以及位於每一個註冊中的 `Enrollment.Course` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-246">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="8d567-247">您可在[讀取相關資料](xref:data/ef-rp/read-related-data)教學課程中詳細檢視這些方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-247">These methods are examined in detail in the [Reading related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="8d567-248">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 方法在傳回實體未在目前內容中更新的情況下可改善效能。</span><span class="sxs-lookup"><span data-stu-id="8d567-248">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="8d567-249">`AsNoTracking` 稍後在本教學課程中將會討論。</span><span class="sxs-lookup"><span data-stu-id="8d567-249">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="8d567-250">顯示註冊</span><span class="sxs-lookup"><span data-stu-id="8d567-250">Display enrollments</span></span>

<span data-ttu-id="8d567-251">將 *Pages/Students/Details.cshtml* 中的程式碼取代為下列程式碼以顯示註冊清單。</span><span class="sxs-lookup"><span data-stu-id="8d567-251">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="8d567-252">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="8d567-252">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="8d567-253">上述程式碼會以迴圈逐一巡覽 `Enrollments` 導覽屬性中的實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-253">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="8d567-254">針對每個註冊，會顯示課程標題及成績。</span><span class="sxs-lookup"><span data-stu-id="8d567-254">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="8d567-255">課程標題會從儲存於 Enrollments 實體之 `Course` 導覽屬性中的課程 (Course) 實體擷取。</span><span class="sxs-lookup"><span data-stu-id="8d567-255">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="8d567-256">執行應用程式，選取 [Students] 索引標籤，然後按一下學生的 [詳細資料] 連結。</span><span class="sxs-lookup"><span data-stu-id="8d567-256">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="8d567-257">將會顯示所選取學生的課程及成績清單。</span><span class="sxs-lookup"><span data-stu-id="8d567-257">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="8d567-258">讀取單一實體的方式</span><span class="sxs-lookup"><span data-stu-id="8d567-258">Ways to read one entity</span></span>

<span data-ttu-id="8d567-259">產生的程式碼會使用 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) 來讀取單一實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-259">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="8d567-260">如果找不到任何內容，則此方法會傳回 Null；否則會傳回符合查詢篩選準則的第一個資料列。</span><span class="sxs-lookup"><span data-stu-id="8d567-260">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="8d567-261">`FirstOrDefaultAsync` 通常是比下列替代選項更好的選擇：</span><span class="sxs-lookup"><span data-stu-id="8d567-261">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="8d567-262">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - 如果有超過一個滿足查詢篩選準則的實體，就會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8d567-262">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="8d567-263">為了判斷查詢是否可能傳回超過一個資料列，`SingleOrDefaultAsync` 會嘗試提取多個資料列。</span><span class="sxs-lookup"><span data-stu-id="8d567-263">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="8d567-264">如果查詢只能傳回單一實體 (如同在搜尋唯一索引鍵時一樣)，則此額外工作是不必要的。</span><span class="sxs-lookup"><span data-stu-id="8d567-264">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="8d567-265">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 尋找含有主索引鍵 (PK) 的實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-265">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="8d567-266">如果內容正在追蹤具有主索引鍵的實體，則會在沒有傳送要求至資料庫的情況下傳回。</span><span class="sxs-lookup"><span data-stu-id="8d567-266">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="8d567-267">此方法已經過最佳化以便查閱單一實體，但是您無法使用 `FindAsync` 呼叫 `Include`。</span><span class="sxs-lookup"><span data-stu-id="8d567-267">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="8d567-268">因此如果需要相關資料，`FirstOrDefaultAsync` 是較好的選擇。</span><span class="sxs-lookup"><span data-stu-id="8d567-268">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="8d567-269">路由資料與查詢字串</span><span class="sxs-lookup"><span data-stu-id="8d567-269">Route data vs. query string</span></span>

<span data-ttu-id="8d567-270">[詳細資料] 頁面的 URL 是 `https://localhost:<port>/Students/Details?id=1`。</span><span class="sxs-lookup"><span data-stu-id="8d567-270">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="8d567-271">實體的主要索引鍵值位於查詢字串中。</span><span class="sxs-lookup"><span data-stu-id="8d567-271">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="8d567-272">某些開發人員偏好在路由資料中傳遞索引鍵值：`https://localhost:<port>/Students/Details/1`。</span><span class="sxs-lookup"><span data-stu-id="8d567-272">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="8d567-273">如需詳細資訊，請參閱[更新產生的程式碼](xref:tutorials/razor-pages/da1#update-the-generated-code)。</span><span class="sxs-lookup"><span data-stu-id="8d567-273">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="8d567-274">更新 [建立] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-274">Update the Create page</span></span>

<span data-ttu-id="8d567-275">[建立] 頁面的 scaffold `OnPostAsync` 程式碼容易受到[大量指派](#overposting)攻擊。</span><span class="sxs-lookup"><span data-stu-id="8d567-275">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="8d567-276">使用下列程式碼取代 *Pages/Students/Create.cshtml.cs* 中的 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-276">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="8d567-277">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="8d567-277">TryUpdateModelAsync</span></span>

<span data-ttu-id="8d567-278">上述程式碼會建立 Student 物件，然後使用張貼的表單欄位來更新 Student 物件屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-278">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="8d567-279">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 方法：</span><span class="sxs-lookup"><span data-stu-id="8d567-279">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="8d567-280">使用 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 中 [PageCoNtext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 屬性的張貼表單值。</span><span class="sxs-lookup"><span data-stu-id="8d567-280">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="8d567-281">僅更新列出的屬性 (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="8d567-281">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="8d567-282">尋找具有 "student" 前置詞的表單欄位。</span><span class="sxs-lookup"><span data-stu-id="8d567-282">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="8d567-283">例如： `Student.FirstMidName` 。</span><span class="sxs-lookup"><span data-stu-id="8d567-283">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="8d567-284">不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="8d567-284">It's not case sensitive.</span></span>
* <span data-ttu-id="8d567-285">使用[模型繫結](xref:mvc/models/model-binding)系統，將字串中表單值轉換成 `Student` 模型中的型別。</span><span class="sxs-lookup"><span data-stu-id="8d567-285">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="8d567-286">例如，`EnrollmentDate` 必須轉換成 DateTime。</span><span class="sxs-lookup"><span data-stu-id="8d567-286">For example, `EnrollmentDate` has to be converted to DateTime.</span></span>

<span data-ttu-id="8d567-287">執行應用程式，並建立 Student 實體來測試 [建立] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-287">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="8d567-288">大量指派 (overposting)</span><span class="sxs-lookup"><span data-stu-id="8d567-288">Overposting</span></span>

<span data-ttu-id="8d567-289">使用 `TryUpdateModel` 來更新具有已張貼值欄位是最安全的做法，因為它會防止大量指派 (overposting)。</span><span class="sxs-lookup"><span data-stu-id="8d567-289">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="8d567-290">例如，假設 Student 實體包含了一個此網頁不應更新或新增的 `Secret` 屬性：</span><span class="sxs-lookup"><span data-stu-id="8d567-290">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="8d567-291">即使應用程式在 `Secret` [建立或更新] 頁面上沒有欄位 Razor ，駭客也可以藉 `Secret` 由大量指派來設定值。</span><span class="sxs-lookup"><span data-stu-id="8d567-291">Even if the app doesn't have a `Secret` field on the create or update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="8d567-292">駭客仍可能使用 Fiddler 等工具，或是撰寫 JavaScript，來張貼 `Secret` 表單值。</span><span class="sxs-lookup"><span data-stu-id="8d567-292">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="8d567-293">原始程式碼並不會限制模型繫結器在建立 Student 執行個體時所使用的欄位。</span><span class="sxs-lookup"><span data-stu-id="8d567-293">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="8d567-294">無論駭客在 `Secret` 表單欄位中指定了什麼值，該值都會更新到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="8d567-294">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="8d567-295">下列影響顯示了 Fiddler 工具將 `Secret` 欄位 (其值為 "OverPost") 新增到表單的值中。</span><span class="sxs-lookup"><span data-stu-id="8d567-295">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler 新增 [Secret] 欄位](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="8d567-297">"OverPost" 值已成功新增至插入資料列的 `Secret` 屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-297">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="8d567-298">即使應用程式設計師從未打算在 [建立] 頁面設定 `Secret` 屬性，還是會發生此情況。</span><span class="sxs-lookup"><span data-stu-id="8d567-298">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="8d567-299">檢視模型</span><span class="sxs-lookup"><span data-stu-id="8d567-299">View model</span></span>

<span data-ttu-id="8d567-300">檢視模型提供防止大量指派 (overposting) 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-300">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="8d567-301">應用程式模型通常稱為網域模型。</span><span class="sxs-lookup"><span data-stu-id="8d567-301">The application model is often called the domain model.</span></span> <span data-ttu-id="8d567-302">網域模型通常會包含資料庫中對應實體所需要的所有屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-302">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="8d567-303">檢視模型只包含 UI 所需要的屬性 (例如 [建立] 頁面)。</span><span class="sxs-lookup"><span data-stu-id="8d567-303">The view model contains only the properties needed for the UI that it is used for (for example, the Create page).</span></span>

<span data-ttu-id="8d567-304">除了 view 模型之外，有些應用程式會使用系結模型或輸入模型，在 Razor 頁面頁面模型類別和瀏覽器之間傳遞資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-304">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> 

<span data-ttu-id="8d567-305">請看看下列 `Student` 檢視模型：</span><span class="sxs-lookup"><span data-stu-id="8d567-305">Consider the following `Student` view model:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

<span data-ttu-id="8d567-306">下列程式碼會使用 `StudentVM` 檢視模型來建立一名新學生：</span><span class="sxs-lookup"><span data-stu-id="8d567-306">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="8d567-307">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 會設定這個物件的值，方法是藉由從其他 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 物件中讀取值。</span><span class="sxs-lookup"><span data-stu-id="8d567-307">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="8d567-308">`SetValues` 使用屬性名稱比對。</span><span class="sxs-lookup"><span data-stu-id="8d567-308">`SetValues` uses property name matching.</span></span> <span data-ttu-id="8d567-309">檢視模型類型不需要與模型類型相關，只需要有符合的屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-309">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="8d567-310">使用 `StudentVM` 需要 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) 更新為使用 `StudentVM` 而不是 `Student`。</span><span class="sxs-lookup"><span data-stu-id="8d567-310">Using `StudentVM` requires [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="8d567-311">更新 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-311">Update the Edit page</span></span>

<span data-ttu-id="8d567-312">在 *Pages/Students/Edit.cshtml.cs* 中，使用下列程式碼取代 `OnGetAsync` 和 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-312">In *Pages/Students/Edit.cshtml.cs*, replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="8d567-313">程式碼的變更與 [建立] 頁面相似，但有一些例外狀況：</span><span class="sxs-lookup"><span data-stu-id="8d567-313">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="8d567-314">`FirstOrDefaultAsync` 已取代為 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)。</span><span class="sxs-lookup"><span data-stu-id="8d567-314">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="8d567-315">不需要包含相關的資料時， `FindAsync` 更有效率。</span><span class="sxs-lookup"><span data-stu-id="8d567-315">When included related data is not needed, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="8d567-316">`OnPostAsync` 具有 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="8d567-316">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="8d567-317">會從資料庫中擷取出目前的學生，而不會建立一個空白的學生資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-317">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="8d567-318">執行應用程式，並藉由建立和編輯學生來進行測試。</span><span class="sxs-lookup"><span data-stu-id="8d567-318">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="8d567-319">實體狀態</span><span class="sxs-lookup"><span data-stu-id="8d567-319">Entity States</span></span>

<span data-ttu-id="8d567-320">無論記憶體中實體是否與資料庫中相對應資料列同步，資料庫內容都會持續追蹤。</span><span class="sxs-lookup"><span data-stu-id="8d567-320">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="8d567-321">呼叫 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 時，此追蹤資訊會決定該怎麼做。</span><span class="sxs-lookup"><span data-stu-id="8d567-321">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="8d567-322">例如，當傳遞一個新的實體到 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 方法時，該實體的狀態便會設定為 [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)。</span><span class="sxs-lookup"><span data-stu-id="8d567-322">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="8d567-323">呼叫 `SaveChangesAsync` 時，資料庫內容會發出 SQL INSERT 命令。</span><span class="sxs-lookup"><span data-stu-id="8d567-323">When `SaveChangesAsync` is called, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="8d567-324">實體可為[下列狀態](/dotnet/api/microsoft.entityframeworkcore.entitystate)中的其中一個：</span><span class="sxs-lookup"><span data-stu-id="8d567-324">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="8d567-325">`Added`：實體尚未存在於資料庫中。</span><span class="sxs-lookup"><span data-stu-id="8d567-325">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="8d567-326">`SaveChanges` 方法會發出 INSERT 陳述式。</span><span class="sxs-lookup"><span data-stu-id="8d567-326">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="8d567-327">`Unchanged`：此實體沒有需要儲存的變更。</span><span class="sxs-lookup"><span data-stu-id="8d567-327">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="8d567-328">從資料庫讀取時，此實體將會有這個狀態。</span><span class="sxs-lookup"><span data-stu-id="8d567-328">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="8d567-329">`Modified`：實體中一部分或全部的屬性值已經過修改。</span><span class="sxs-lookup"><span data-stu-id="8d567-329">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="8d567-330">`SaveChanges` 方法會發出 UPDATE 陳述式。</span><span class="sxs-lookup"><span data-stu-id="8d567-330">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="8d567-331">`Deleted`：實體已遭標示刪除。</span><span class="sxs-lookup"><span data-stu-id="8d567-331">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="8d567-332">`SaveChanges` 方法會發出 DELETE 陳述式。</span><span class="sxs-lookup"><span data-stu-id="8d567-332">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="8d567-333">`Detached`：實體未被資料庫內容追蹤。</span><span class="sxs-lookup"><span data-stu-id="8d567-333">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="8d567-334">在桌面應用程式中，狀態變更通常會自動進行設定。</span><span class="sxs-lookup"><span data-stu-id="8d567-334">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="8d567-335">實體已讀取、已進行變更，且實體狀態會自動變更為 `Modified`。</span><span class="sxs-lookup"><span data-stu-id="8d567-335">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="8d567-336">呼叫 `SaveChanges` 會產生 SQL UPDATE 陳述式，此陳述式只會更新變更過的屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-336">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="8d567-337">在 Web 應用程式中，讀取實體並顯示其資料以供編輯之用的 `DbContext` 會在頁面轉譯之後遭到處置。</span><span class="sxs-lookup"><span data-stu-id="8d567-337">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="8d567-338">當呼叫頁面的 `OnPostAsync` 方法時，會發出新的 Web 要求，並且會擁有一個新的 `DbContext` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="8d567-338">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="8d567-339">在新的內容中重新讀取實體時，會模擬桌面處理流程。</span><span class="sxs-lookup"><span data-stu-id="8d567-339">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="8d567-340">更新 [刪除] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-340">Update the Delete page</span></span>

<span data-ttu-id="8d567-341">在本節中，您將實作一個呼叫至 `SaveChanges` 失敗時的自訂錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="8d567-341">In this section, you implement a custom error message when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="8d567-342">使用下列程式碼取代 *Pages/Students/Delete.cshtml.cs* 中的程式碼。</span><span class="sxs-lookup"><span data-stu-id="8d567-342">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="8d567-343">變更會醒目提示 (除了清除 `using` 陳述式之外)。</span><span class="sxs-lookup"><span data-stu-id="8d567-343">The changes are highlighted (other than cleanup of `using` statements).</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

<span data-ttu-id="8d567-344">上述程式碼會將選擇性參數 `saveChangesError` 新增至 `OnGetAsync` 方法簽章。</span><span class="sxs-lookup"><span data-stu-id="8d567-344">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="8d567-345">`saveChangesError` 會顯示出此方法是否已在刪除學生物件失敗後呼叫。</span><span class="sxs-lookup"><span data-stu-id="8d567-345">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="8d567-346">刪除作業可能會因為暫時性的網路問題而失敗。</span><span class="sxs-lookup"><span data-stu-id="8d567-346">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="8d567-347">資料庫位於雲端時，較可能發生暫時性的網路錯誤。</span><span class="sxs-lookup"><span data-stu-id="8d567-347">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="8d567-348">從 UI 中呼叫 [刪除] 頁面 `OnGetAsync` 時，`saveChangesError` 參數為 false。</span><span class="sxs-lookup"><span data-stu-id="8d567-348">The `saveChangesError` parameter is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="8d567-349">當 `OnPostAsync` 呼叫 `OnGetAsync` 時 (因刪除作業失敗)，則 `saveChangesError` 參數為 true。</span><span class="sxs-lookup"><span data-stu-id="8d567-349">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

<span data-ttu-id="8d567-350">`OnPostAsync` 方法會擷取選取的實體，然後呼叫 [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 方法來將實體的狀態設定為 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="8d567-350">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="8d567-351">當呼叫 `SaveChanges` 時，便會產生 SQL DELETE 命令。</span><span class="sxs-lookup"><span data-stu-id="8d567-351">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="8d567-352">如果 `Remove` 失敗：</span><span class="sxs-lookup"><span data-stu-id="8d567-352">If `Remove` fails:</span></span>

* <span data-ttu-id="8d567-353">攔截到資料庫例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8d567-353">The database exception is caught.</span></span>
* <span data-ttu-id="8d567-354">刪除頁面的 `OnGetAsync` 方法是使用來呼叫 `saveChangesError=true` 。</span><span class="sxs-lookup"><span data-stu-id="8d567-354">The Delete page's `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="8d567-355">將錯誤訊息加入至 [刪除] Razor 頁面， (*Pages/Student/Delete. cshtml*) ：</span><span class="sxs-lookup"><span data-stu-id="8d567-355">Add an error message to the Delete Razor Page (*Pages/Students/Delete.cshtml*):</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="8d567-356">執行應用程式並刪除學生以測試 [刪除] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-356">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8d567-357">後續步驟</span><span class="sxs-lookup"><span data-stu-id="8d567-357">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8d567-358">[上一個教學](xref:data/ef-rp/intro) 
>  課程[下一個教學](xref:data/ef-rp/sort-filter-page)課程</span><span class="sxs-lookup"><span data-stu-id="8d567-358">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d567-359">在本教學課程中，將會檢閱並自訂 Scaffold CRUD (建立、讀取、更新、刪除)。</span><span class="sxs-lookup"><span data-stu-id="8d567-359">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="8d567-360">為降低複雜性並將教學課程聚焦於 EF Core，EF Core 程式碼會使用於頁面模型中。</span><span class="sxs-lookup"><span data-stu-id="8d567-360">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="8d567-361">某些開發人員會使用中的服務層或儲存機制模式，在 UI (Razor 頁面) 和資料存取層之間建立抽象層。</span><span class="sxs-lookup"><span data-stu-id="8d567-361">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="8d567-362">在本教學課程中， Razor 會檢查 [ *學生* ] 資料夾中的 [建立]、[編輯]、[刪除] 和 [詳細資料] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-362">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="8d567-363">Scaffold 程式碼會為 [建立]、[編輯]、[刪除] 頁面使用下列模式：</span><span class="sxs-lookup"><span data-stu-id="8d567-363">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="8d567-364">使用 HTTP GET 方法 `OnGetAsync` 來取得並顯示所要求的資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-364">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="8d567-365">使用 HTTP POST `OnPostAsync` 方法將變更儲存至資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-365">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="8d567-366">[索引] 頁面和 [詳細資料] 頁面以 HTTP GET 方法 `OnGetAsync` 取得並顯示所要求的資料</span><span class="sxs-lookup"><span data-stu-id="8d567-366">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="8d567-367">SingleOrDefaultAsync 與 Singleordefaultasync 的比較</span><span class="sxs-lookup"><span data-stu-id="8d567-367">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="8d567-368">產生的程式碼會使用 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)，一般會偏好它而非 [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。</span><span class="sxs-lookup"><span data-stu-id="8d567-368">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="8d567-369">`FirstOrDefaultAsync` 在擷取單一實體上比 `SingleOrDefaultAsync` 更有效率：</span><span class="sxs-lookup"><span data-stu-id="8d567-369">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="8d567-370">除非程式碼需要確認從查詢傳回的實體不多於一個。</span><span class="sxs-lookup"><span data-stu-id="8d567-370">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="8d567-371">`SingleOrDefaultAsync` 擷取更多資料並進行不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="8d567-371">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="8d567-372">如果有多於一個符合篩選組件的實體，`SingleOrDefaultAsync` 將會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8d567-372">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="8d567-373">如果有多於一個符合篩選組件的實體，`FirstOrDefaultAsync` 不會擲回。</span><span class="sxs-lookup"><span data-stu-id="8d567-373">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="8d567-374">FindAsync</span><span class="sxs-lookup"><span data-stu-id="8d567-374">FindAsync</span></span>

<span data-ttu-id="8d567-375">在大部分的 Scaffold 程式碼中，[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) 可用來取代 `FirstOrDefaultAsync`。</span><span class="sxs-lookup"><span data-stu-id="8d567-375">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="8d567-376">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="8d567-376">`FindAsync`:</span></span>

* <span data-ttu-id="8d567-377">尋找含有主索引鍵 (PK) 的實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-377">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="8d567-378">如果內容正在追蹤含有主索引鍵的實體，會在沒有傳送要求至資料庫的情況下傳回。</span><span class="sxs-lookup"><span data-stu-id="8d567-378">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="8d567-379">簡單並精確。</span><span class="sxs-lookup"><span data-stu-id="8d567-379">Is simple and concise.</span></span>
* <span data-ttu-id="8d567-380">已最佳化來查閱單一實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-380">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="8d567-381">在某些情況下可具有效能優勢，但是在一般的 Web 應用程式很少見。</span><span class="sxs-lookup"><span data-stu-id="8d567-381">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="8d567-382">隱含使用 [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) 而不是 [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。</span><span class="sxs-lookup"><span data-stu-id="8d567-382">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="8d567-383">但是如果您想要 `Include` 其他項目，則 `FindAsync` 已不再適用。</span><span class="sxs-lookup"><span data-stu-id="8d567-383">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="8d567-384">這表示您可能要放棄 `FindAsync` 並隨著您的應用程式進行而移至查詢。</span><span class="sxs-lookup"><span data-stu-id="8d567-384">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="8d567-385">自訂 [詳細資料] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-385">Customize the Details page</span></span>

<span data-ttu-id="8d567-386">瀏覽至 `Pages/Students` 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-386">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="8d567-387">在 *Pages/Students/Index.cshtml* 檔案中，**編輯**、**詳細資料**、**刪除** 連結是由 [錨點標籤協助程式](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)所產生。</span><span class="sxs-lookup"><span data-stu-id="8d567-387">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="8d567-388">執行應用程式並選取 [詳細資料] 連結。</span><span class="sxs-lookup"><span data-stu-id="8d567-388">Run the app and select a **Details** link.</span></span> <span data-ttu-id="8d567-389">URL 的格式為 `http://localhost:5000/Students/Details?id=2`。</span><span class="sxs-lookup"><span data-stu-id="8d567-389">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="8d567-390">使用 (`?id=2`) 查詢字串來傳遞 Student ID。</span><span class="sxs-lookup"><span data-stu-id="8d567-390">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="8d567-391">更新 [編輯]、[詳細資料] 和 [刪除] Razor 頁面，以使用 `"{id:int}"` 路由範本。</span><span class="sxs-lookup"><span data-stu-id="8d567-391">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="8d567-392">將這些頁面每一頁的頁面指示詞從 `@page` 變更為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="8d567-392">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="8d567-393">對使用 "{id:int}" 路由範本的頁面提出的要求若 **未** 包含整數路由值，將傳回 HTTP 404 (找不到) 錯誤。</span><span class="sxs-lookup"><span data-stu-id="8d567-393">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="8d567-394">例如，`http://localhost:5000/Students/Details` 傳回 404 錯誤。</span><span class="sxs-lookup"><span data-stu-id="8d567-394">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="8d567-395">若要使識別碼成為選擇性，請將 `?` 附加至路由條件約束：</span><span class="sxs-lookup"><span data-stu-id="8d567-395">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="8d567-396">執行應用程式，按一下詳細資料連結，並確認 URL 作為路由資料 (`http://localhost:5000/Students/Details/2`) 來傳遞識別碼。</span><span class="sxs-lookup"><span data-stu-id="8d567-396">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="8d567-397">請勿全域變更 `@page` 至 `@page "{id:int}"`，這麼做會中斷 [首頁] 頁面與 [建立] 頁面之間的連結。</span><span class="sxs-lookup"><span data-stu-id="8d567-397">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="8d567-398">新增相關資料</span><span class="sxs-lookup"><span data-stu-id="8d567-398">Add related data</span></span>

<span data-ttu-id="8d567-399">Students [索引] 頁面的 Scaffold 程式碼不包含 `Enrollments` 屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-399">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="8d567-400">在本節中，`Enrollments` 集合的內容會顯示於 [詳細資料] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-400">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="8d567-401">*Pages/Students/Details.cshtml.cs* 的 `OnGetAsync` 方法使用 `FirstOrDefaultAsync` 方法來擷取單一 `Student` 實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-401">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="8d567-402">請新增下列醒目提示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="8d567-402">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="8d567-403">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 及 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 方法會使內容載入 `Student.Enrollments` 導覽屬性，以及位於每一個註冊中的 `Enrollment.Course` 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-403">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="8d567-404">將會在讀取相關資料教學課程中詳細檢視這些方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-404">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="8d567-405">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 方法在傳回實體未在目前內容中更新的案例下可改善效能。</span><span class="sxs-lookup"><span data-stu-id="8d567-405">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="8d567-406">`AsNoTracking` 稍後在本教學課程中將會討論。</span><span class="sxs-lookup"><span data-stu-id="8d567-406">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="8d567-407">在 [詳細資料] 頁面上顯示相關的註冊</span><span class="sxs-lookup"><span data-stu-id="8d567-407">Display related enrollments on the Details page</span></span>

<span data-ttu-id="8d567-408">開啟 *Pages/Students/Details.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="8d567-408">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="8d567-409">請新增下列醒目顯示的程式碼，以顯示一份註冊清單：</span><span class="sxs-lookup"><span data-stu-id="8d567-409">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="8d567-410">若在貼上程式碼之後，程式碼的縮排發生錯誤，請按 CTRL-K-D 修正它。</span><span class="sxs-lookup"><span data-stu-id="8d567-410">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="8d567-411">上述程式碼會以迴圈逐一巡覽 `Enrollments` 導覽屬性中的實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-411">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="8d567-412">針對每個註冊，會顯示課程標題及成績。</span><span class="sxs-lookup"><span data-stu-id="8d567-412">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="8d567-413">課程標題會從儲存於 Enrollments 實體之 `Course` 導覽屬性中的課程 (Course) 實體擷取。</span><span class="sxs-lookup"><span data-stu-id="8d567-413">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="8d567-414">執行應用程式，選取 [Students] 索引標籤，然後按一下學生的 [詳細資料] 連結。</span><span class="sxs-lookup"><span data-stu-id="8d567-414">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="8d567-415">將會顯示所選取學生的課程及成績清單。</span><span class="sxs-lookup"><span data-stu-id="8d567-415">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="8d567-416">更新 [建立] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-416">Update the Create page</span></span>

<span data-ttu-id="8d567-417">以下列程式碼更新 *Pages/Students/Create.cshtml.cs* 中的 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="8d567-417">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="8d567-418">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="8d567-418">TryUpdateModelAsync</span></span>

<span data-ttu-id="8d567-419">檢查 [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 程式碼：</span><span class="sxs-lookup"><span data-stu-id="8d567-419">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="8d567-420">在上述程式碼，`TryUpdateModelAsync<Student>` 從 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 中的 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 屬性中，使用已張貼的表單值來嘗試更新 `emptyStudent` 物件。</span><span class="sxs-lookup"><span data-stu-id="8d567-420">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="8d567-421">`TryUpdateModelAsync` 只會更新列出的屬性 (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="8d567-421">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="8d567-422">在上述範例中：</span><span class="sxs-lookup"><span data-stu-id="8d567-422">In the preceding sample:</span></span>

* <span data-ttu-id="8d567-423">第二個引數 (`"student", // Prefix`) 是使用來查閱值的前置詞。</span><span class="sxs-lookup"><span data-stu-id="8d567-423">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="8d567-424">不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="8d567-424">It's not case sensitive.</span></span>
* <span data-ttu-id="8d567-425">已張貼的表單值會使用[模型繫結](xref:mvc/models/model-binding)轉換至 `Student` 模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="8d567-425">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="8d567-426">大量指派 (overposting)</span><span class="sxs-lookup"><span data-stu-id="8d567-426">Overposting</span></span>

<span data-ttu-id="8d567-427">使用 `TryUpdateModel` 來更新具有已張貼值欄位是最安全的做法，因為它會防止大量指派 (overposting)。</span><span class="sxs-lookup"><span data-stu-id="8d567-427">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="8d567-428">例如，假設 Student 實體包含了一個此網頁不應更新或新增的 `Secret` 屬性：</span><span class="sxs-lookup"><span data-stu-id="8d567-428">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="8d567-429">即使應用程式在 `Secret` [建立/更新] 頁面上沒有欄位 Razor ，駭客也可以藉 `Secret` 由大量指派來設定值。</span><span class="sxs-lookup"><span data-stu-id="8d567-429">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="8d567-430">駭客仍可能使用 Fiddler 等工具，或是撰寫 JavaScript，來張貼 `Secret` 表單值。</span><span class="sxs-lookup"><span data-stu-id="8d567-430">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="8d567-431">原始程式碼並不會限制模型繫結器在建立 Student 執行個體時所使用的欄位。</span><span class="sxs-lookup"><span data-stu-id="8d567-431">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="8d567-432">無論駭客在 `Secret` 表單欄位中指定了什麼值，該值都會更新到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="8d567-432">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="8d567-433">下列影響顯示了 Fiddler 工具將 `Secret` 欄位 (其值為 "OverPost") 新增到表單的值中。</span><span class="sxs-lookup"><span data-stu-id="8d567-433">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler 新增 [Secret] 欄位](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="8d567-435">"OverPost" 值已成功新增至插入資料列的 `Secret` 屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-435">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="8d567-436">應用程式設計師從未打算在 [建立] 頁面設定 `Secret` 屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-436">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="8d567-437">檢視模型</span><span class="sxs-lookup"><span data-stu-id="8d567-437">View model</span></span>

<span data-ttu-id="8d567-438">檢視模型通常包含屬性中的子集，這些屬性包含在應用程式使用的模型中。</span><span class="sxs-lookup"><span data-stu-id="8d567-438">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="8d567-439">應用程式模型通常稱為網域模型。</span><span class="sxs-lookup"><span data-stu-id="8d567-439">The application model is often called the domain model.</span></span> <span data-ttu-id="8d567-440">網域模型通常會包含資料庫中對應實體所需要的所有屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-440">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="8d567-441">檢視模型只包含 UI 層所需要的屬性 (例如 [建立] 頁面)。</span><span class="sxs-lookup"><span data-stu-id="8d567-441">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="8d567-442">除了 view 模型之外，有些應用程式會使用系結模型或輸入模型，在 Razor 頁面頁面模型類別和瀏覽器之間傳遞資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-442">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="8d567-443">請看看下列 `Student` 檢視模型：</span><span class="sxs-lookup"><span data-stu-id="8d567-443">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="8d567-444">檢視模型提供防止大量指派 (overposting) 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="8d567-444">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="8d567-445">檢視模型只包含用來檢視 (顯示) 或更新的屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-445">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="8d567-446">下列程式碼會使用 `StudentVM` 檢視模型來建立一名新學生：</span><span class="sxs-lookup"><span data-stu-id="8d567-446">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="8d567-447">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 會設定這個物件的值，方法是藉由從其他 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 物件中讀取值。</span><span class="sxs-lookup"><span data-stu-id="8d567-447">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="8d567-448">`SetValues` 使用屬性名稱比對。</span><span class="sxs-lookup"><span data-stu-id="8d567-448">`SetValues` uses property name matching.</span></span> <span data-ttu-id="8d567-449">檢視模型類型不需要與模型類型相關，只需要有符合的屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-449">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="8d567-450">使用 `StudentVM` 需要 [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) 更新為使用 `StudentVM`，而不是使用 `Student`。</span><span class="sxs-lookup"><span data-stu-id="8d567-450">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="8d567-451">在 Razor 頁面中， `PageModel` 衍生類別是視圖模型。</span><span class="sxs-lookup"><span data-stu-id="8d567-451">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="8d567-452">更新 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-452">Update the Edit page</span></span>

<span data-ttu-id="8d567-453">為 [編輯] 頁面更新頁面模型。</span><span class="sxs-lookup"><span data-stu-id="8d567-453">Update the page model for the Edit page.</span></span> <span data-ttu-id="8d567-454">主要變更已反白顯示：</span><span class="sxs-lookup"><span data-stu-id="8d567-454">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="8d567-455">程式碼的變更與 [建立] 頁面相似，但有一些例外狀況：</span><span class="sxs-lookup"><span data-stu-id="8d567-455">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="8d567-456">`OnPostAsync` 具有選擇性 `id` 參數。</span><span class="sxs-lookup"><span data-stu-id="8d567-456">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="8d567-457">目前的學生資料會從資料庫中擷取出來，而不會建立一個空白的學生資料。</span><span class="sxs-lookup"><span data-stu-id="8d567-457">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="8d567-458">`FirstOrDefaultAsync` 已取代為 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)。</span><span class="sxs-lookup"><span data-stu-id="8d567-458">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="8d567-459">從主索引鍵選取實體時，`FindAsync` 是不錯的選擇。</span><span class="sxs-lookup"><span data-stu-id="8d567-459">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="8d567-460">請參閱 [FindAsync](#FindAsync) 以取得更詳細的資訊。</span><span class="sxs-lookup"><span data-stu-id="8d567-460">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="8d567-461">測試 [編輯] 頁面和 [建立] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-461">Test the Edit and Create pages</span></span>

<span data-ttu-id="8d567-462">建立並編輯一些學生實體。</span><span class="sxs-lookup"><span data-stu-id="8d567-462">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="8d567-463">實體狀態</span><span class="sxs-lookup"><span data-stu-id="8d567-463">Entity States</span></span>

<span data-ttu-id="8d567-464">無論記憶體中的實體是否與資料庫中相對應的資料列同步，資料庫內容都會持續追蹤。</span><span class="sxs-lookup"><span data-stu-id="8d567-464">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="8d567-465">呼叫 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 時，資料庫內容的同步資訊會決定該怎麼做。</span><span class="sxs-lookup"><span data-stu-id="8d567-465">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="8d567-466">例如，當傳遞一個新的實體到 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 方法時，該實體的狀態便會設定為 [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)。</span><span class="sxs-lookup"><span data-stu-id="8d567-466">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="8d567-467">呼叫 `SaveChangesAsync` 時，資料庫內容會發出 SQL INSERT 命令。</span><span class="sxs-lookup"><span data-stu-id="8d567-467">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="8d567-468">實體可為[下列狀態](/dotnet/api/microsoft.entityframeworkcore.entitystate)中的其中一個：</span><span class="sxs-lookup"><span data-stu-id="8d567-468">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="8d567-469">`Added`：實體還不存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="8d567-469">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="8d567-470">`SaveChanges` 方法會發出 INSERT 陳述式。</span><span class="sxs-lookup"><span data-stu-id="8d567-470">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="8d567-471">`Unchanged`：此實體沒有需要儲存的變更。</span><span class="sxs-lookup"><span data-stu-id="8d567-471">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="8d567-472">從資料庫讀取時，此實體將會有這個狀態。</span><span class="sxs-lookup"><span data-stu-id="8d567-472">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="8d567-473">`Modified`：實體中一部分或全部的屬性值已經過修改。</span><span class="sxs-lookup"><span data-stu-id="8d567-473">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="8d567-474">`SaveChanges` 方法會發出 UPDATE 陳述式。</span><span class="sxs-lookup"><span data-stu-id="8d567-474">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="8d567-475">`Deleted`：實體已遭標示刪除。</span><span class="sxs-lookup"><span data-stu-id="8d567-475">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="8d567-476">`SaveChanges` 方法會發出 DELETE 陳述式。</span><span class="sxs-lookup"><span data-stu-id="8d567-476">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="8d567-477">`Detached`：實體未獲得資料庫內容追蹤。</span><span class="sxs-lookup"><span data-stu-id="8d567-477">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="8d567-478">在桌面應用程式中，狀態變更通常會自動進行設定。</span><span class="sxs-lookup"><span data-stu-id="8d567-478">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="8d567-479">實體已讀取、已進行變更，且實體狀態會自動變更為 `Modified`。</span><span class="sxs-lookup"><span data-stu-id="8d567-479">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="8d567-480">呼叫 `SaveChanges` 會產生 SQL UPDATE 陳述式，此陳述式只會更新變更過的屬性。</span><span class="sxs-lookup"><span data-stu-id="8d567-480">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="8d567-481">在 Web 應用程式中，讀取實體並顯示其資料以供編輯之用的 `DbContext` 會在頁面轉譯之後遭到處置。</span><span class="sxs-lookup"><span data-stu-id="8d567-481">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="8d567-482">當呼叫頁面的 `OnPostAsync` 方法時，會發出新的 Web 要求，並且會擁有一個新的 `DbContext` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="8d567-482">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="8d567-483">在新的內容中重新讀取實體時，會模擬桌面處理流程。</span><span class="sxs-lookup"><span data-stu-id="8d567-483">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="8d567-484">更新 [刪除] 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-484">Update the Delete page</span></span>

<span data-ttu-id="8d567-485">在本節中，將新增程式碼來實作一個呼叫至 `SaveChanges` 失敗時的自訂錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="8d567-485">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="8d567-486">請新增字串來包含可能的錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="8d567-486">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="8d567-487">以下列程式碼取代 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="8d567-487">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="8d567-488">上述程式碼中包含選擇性參數 `saveChangesError`。</span><span class="sxs-lookup"><span data-stu-id="8d567-488">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="8d567-489">`saveChangesError` 會顯示出此方法是否已在刪除學生物件失敗後呼叫。</span><span class="sxs-lookup"><span data-stu-id="8d567-489">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="8d567-490">刪除作業可能會因為暫時性的網路問題而失敗。</span><span class="sxs-lookup"><span data-stu-id="8d567-490">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="8d567-491">暫時性的網路錯誤較可能是在雲端。</span><span class="sxs-lookup"><span data-stu-id="8d567-491">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="8d567-492">[刪除] 頁面 `OnGetAsync` 從 UI 中呼叫時，`saveChangesError` 為 false。</span><span class="sxs-lookup"><span data-stu-id="8d567-492">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="8d567-493">當 `OnPostAsync` 呼叫 `OnGetAsync` 時 (因刪除作業失敗)，則 `saveChangesError` 參數為 true。</span><span class="sxs-lookup"><span data-stu-id="8d567-493">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="8d567-494">[刪除] 頁面的 OnPostAsync 方法</span><span class="sxs-lookup"><span data-stu-id="8d567-494">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="8d567-495">使用下列程式碼取代 `OnPostAsync`：</span><span class="sxs-lookup"><span data-stu-id="8d567-495">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="8d567-496">上述程式碼會擷取選取的實體，然後呼叫 [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 方法來將實體的狀態設定為 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="8d567-496">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="8d567-497">當呼叫 `SaveChanges` 時，便會產生 SQL DELETE 命令。</span><span class="sxs-lookup"><span data-stu-id="8d567-497">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="8d567-498">如果 `Remove` 失敗：</span><span class="sxs-lookup"><span data-stu-id="8d567-498">If `Remove` fails:</span></span>

* <span data-ttu-id="8d567-499">攔截到資料庫例外狀況。</span><span class="sxs-lookup"><span data-stu-id="8d567-499">The DB exception is caught.</span></span>
* <span data-ttu-id="8d567-500">[刪除] 頁面的 `OnGetAsync` 方法會以 `saveChangesError=true` 呼叫。</span><span class="sxs-lookup"><span data-stu-id="8d567-500">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-no-locrazor-page"></a><span data-ttu-id="8d567-501">更新 [刪除] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="8d567-501">Update the Delete Razor Page</span></span>

<span data-ttu-id="8d567-502">將下列反白顯示的錯誤訊息新增至 [刪除] Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="8d567-502">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="8d567-503">測試刪除。</span><span class="sxs-lookup"><span data-stu-id="8d567-503">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="8d567-504">常見錯誤</span><span class="sxs-lookup"><span data-stu-id="8d567-504">Common errors</span></span>

<span data-ttu-id="8d567-505">Students/Index 或其他連結運作失常：</span><span class="sxs-lookup"><span data-stu-id="8d567-505">Students/Index or other links don't work:</span></span>

<span data-ttu-id="8d567-506">確認 Razor 頁面包含正確的指示詞 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="8d567-506">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="8d567-507">例如，[學生/索引] Razor 頁面 **不** 應包含路由範本：</span><span class="sxs-lookup"><span data-stu-id="8d567-507">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="8d567-508">每個 Razor 頁面都必須包含指示詞 `@page` 。</span><span class="sxs-lookup"><span data-stu-id="8d567-508">Each Razor Page must include the `@page` directive.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="8d567-509">其他資源</span><span class="sxs-lookup"><span data-stu-id="8d567-509">Additional resources</span></span>

* [<span data-ttu-id="8d567-510">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="8d567-510">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="8d567-511">[上一個](xref:data/ef-rp/intro) 
> [下一步](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="8d567-511">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end
