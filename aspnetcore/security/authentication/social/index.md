---
title: ASP.NET Core 中的 Facebook、Google 及外部提供者驗證
author: rick-anderson
description: 本教學課程示範如何使用 OAuth 2.0 搭配外部驗證提供者來建立 ASP.NET Core 應用程式。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: f7bc7c6a14d014f8c3c283fb09718b9191b7b478
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035823"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>ASP.NET Core 中的 Facebook、Google 及外部提供者驗證

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

本教學課程示範如何建立 ASP.NET Core 應用程式，讓使用者能夠使用 OAuth 2.0 搭配外部驗證提供者的認證進行登入。

[Facebook](xref:security/authentication/facebook-logins)、 [Twitter](xref:security/authentication/twitter-logins)、 [Google](xref:security/authentication/google-logins)和 [Microsoft](xref:security/authentication/microsoft-logins) 提供者會在下列各節中討論，並使用本文中建立的入門專案。 您可透過 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 和 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) 這類協力廠商套件，取得其他提供者。

讓使用者透過現有認證登入：

* 對使用者而言十分方便。
* 可將複雜的登入程序管理作業轉移至協力廠商。

如需社交登入如何帶動流量和客戶轉換的範例，請參閱 [Facebook](https://www.facebook.com/unsupportedbrowser) 和 [Twitter](https://dev.twitter.com/resources/case-studies) 的案例研究。

## <a name="create-a-new-aspnet-core-project"></a>建立新的 ASP.NET Core 專案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 建立新專案。
* 選取 [ASP.NET Core Web 應用程式] 和 [下一步]。
* 提供 **專案名稱** 並確認或變更 **位置**。 選取 [建立]。
* 在下拉式 (**ASP.NET Core {X. Y}**) 中選取最新版本的 ASP.NET Core，然後選取 [ **Web 應用程式**]。
* 選取 [驗證] 下的 [變更]，並將驗證設定為 [個別使用者帳戶]。 選取 [確定]。
* 在 [建立新的 ASP.NET Core Web 應用程式] 視窗中選取 [建立]。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 開啟終端機。  針對 Visual Studio Code 您可以開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。

* 將目錄 (`cd`) 變更為其中包含專案的資料夾。

* 針對 Windows，請執行下列命令：

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  針對 macOS 和 Linux，請執行下列命令：

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * 此 `dotnet new` 命令會 Razor 在 *WebApp1* 資料夾中建立新的頁面專案。
  * `-au Individual` 會建立程式碼以進行個別驗證。
  * `-uld` 使用 LocalDB，此為適用于 Windows 的 SQL Server Express 輕量版本。 省略 `-uld` 以使用 SQLite。
  * `code` 命令會在新的 Visual Studio Code 執行個體中開啟 [WebApp1] 資料夾。

---

## <a name="apply-migrations"></a>套用移轉

* 執行應用程式並選取 [登錄] 連結。
* 輸入新帳戶的電子郵件和密碼，然後選取 [註冊]。
* 遵循指示以套用移轉。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>使用 SecretManager 來儲存登入提供者指派的權杖

社交登入提供者會在註冊程序期間指派 **應用程式識別碼** 和 **應用程式密碼** 權杖。 確切權杖名稱會依提供者而有所不同。 這些權杖代表您的應用程式用來存取其 API 的認證。 這些權杖構成「使用者秘密」，可透過 [秘密管理員](xref:security/app-secrets#secret-manager)的協助連結至您的應用程式設定。 使用者密碼是在設定檔中儲存權杖（例如）的更安全替代方法 *appsettings.json* 。

> [!IMPORTANT]
> 祕密管理員僅供用於開發用途。 您可以透過 [Azure Key Vault 設定提供者](xref:security/key-vault-configuration)儲存及保護 Azure 測試與生產祕密。

請遵循 [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) (在 ASP.NET Core 開發過程中安全地儲存應用程式祕密) 主題中的步驟，儲存下方各個登入提供者指派的權杖。

## <a name="setup-login-providers-required-by-your-application"></a>設定應用程式所需的登入提供者

若要將應用程式設定為使用相應的提供者，請使用下列主題：

* [Facebook](xref:security/authentication/facebook-logins) 指示
* [Twitter](xref:security/authentication/twitter-logins) 指示
* [Google](xref:security/authentication/google-logins) 指示
* [Microsoft](xref:security/authentication/microsoft-logins) 指示
* [其他提供者](xref:security/authentication/otherlogins)指示

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>選擇性地設定密碼

當您使用外部登入提供者註冊時，您並未向應用程式註冊密碼。 這樣可以減輕您建立網站密碼與記住密碼的壓力，但這也會讓您依賴外部登入提供者。 如果無法使用外部登入提供者，您就無法登入網站。

若要建立密碼，並使用您在外部提供者登入程序期間所設的電子郵件進行登入：

* 選取右上角的 [Hello &lt;電子郵件別名&gt;] 連結以瀏覽至 [管理] 檢視。

![Web 應用程式的 [管理] 檢視](index/_static/pass1a.png)

* 選取 [建立] 

![[設定密碼] 頁面](index/_static/pass2a.png)

* 設定有效的密碼，以便使用此密碼與電子郵件進行登入。

## <a name="next-steps"></a>後續步驟

* 如需如何自訂登入按鈕的詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore.Docs/issues/10563) 。
* 本文介紹了外部驗證，並說明將外部登入新增至 ASP.NET Core 應用程式所需的必要條件。
* 請參考提供者的特定頁面，以設定應用程式所需的提供者登入項目。
* 您想要保存有關使用者與其存取和重新整理權杖的額外資料。 如需詳細資訊，請參閱<xref:security/authentication/social/additional-claims>。
