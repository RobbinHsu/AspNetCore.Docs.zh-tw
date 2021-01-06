---
title: 使用 dotnet-grpc 管理 Protobuf 參考
author: juntaoluo
description: 瞭解如何使用 dotnet grpc 的全域工具來新增、更新、移除及列出 Protobuf 參考。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059906"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="fa168-103">使用 dotnet-grpc 管理 Protobuf 參考</span><span class="sxs-lookup"><span data-stu-id="fa168-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="fa168-104">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="fa168-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="fa168-105">`dotnet-grpc` 是一種 .NET Core 通用工具，可用於管理 [Protobuf (*proto*)](xref:grpc/basics#proto-file) .net gRPC 專案中的參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="fa168-106">您可以使用此工具來新增、重新整理、移除及列出 Protobuf 參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="fa168-107">安裝</span><span class="sxs-lookup"><span data-stu-id="fa168-107">Installation</span></span>

<span data-ttu-id="fa168-108">若要安裝 `dotnet-grpc` [.Net Core 通用工具](/dotnet/core/tools/global-tools)，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="fa168-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="fa168-109">新增參考</span><span class="sxs-lookup"><span data-stu-id="fa168-109">Add references</span></span>

<span data-ttu-id="fa168-110">`dotnet-grpc` 可以用來將 Protobuf 參考當做 `<Protobuf />` 專案新增至 *.csproj* 檔案：</span><span class="sxs-lookup"><span data-stu-id="fa168-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="fa168-111">Protobuf 參考可用來產生 c # 用戶端和/或伺服器資產。</span><span class="sxs-lookup"><span data-stu-id="fa168-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="fa168-112">此 `dotnet-grpc` 工具可以：</span><span class="sxs-lookup"><span data-stu-id="fa168-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="fa168-113">從磁片上的本機檔案建立 Protobuf 參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="fa168-114">從 URL 指定的遠端檔案建立 Protobuf 參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="fa168-115">確定已將正確的 gRPC 套件相依性新增至專案。</span><span class="sxs-lookup"><span data-stu-id="fa168-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="fa168-116">例如，將 `Grpc.AspNetCore` 套件新增至 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="fa168-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="fa168-117">`Grpc.AspNetCore` 包含 gRPC 伺服器和用戶端程式庫和工具支援。</span><span class="sxs-lookup"><span data-stu-id="fa168-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="fa168-118">或者， `Grpc.Net.Client` `Grpc.Tools` `Google.Protobuf` 只會將包含 gRPC 用戶端程式庫和工具支援的和套件新增至主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="fa168-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="fa168-119">新增檔案</span><span class="sxs-lookup"><span data-stu-id="fa168-119">Add file</span></span>

<span data-ttu-id="fa168-120">此 `add-file` 命令可用來將磁片上的本機檔案新增為 Protobuf 參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="fa168-121">提供的檔案路徑：</span><span class="sxs-lookup"><span data-stu-id="fa168-121">The file paths provided:</span></span>

