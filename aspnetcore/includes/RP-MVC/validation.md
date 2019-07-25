
## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="68f77-101">將驗證規則新增至電影模型</span><span class="sxs-lookup"><span data-stu-id="68f77-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="68f77-102">開啟 *Movie.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="68f77-102">Open the *Movie.cs* file.</span></span> <span data-ttu-id="68f77-103">DataAnnotations 命名空間提供一組內建的驗證屬性 (attribute)，其以宣告方式套用至類別或屬性 (property)。</span><span class="sxs-lookup"><span data-stu-id="68f77-103">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="68f77-104">DataAnnotations 也包含格式化屬性 (如 `DataType`)，可協助進行格式化，但不提供任何驗證。</span><span class="sxs-lookup"><span data-stu-id="68f77-104">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="68f77-105">更新 `Movie` 類別，以充分利用內建的 `Required`、`StringLength`、`RegularExpression` 和 `Range` 驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="68f77-105">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="68f77-106">驗證屬性會指定您想要對套用目標模型屬性強制執行的行為：</span><span class="sxs-lookup"><span data-stu-id="68f77-106">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="68f77-107">`Required` 和 `MinimumLength` 屬性 (attribute) 指出屬性 (property) 必須是值；但無法防止使用者輸入空格以滿足此驗證。</span><span class="sxs-lookup"><span data-stu-id="68f77-107">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="68f77-108">`RegularExpression` 屬性則用來限制可輸入的字元。</span><span class="sxs-lookup"><span data-stu-id="68f77-108">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="68f77-109">在上述程式碼中，"Genre"：</span><span class="sxs-lookup"><span data-stu-id="68f77-109">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="68f77-110">必須指使用字母。</span><span class="sxs-lookup"><span data-stu-id="68f77-110">Must only use letters.</span></span>
  * <span data-ttu-id="68f77-111">第一個字母必須是大寫。</span><span class="sxs-lookup"><span data-stu-id="68f77-111">The first letter is required to be uppercase.</span></span> <span data-ttu-id="68f77-112">不允許使用空格、數字和特殊字元。</span><span class="sxs-lookup"><span data-stu-id="68f77-112">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="68f77-113">`RegularExpression` "Rating"：</span><span class="sxs-lookup"><span data-stu-id="68f77-113">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="68f77-114">第一個字元必須為大寫字母。</span><span class="sxs-lookup"><span data-stu-id="68f77-114">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="68f77-115">允許後續空格中的特殊字元和數位。</span><span class="sxs-lookup"><span data-stu-id="68f77-115">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="68f77-116">"PG-13" 對分級而言有效，但不適用於 "Genre"。</span><span class="sxs-lookup"><span data-stu-id="68f77-116">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="68f77-117">`Range` 屬性會將值限制在指定的範圍內。</span><span class="sxs-lookup"><span data-stu-id="68f77-117">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="68f77-118">`StringLength` 屬性可讓您設定字串屬性的最大長度，並選擇性設定其最小長度。</span><span class="sxs-lookup"><span data-stu-id="68f77-118">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="68f77-119">實值型別 (如`decimal`、`int`、`float`、`DateTime`) 原本就是必要項目，而且不需要 `[Required]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="68f77-119">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="68f77-120">擁有 ASP.NET Core 自動強制執行的驗證規則有助於讓您的應用程式更穩固。</span><span class="sxs-lookup"><span data-stu-id="68f77-120">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="68f77-121">它也確保您不會忘記要驗證某些項目，不小心讓不正確的資料進入資料庫。</span><span class="sxs-lookup"><span data-stu-id="68f77-121">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>