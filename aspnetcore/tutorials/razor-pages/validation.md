---
title: 第8部分，新增驗證
author: rick-anderson
description: 頁面上的第8部分教學課程系列 Razor 。
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/29/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 9774607b641005145bdb1c98d850c9ce79a25476
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486118"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="9f8ad-103">頁面上的第8部分教學課程系列 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="9f8ad-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9f8ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9f8ad-105">在本節中將驗證邏輯新增至 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="9f8ad-106">使用者建立或編輯電影時，就會強制執行驗證規則。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="9f8ad-107">驗證</span><span class="sxs-lookup"><span data-stu-id="9f8ad-107">Validation</span></span>

<span data-ttu-id="9f8ad-108">軟體開發的核心原則稱為 [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself)("**D** on't **R** epeat **Y** ourself", 不重複原則)。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="9f8ad-109">Razor 頁面會鼓勵開發環境指定一次，而且會反映在整個應用程式中。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="9f8ad-110">DRY 有助於：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-110">DRY can help:</span></span>

* <span data-ttu-id="9f8ad-111">降低應用程式中的程式碼數量。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="9f8ad-112">使程式碼較少出現錯誤，而且更容易進行測試和維護。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="9f8ad-113">頁面和 Entity Framework 所提供的驗證支援 Razor 是理想的策略範例：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="9f8ad-114">驗證規則會以宣告方式指定于模型類別中的一個位置。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="9f8ad-115">規則會在應用程式的任何位置強制執行。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="9f8ad-116">將驗證規則新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="9f8ad-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="9f8ad-117">`DataAnnotations`命名空間提供：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="9f8ad-118">一組內建的驗證屬性，會以宣告方式套用至類別或屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="9f8ad-119">格式化屬性 `[DataType]` ，例如格式化的說明，而且不提供任何驗證。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="9f8ad-120">更新 `Movie` 類別，以充分利用內建的 `[Required]`、`[StringLength]`、`[RegularExpression]` 和 `[Range]` 驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="9f8ad-121">驗證屬性會指定在其套用的模型屬性上強制執行的行為：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="9f8ad-122">`[Required]` 和 `[MinimumLength]` 屬性 (attribute) 指出屬性 (property) 必須具有值。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="9f8ad-123">無需防止使用者輸入空白字元來滿足這種驗證。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="9f8ad-124">`[RegularExpression]` 屬性則用來限制可輸入的字元。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="9f8ad-125">在上述程式碼中， `Genre` ：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="9f8ad-126">必須指使用字母。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-126">Must only use letters.</span></span>
  * <span data-ttu-id="9f8ad-127">第一個字母必須是大寫。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="9f8ad-128">不允許使用空格、數字和特殊字元。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="9f8ad-129">`RegularExpression` `Rating` ：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="9f8ad-130">第一個字元必須為大寫字母。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="9f8ad-131">允許後續空格中的特殊字元和數位。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="9f8ad-132">"PG-13" 適用于評等，但失敗 `Genre` 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="9f8ad-133">`[Range]` 屬性會將值限制在指定的範圍內。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="9f8ad-134">`[StringLength]`屬性可以設定字串屬性的最大長度，並選擇性地設定其最小長度。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="9f8ad-135">實數值型別（例如，、、、） `decimal` `int` 本質上 `float` `DateTime` 是必要的，而且不需要 `[Required]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="9f8ad-136">上述驗證規則是用於示範，不是生產系統的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="9f8ad-137">例如，上述程式可避免輸入只有兩個字元的電影，而不允許在中使用特殊字元 `Genre` 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="9f8ad-138">具有 ASP.NET Core 自動強制執行的驗證規則有助於：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="9f8ad-139">有助於讓應用程式更穩固。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="9f8ad-140">減少將無效資料儲存到資料庫的機會。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="9f8ad-141">頁面中的驗證錯誤 UI Razor</span><span class="sxs-lookup"><span data-stu-id="9f8ad-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="9f8ad-142">執行應用程式，並巡覽至 Pages/Movies。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="9f8ad-143">選取 **Create New** 連結。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-143">Select the **Create New** link.</span></span> <span data-ttu-id="9f8ad-144">使用某些無效值完成表單。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="9f8ad-145">當 jQuery 用戶端驗證偵測到錯誤時，它會顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![有多個 jQuery 用戶端驗證錯誤的電影檢視表單](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="9f8ad-147">請注意表單在包含無效值的每個欄位中自動呈現驗證錯誤訊息的方式。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="9f8ad-148">當使用者已停用 JavaScript 時，用戶端、使用 JavaScript 和 jQuery 以及伺服器端都會強制執行這些錯誤。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="9f8ad-149">最大的好處是在 [建立] 或 [編輯] 頁面中不需要變更 **任何** 程式碼。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="9f8ad-150">一旦將資料批註套用至模型之後，就會啟用驗證 UI。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="9f8ad-151">Razor在本教學課程中建立的頁面，會在模型類別的屬性上使用驗證屬性，自動挑選驗證規則 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="9f8ad-152">使用 Edit 頁面測試驗證，會套用相同的驗證。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="9f8ad-153">要一直到沒有任何用戶端驗證錯誤之後，才會將表單資料發佈到伺服器。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="9f8ad-154">請確認表單資料不會經由下列一或多種方式發佈：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="9f8ad-155">將中斷點放置在 `OnPostAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="9f8ad-156">選取 [ **建立** ] 或 [ **儲存**] 來提交表單。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="9f8ad-157">永遠不會叫用中斷點。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-157">The break point is never hit.</span></span>