* <span data-ttu-id="fa168-122">可以相對於目前的目錄或絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="fa168-123">可能包含以模式為基礎的檔案萬用字元的[萬用字元。](https://wikipedia.org/wiki/Glob_(programming))</span><span class="sxs-lookup"><span data-stu-id="fa168-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="fa168-124">如果有任何檔案位於專案目錄之外， `Link` 就會加入專案，以在 Visual Studio 的資料夾下顯示檔案 `Protos` 。</span><span class="sxs-lookup"><span data-stu-id="fa168-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="fa168-125">使用方式</span><span class="sxs-lookup"><span data-stu-id="fa168-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="fa168-126">引數</span><span class="sxs-lookup"><span data-stu-id="fa168-126">Arguments</span></span>

| <span data-ttu-id="fa168-127">引數</span><span class="sxs-lookup"><span data-stu-id="fa168-127">Argument</span></span> | <span data-ttu-id="fa168-128">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-128">Description</span></span> |
|-|-|
| <span data-ttu-id="fa168-129">files</span><span class="sxs-lookup"><span data-stu-id="fa168-129">files</span></span> | <span data-ttu-id="fa168-130">Protobuf 檔參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-130">The protobuf file references.</span></span> <span data-ttu-id="fa168-131">這些可以是本機 protobuf 檔案的 glob 路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="fa168-132">選項</span><span class="sxs-lookup"><span data-stu-id="fa168-132">Options</span></span>

| <span data-ttu-id="fa168-133">Short 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-133">Short option</span></span> | <span data-ttu-id="fa168-134">Long 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-134">Long option</span></span> | <span data-ttu-id="fa168-135">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="fa168-136">-p</span><span class="sxs-lookup"><span data-stu-id="fa168-136">-p</span></span> | <span data-ttu-id="fa168-137">--project</span><span class="sxs-lookup"><span data-stu-id="fa168-137">--project</span></span> | <span data-ttu-id="fa168-138">要操作之專案檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-138">The path to the project file to operate on.</span></span> <span data-ttu-id="fa168-139">如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。</span><span class="sxs-lookup"><span data-stu-id="fa168-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="fa168-140">-S</span><span class="sxs-lookup"><span data-stu-id="fa168-140">-s</span></span> | <span data-ttu-id="fa168-141">--服務</span><span class="sxs-lookup"><span data-stu-id="fa168-141">--services</span></span> | <span data-ttu-id="fa168-142">應產生的 gRPC 服務類型。</span><span class="sxs-lookup"><span data-stu-id="fa168-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="fa168-143">如果 `Default` 指定了， `Both` 就會用於 Web 專案，並用於 `Client` 非 Web 專案。</span><span class="sxs-lookup"><span data-stu-id="fa168-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="fa168-144">接受的值為 `Both` 、 `Client` 、 `Default` 、 `None` 、 `Server` 。</span><span class="sxs-lookup"><span data-stu-id="fa168-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="fa168-145">-i</span><span class="sxs-lookup"><span data-stu-id="fa168-145">-i</span></span> | <span data-ttu-id="fa168-146">--其他-匯入-目錄</span><span class="sxs-lookup"><span data-stu-id="fa168-146">--additional-import-dirs</span></span> | <span data-ttu-id="fa168-147">解析 protobuf 檔匯入時要使用的其他目錄。</span><span class="sxs-lookup"><span data-stu-id="fa168-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="fa168-148">這是以分號分隔的路徑清單。</span><span class="sxs-lookup"><span data-stu-id="fa168-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="fa168-149">--存取</span><span class="sxs-lookup"><span data-stu-id="fa168-149">--access</span></span> | <span data-ttu-id="fa168-150">要用於產生的 c # 類別的存取修飾詞。</span><span class="sxs-lookup"><span data-stu-id="fa168-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="fa168-151">預設值是 `Public`。</span><span class="sxs-lookup"><span data-stu-id="fa168-151">The default value is `Public`.</span></span> <span data-ttu-id="fa168-152">接受的值為 `Internal` 和 `Public`。</span><span class="sxs-lookup"><span data-stu-id="fa168-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="fa168-153">新增 URL</span><span class="sxs-lookup"><span data-stu-id="fa168-153">Add URL</span></span>

<span data-ttu-id="fa168-154">此 `add-url` 命令是用來新增來源 URL 所指定的遠端檔案，做為 Protobuf 參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="fa168-155">必須提供檔案路徑，以指定遠端檔案的下載位置。</span><span class="sxs-lookup"><span data-stu-id="fa168-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="fa168-156">檔案路徑可以相對於目前的目錄或絕對路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="fa168-157">如果檔案路徑是在專案目錄外， `Link` 則會新增一個專案，以在 Visual Studio 的虛擬資料夾下顯示該檔案 `Protos` 。</span><span class="sxs-lookup"><span data-stu-id="fa168-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="fa168-158">使用方式</span><span class="sxs-lookup"><span data-stu-id="fa168-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="fa168-159">引數</span><span class="sxs-lookup"><span data-stu-id="fa168-159">Arguments</span></span>

| <span data-ttu-id="fa168-160">引數</span><span class="sxs-lookup"><span data-stu-id="fa168-160">Argument</span></span> | <span data-ttu-id="fa168-161">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-161">Description</span></span> |
|-|-|
| <span data-ttu-id="fa168-162">url</span><span class="sxs-lookup"><span data-stu-id="fa168-162">url</span></span> | <span data-ttu-id="fa168-163">遠端 protobuf 檔的 URL。</span><span class="sxs-lookup"><span data-stu-id="fa168-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="fa168-164">選項</span><span class="sxs-lookup"><span data-stu-id="fa168-164">Options</span></span>

| <span data-ttu-id="fa168-165">Short 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-165">Short option</span></span> | <span data-ttu-id="fa168-166">Long 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-166">Long option</span></span> | <span data-ttu-id="fa168-167">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="fa168-168">-o</span><span class="sxs-lookup"><span data-stu-id="fa168-168">-o</span></span> | <span data-ttu-id="fa168-169">--output</span><span class="sxs-lookup"><span data-stu-id="fa168-169">--output</span></span> | <span data-ttu-id="fa168-170">指定遠端 protobuf 檔的下載路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="fa168-171">這是必要選項。</span><span class="sxs-lookup"><span data-stu-id="fa168-171">This is a required option.</span></span>
| <span data-ttu-id="fa168-172">-p</span><span class="sxs-lookup"><span data-stu-id="fa168-172">-p</span></span> | <span data-ttu-id="fa168-173">--project</span><span class="sxs-lookup"><span data-stu-id="fa168-173">--project</span></span> | <span data-ttu-id="fa168-174">要操作之專案檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-174">The path to the project file to operate on.</span></span> <span data-ttu-id="fa168-175">如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。</span><span class="sxs-lookup"><span data-stu-id="fa168-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="fa168-176">-S</span><span class="sxs-lookup"><span data-stu-id="fa168-176">-s</span></span> | <span data-ttu-id="fa168-177">--服務</span><span class="sxs-lookup"><span data-stu-id="fa168-177">--services</span></span> | <span data-ttu-id="fa168-178">應產生的 gRPC 服務類型。</span><span class="sxs-lookup"><span data-stu-id="fa168-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="fa168-179">如果 `Default` 指定了， `Both` 就會用於 Web 專案，並用於 `Client` 非 Web 專案。</span><span class="sxs-lookup"><span data-stu-id="fa168-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="fa168-180">接受的值為 `Both` 、 `Client` 、 `Default` 、 `None` 、 `Server` 。</span><span class="sxs-lookup"><span data-stu-id="fa168-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="fa168-181">-i</span><span class="sxs-lookup"><span data-stu-id="fa168-181">-i</span></span> | <span data-ttu-id="fa168-182">--其他-匯入-目錄</span><span class="sxs-lookup"><span data-stu-id="fa168-182">--additional-import-dirs</span></span> | <span data-ttu-id="fa168-183">解析 protobuf 檔匯入時要使用的其他目錄。</span><span class="sxs-lookup"><span data-stu-id="fa168-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="fa168-184">這是以分號分隔的路徑清單。</span><span class="sxs-lookup"><span data-stu-id="fa168-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="fa168-185">--存取</span><span class="sxs-lookup"><span data-stu-id="fa168-185">--access</span></span> | <span data-ttu-id="fa168-186">要用於產生的 c # 類別的存取修飾詞。</span><span class="sxs-lookup"><span data-stu-id="fa168-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="fa168-187">預設值為 `Public`。</span><span class="sxs-lookup"><span data-stu-id="fa168-187">Default value is `Public`.</span></span> <span data-ttu-id="fa168-188">接受的值為 `Internal` 和 `Public`。</span><span class="sxs-lookup"><span data-stu-id="fa168-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="fa168-189">移除</span><span class="sxs-lookup"><span data-stu-id="fa168-189">Remove</span></span>

<span data-ttu-id="fa168-190">此 `remove` 命令可用來移除 *.csproj* 檔案中的 Protobuf 參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="fa168-191">命令接受路徑引數和來源 Url 作為引數。</span><span class="sxs-lookup"><span data-stu-id="fa168-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="fa168-192">工具：</span><span class="sxs-lookup"><span data-stu-id="fa168-192">The tool:</span></span>

* <span data-ttu-id="fa168-193">只會移除 Protobuf 參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="fa168-194">不會刪除 *proto* 檔案，即使它原本是從遠端 URL 下載也一樣。</span><span class="sxs-lookup"><span data-stu-id="fa168-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="fa168-195">使用方式</span><span class="sxs-lookup"><span data-stu-id="fa168-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="fa168-196">引數</span><span class="sxs-lookup"><span data-stu-id="fa168-196">Arguments</span></span>

| <span data-ttu-id="fa168-197">引數</span><span class="sxs-lookup"><span data-stu-id="fa168-197">Argument</span></span> | <span data-ttu-id="fa168-198">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-198">Description</span></span> |
|-|-|
| <span data-ttu-id="fa168-199">參考</span><span class="sxs-lookup"><span data-stu-id="fa168-199">references</span></span> | <span data-ttu-id="fa168-200">要移除之 protobuf 參考的 Url 或檔案路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="fa168-201">選項</span><span class="sxs-lookup"><span data-stu-id="fa168-201">Options</span></span>

| <span data-ttu-id="fa168-202">Short 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-202">Short option</span></span> | <span data-ttu-id="fa168-203">Long 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-203">Long option</span></span> | <span data-ttu-id="fa168-204">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="fa168-205">-p</span><span class="sxs-lookup"><span data-stu-id="fa168-205">-p</span></span> | <span data-ttu-id="fa168-206">--project</span><span class="sxs-lookup"><span data-stu-id="fa168-206">--project</span></span> | <span data-ttu-id="fa168-207">要操作之專案檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-207">The path to the project file to operate on.</span></span> <span data-ttu-id="fa168-208">如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。</span><span class="sxs-lookup"><span data-stu-id="fa168-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="fa168-209">Refresh</span><span class="sxs-lookup"><span data-stu-id="fa168-209">Refresh</span></span>

<span data-ttu-id="fa168-210">此 `refresh` 命令可用來從來源 URL 的最新內容更新遠端參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="fa168-211">下載檔案路徑和來源 URL 都可以用來指定要更新的參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="fa168-212">附註：</span><span class="sxs-lookup"><span data-stu-id="fa168-212">Note:</span></span>

* <span data-ttu-id="fa168-213">檔案內容的雜湊會進行比較，以判斷是否應該更新本機檔案。</span><span class="sxs-lookup"><span data-stu-id="fa168-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="fa168-214">不會比較時間戳記資訊。</span><span class="sxs-lookup"><span data-stu-id="fa168-214">No timestamp information is compared.</span></span>

<span data-ttu-id="fa168-215">如果需要更新，此工具一律會將本機檔案取代為遠端檔案。</span><span class="sxs-lookup"><span data-stu-id="fa168-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="fa168-216">使用方式</span><span class="sxs-lookup"><span data-stu-id="fa168-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="fa168-217">引數</span><span class="sxs-lookup"><span data-stu-id="fa168-217">Arguments</span></span>

| <span data-ttu-id="fa168-218">引數</span><span class="sxs-lookup"><span data-stu-id="fa168-218">Argument</span></span> | <span data-ttu-id="fa168-219">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-219">Description</span></span> |
|-|-|
| <span data-ttu-id="fa168-220">參考</span><span class="sxs-lookup"><span data-stu-id="fa168-220">references</span></span> | <span data-ttu-id="fa168-221">應更新之遠端 protobuf 參考的 Url 或檔案路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="fa168-222">將此引數保留為空白，以重新整理所有遠端參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="fa168-223">選項</span><span class="sxs-lookup"><span data-stu-id="fa168-223">Options</span></span>

| <span data-ttu-id="fa168-224">Short 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-224">Short option</span></span> | <span data-ttu-id="fa168-225">Long 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-225">Long option</span></span> | <span data-ttu-id="fa168-226">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="fa168-227">-p</span><span class="sxs-lookup"><span data-stu-id="fa168-227">-p</span></span> | <span data-ttu-id="fa168-228">--project</span><span class="sxs-lookup"><span data-stu-id="fa168-228">--project</span></span> | <span data-ttu-id="fa168-229">要操作之專案檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-229">The path to the project file to operate on.</span></span> <span data-ttu-id="fa168-230">如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。</span><span class="sxs-lookup"><span data-stu-id="fa168-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="fa168-231">--試執行</span><span class="sxs-lookup"><span data-stu-id="fa168-231">--dry-run</span></span> | <span data-ttu-id="fa168-232">輸出會更新而不需要下載任何新內容的檔案清單。</span><span class="sxs-lookup"><span data-stu-id="fa168-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="fa168-233">清單</span><span class="sxs-lookup"><span data-stu-id="fa168-233">List</span></span>

<span data-ttu-id="fa168-234">此 `list` 命令會用來顯示專案檔中的所有 Protobuf 參考。</span><span class="sxs-lookup"><span data-stu-id="fa168-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="fa168-235">如果資料行的所有值都是預設值，則可能會省略該資料行。</span><span class="sxs-lookup"><span data-stu-id="fa168-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="fa168-236">使用方式</span><span class="sxs-lookup"><span data-stu-id="fa168-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="fa168-237">選項</span><span class="sxs-lookup"><span data-stu-id="fa168-237">Options</span></span>

| <span data-ttu-id="fa168-238">Short 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-238">Short option</span></span> | <span data-ttu-id="fa168-239">Long 選項</span><span class="sxs-lookup"><span data-stu-id="fa168-239">Long option</span></span> | <span data-ttu-id="fa168-240">描述</span><span class="sxs-lookup"><span data-stu-id="fa168-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="fa168-241">-p</span><span class="sxs-lookup"><span data-stu-id="fa168-241">-p</span></span> | <span data-ttu-id="fa168-242">--project</span><span class="sxs-lookup"><span data-stu-id="fa168-242">--project</span></span> | <span data-ttu-id="fa168-243">要操作之專案檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="fa168-243">The path to the project file to operate on.</span></span> <span data-ttu-id="fa168-244">如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。</span><span class="sxs-lookup"><span data-stu-id="fa168-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fa168-245">其他資源</span><span class="sxs-lookup"><span data-stu-id="fa168-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
