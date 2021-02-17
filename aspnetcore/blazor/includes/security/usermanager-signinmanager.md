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
ms.openlocfilehash: d6c3c1800bd341cc1c21ec6ec80421932dae61df
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551415"
---
## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="30cd1-101">UserManager 和使用</span><span class="sxs-lookup"><span data-stu-id="30cd1-101">UserManager and SignInManager</span></span>

<span data-ttu-id="30cd1-102">設定伺服器應用程式需要的使用者識別碼宣告類型：</span><span class="sxs-lookup"><span data-stu-id="30cd1-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="30cd1-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> 或 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 在 API 端點中。</span><span class="sxs-lookup"><span data-stu-id="30cd1-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="30cd1-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> 詳細資料，例如使用者名稱、電子郵件地址或鎖定結束時間。</span><span class="sxs-lookup"><span data-stu-id="30cd1-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="30cd1-105">在 `Startup.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="30cd1-105">In `Startup.ConfigureServices`:</span></span>

```csharp
using System.Security.Claims;

...

services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="30cd1-106">以下 `WeatherForecastController` <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> 會在 `Get` 呼叫方法時記錄：</span><span class="sxs-lookup"><span data-stu-id="30cd1-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using {APP NAMESPACE}.Server.Models;
using {APP NAMESPACE}.Shared;

namespace {APP NAMESPACE}.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            this.userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```
