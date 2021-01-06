::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> <span data-ttu-id="cd4c8-101">**Catch-all** 參數可能會因為路由中的 [錯誤](https://github.com/dotnet/aspnetcore/issues/18677)而不正確地符合路由。</span><span class="sxs-lookup"><span data-stu-id="cd4c8-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="cd4c8-102">受此 bug 影響的應用程式具有下列特性：</span><span class="sxs-lookup"><span data-stu-id="cd4c8-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="cd4c8-103">Catch-all 路由，例如 `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="cd4c8-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="cd4c8-104">Catch-all 路由無法符合它應該符合的要求。</span><span class="sxs-lookup"><span data-stu-id="cd4c8-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="cd4c8-105">移除其他路由會讓全部攔截路由開始運作。</span><span class="sxs-lookup"><span data-stu-id="cd4c8-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="cd4c8-106">如需遇到此錯誤的範例案例，請參閱 GitHub bug [18677](https://github.com/dotnet/aspnetcore/issues/18677) 和 [16579](https://github.com/dotnet/aspnetcore/issues/16579) 。</span><span class="sxs-lookup"><span data-stu-id="cd4c8-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="cd4c8-107">[.Net Core 3.1.301 SDK 和更新版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)包含此錯誤的加入宣告修正。</span><span class="sxs-lookup"><span data-stu-id="cd4c8-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="cd4c8-108">下列程式碼會設定可修正此錯誤的內部參數：</span><span class="sxs-lookup"><span data-stu-id="cd4c8-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end