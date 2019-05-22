---
title: 設定 Blazor 的連結器
author: guardrex
description: 了解如何在組建 Blazor 應用程式時，控制中繼語言 (IL) 連結器。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 00676d4311f8955c3c1ef38d31219d62ea9f4a25
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887773"
---
# <a name="configure-the-linker-for-blazor"></a><span data-ttu-id="dba41-103">設定 Blazor 的連結器</span><span class="sxs-lookup"><span data-stu-id="dba41-103">Configure the Linker for Blazor</span></span>

<span data-ttu-id="dba41-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dba41-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dba41-105">Blazor 會在發行組建期間執行[中繼語言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 連結，以從應用程式的輸出組件中移除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="dba41-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a Release build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="dba41-106">請使用下列其中一種方法來控制組件連結：</span><span class="sxs-lookup"><span data-stu-id="dba41-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="dba41-107">使用 [MSBuild 屬性](#disable-linking-with-a-msbuild-property)來全域停用連結。</span><span class="sxs-lookup"><span data-stu-id="dba41-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="dba41-108">使用[設定檔](#control-linking-with-a-configuration-file)來根據組件控制連結。</span><span class="sxs-lookup"><span data-stu-id="dba41-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="dba41-109">使用 MSBuild 屬性來停用連結</span><span class="sxs-lookup"><span data-stu-id="dba41-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="dba41-110">組建應用程式時，預設會在版本模式中啟用連結，其中包括發佈。</span><span class="sxs-lookup"><span data-stu-id="dba41-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="dba41-111">若要停用所有組件的連結，請在專案檔中將 `<BlazorLinkOnBuild>` MSBuild 屬性設為 `false`：</span><span class="sxs-lookup"><span data-stu-id="dba41-111">To disable linking for all assemblies, set the `<BlazorLinkOnBuild>` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="dba41-112">使用組態檔控制連結</span><span class="sxs-lookup"><span data-stu-id="dba41-112">Control linking with a configuration file</span></span>

<span data-ttu-id="dba41-113">透過提供 XML 設定檔，並將此檔案指定為專案檔中的 MSBuild 項目，即可根據組件控制連結：</span><span class="sxs-lookup"><span data-stu-id="dba41-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="dba41-114">*Linker.xml*：</span><span class="sxs-lookup"><span data-stu-id="dba41-114">*Linker.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/aspnet/Blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="dba41-115">如需詳細資訊，請參閱 [IL 連結器：XML 描述項的語法](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor)。</span><span class="sxs-lookup"><span data-stu-id="dba41-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>