* <span data-ttu-id="9f8ad-158">使用 [Fiddler 工具](https://www.telerik.com/fiddler)。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="9f8ad-159">使用瀏覽器開發人員工具來監視網路流量。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="9f8ad-160">伺服器端驗證</span><span class="sxs-lookup"><span data-stu-id="9f8ad-160">Server-side validation</span></span>

<span data-ttu-id="9f8ad-161">在瀏覽器中停用 JavaScript 時，提交含有錯誤的表單將發佈到伺服器。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="9f8ad-162">選擇性地測試伺服器端驗證：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="9f8ad-163">在瀏覽器中停用 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="9f8ad-164">您可以使用瀏覽器的開發人員工具來停用 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="9f8ad-165">如果無法在瀏覽器中停用 JavaScript，請嘗試另一個瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="9f8ad-166">在 Create 或 Edit 頁面的 `OnPostAsync` 方法中設定中斷點。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="9f8ad-167">提交含有無效資料的表單。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="9f8ad-168">確認模型狀態無效：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="9f8ad-169">或者， [在伺服器上停用用戶端驗證](xref:mvc/models/validation#disable-client-side-validation)。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="9f8ad-170">下列程式碼顯示稍早在此教學課程中包含 Scaffold 的部分 *Create.cshtml* 頁面。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="9f8ad-171">Create 和 Edit 頁面會使用它來：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="9f8ad-172">顯示初始表單。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-172">Display the initial form.</span></span>
* <span data-ttu-id="9f8ad-173">發生錯誤時重新顯示表單。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="9f8ad-174">[輸入標記協助程式](xref:mvc/views/working-with-forms)會使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 屬性，並產生在用戶端上進行 jQuery 驗證所需的 HTML 屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="9f8ad-175">[驗證標記協助程式](xref:mvc/views/working-with-forms#the-validation-tag-helpers)會顯示驗證錯誤。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="9f8ad-176">如需詳細資訊，請參閱[驗證](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="9f8ad-177">Create 和 Edit 頁面中沒有任何驗證規則。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="9f8ad-178">只有在 `Movie` 類別中才能指定驗證規則和錯誤字串。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="9f8ad-179">這些驗證規則會自動套用至 Razor 編輯模型的頁面 `Movie` 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="9f8ad-180">當驗證邏輯需要變更時，它只會在模型中進行。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="9f8ad-181">驗證會一致地套用到整個應用程式中，而驗證邏輯則定義于一個位置。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="9f8ad-182">位於一個位置的驗證有助於讓程式碼保持整潔，並可讓您更容易進行維護和更新。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="9f8ad-183">使用 DataType 屬性</span><span class="sxs-lookup"><span data-stu-id="9f8ad-183">Use DataType Attributes</span></span>

<span data-ttu-id="9f8ad-184">檢查 `Movie` 類別。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-184">Examine the `Movie` class.</span></span> <span data-ttu-id="9f8ad-185">除了一組內建的驗證屬性之外，`System.ComponentModel.DataAnnotations` 命名空間還提供了格式屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="9f8ad-186">`[DataType]` 屬性會套用到 `ReleaseDate` 和 `Price` 屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="9f8ad-187">`[DataType]`屬性提供：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="9f8ad-188">查看引擎格式化資料的提示。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="9f8ad-189">提供屬性，例如 `<a>` URL 和 `<a href="mailto:EmailAddress.com">` 電子郵件。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="9f8ad-190">使用 `[RegularExpression]` 屬性來驗證資料的格式。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="9f8ad-191">`[DataType]` 屬性可用於指定比資料庫內建類型更特定的資料類型。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="9f8ad-192">`[DataType]` 屬性不是驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="9f8ad-193">在範例應用程式中，只會顯示日期，而不含時間。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="9f8ad-194">`DataType`列舉提供許多資料類型，例如、、 `Date` `Time` `PhoneNumber` 、、等等 `Currency` `EmailAddress` 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="9f8ad-195">`[DataType]`屬性：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="9f8ad-196">可以讓應用程式自動提供型別特有的功能。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="9f8ad-197">例如，可針對 `DataType.EmailAddress` 建立 `mailto:` 連結。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="9f8ad-198">可以 `DataType.Date` 在支援 HTML5 的瀏覽器中提供日期選擇器。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="9f8ad-199">發出 HTML 5 `data-` ，發音為「資料虛線」，這是 html 5 瀏覽器使用的屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="9f8ad-200">請勿 **提供任何** 驗證。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="9f8ad-201">`DataType.Date` 未指定顯示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="9f8ad-202">根據預設，將依據以伺服器 `CultureInfo` 為基礎的預設格式顯示資料欄位。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="9f8ad-203">`[Column(TypeName = "decimal(18, 2)")]` 資料註解為必要項，因此 Entity Framework Core 可將 `Price` 正確對應到資料庫中的貨幣。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="9f8ad-204">如需詳細資訊，請參閱[資料類型](/ef/core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="9f8ad-205">`[DisplayFormat]` 屬性用來明確指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="9f8ad-206">`ApplyFormatInEditMode`設定會指定在顯示值以供編輯時，將套用格式設定。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="9f8ad-207">某些欄位可能不需要該行為。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="9f8ad-208">例如，在貨幣值中，在編輯 UI 中通常不需要貨幣符號。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="9f8ad-209">`[DisplayFormat]` 屬性可以單獨使用，但通常最好是使用 `[DataType]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="9f8ad-210">`[DataType]` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="9f8ad-211">`[DataType]`屬性提供下列無法使用的優點 `[DisplayFormat]` ：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="9f8ad-212">瀏覽器可以啟用 HTML5 功能，例如顯示行事曆控制項、地區設定適當的貨幣符號、電子郵件連結等等。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="9f8ad-213">根據預設，瀏覽器會根據其地區設定，使用正確的格式來呈現資料。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="9f8ad-214">`[DataType]` 屬性可以啟用 ASP.NET Core 架構，來選擇用於呈現資料的正確欄位範本。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="9f8ad-215">`DisplayFormat`如果本身使用，則會使用字串範本。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="9f8ad-216">**注意：** jQuery 驗證不適用於 `[Range]` 屬性和 `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="9f8ad-217">例如，下列程式碼一律會顯示用戶端驗證錯誤，即使當日期位在指定範圍中也一樣：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="9f8ad-218">最佳做法是避免在模型中編譯硬性日期，因此 `[Range]` `DateTime` 不建議使用屬性。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="9f8ad-219">使用日期範圍的設定以及其他受頻繁 [變更的值](xref:fundamentals/configuration/index) ，而不是在程式碼中指定。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="9f8ad-220">下列程式碼會顯示一行上的結合屬性：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="9f8ad-221">[入門 Razor 頁面和 EF Core](xref:data/ef-rp/intro) 會顯示頁面的 advanced EF Core 作業 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="9f8ad-222">套用移轉</span><span class="sxs-lookup"><span data-stu-id="9f8ad-222">Apply migrations</span></span>

<span data-ttu-id="9f8ad-223">套用至類別的 DataAnnotations 會變更架構。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="9f8ad-224">例如，套用至 `Title` 欄位的 DataAnnotations：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="9f8ad-225">將字元限制為 60 個。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="9f8ad-226">不允許 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9f8ad-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f8ad-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9f8ad-228">`Movie` 資料表目前具有下列結構描述：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-228">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="9f8ad-229">上述結構描述變更不會造成 EF 擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="9f8ad-230">不過，請建立移轉，讓結構描述與模型一致。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="9f8ad-231"> 從 [工具] 功能表中，選取 [NuGet 套件管理員] > [套件管理員主控台]。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="9f8ad-232">在 PMC 中，輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="9f8ad-233">`Update-Database` 會執行 `New_DataAnnotations` 類別的 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="9f8ad-234">檢查 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="9f8ad-235">已更新的 `Movie` 資料表具有下列結構描述：</span><span class="sxs-lookup"><span data-stu-id="9f8ad-235">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9f8ad-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9f8ad-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9f8ad-237">SQLite 不需要移轉。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="9f8ad-238">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="9f8ad-238">Publish to Azure</span></span>

<span data-ttu-id="9f8ad-239">如需部署至 Azure 的詳細資訊，請參閱 [教學課程：使用 SQL Database 在 azure 中建立 ASP.NET Core 應用程式](/azure/app-service/tutorial-dotnetcore-sqldb-app)。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="9f8ad-240">感謝您完成本頁面簡介 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="9f8ad-241">[入門 Razor 頁面和 EF Core](xref:data/ef-rp/intro) 是本教學課程的最佳追蹤。</span><span class="sxs-lookup"><span data-stu-id="9f8ad-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f8ad-242">其他資源</span><span class="sxs-lookup"><span data-stu-id="9f8ad-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="9f8ad-243">上一步：新增欄位</span><span class="sxs-lookup"><span data-stu-id="9f8ad-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
