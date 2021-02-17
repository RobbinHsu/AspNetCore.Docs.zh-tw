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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552619"
---
> [!NOTE]
> 在本教學課程中，請盡可能使用 Entity Framework Core「移轉」功能。 移轉可更新資料庫結構描述，以符合資料模型中的變更。 不過，移轉只能進行 EF Core 提供者支援的變更類型，SQLite 提供者的功能則有限制。 例如，其支援新增資料行，但不支援移除或變更資料行。 如果您建立移轉來移除或變更資料行，`ef migrations add` 命令會成功，但 `ef database update` 命令會失敗。 由於這些限制，本教學課程不會將移轉用於 SQLite 結構描述變更。 反之，當結構描述變更時，請您卸除並重新建立資料庫。
>
>SQLite 限制的因應措施是手動撰寫移轉程式碼，以在資料表有所變更時執行資料表重建。 重建資料表包含：
>
>* 建立新的資料表。
>* 將資料從舊的資料表複製到新的資料表。
>* 卸除舊的資料表。
>* 重新命名新資料表。
>
>如需詳細資訊，請參閱下列資源：
>
> * [SQLite EF Core 資料庫提供者限制](/ef/core/providers/sqlite/limitations)
> * [自訂移轉程式碼](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [資料植入](/ef/core/modeling/data-seeding)
  * [SQLite ALTER TABLE 陳述式](https://sqlite.org/lang_altertable.html) \(英文\)