---
title: 在 ASP.NET Core 中使用 Grunt
author: rick-anderson
description: 在 ASP.NET Core 中使用 Grunt
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054550"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="b9c67-103">在 ASP.NET Core 中使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="b9c67-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="b9c67-104">Grunt 是一種 JavaScript 工作執行器，可將腳本縮制、TypeScript 編譯、程式碼品質「不起毛」工具、CSS 前置處理器，以及任何需要執行以支援用戶端開發的重複性工作自動化。</span><span class="sxs-lookup"><span data-stu-id="b9c67-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="b9c67-105">Visual Studio 完全支援 Grunt。</span><span class="sxs-lookup"><span data-stu-id="b9c67-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="b9c67-106">此範例會使用空的 ASP.NET Core 專案作為其起點，以示範如何從頭開始自動執行用戶端組建程式。</span><span class="sxs-lookup"><span data-stu-id="b9c67-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="b9c67-107">完成的範例會清除目標部署目錄、結合 JavaScript 檔案、檢查程式碼品質、總是 JavaScript 檔案內容，並將其部署至 web 應用程式的根目錄。</span><span class="sxs-lookup"><span data-stu-id="b9c67-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="b9c67-108">我們將使用下列套件：</span><span class="sxs-lookup"><span data-stu-id="b9c67-108">We will use the following packages:</span></span>

* <span data-ttu-id="b9c67-109">**grunt**： grunt 工作執行器套件。</span><span class="sxs-lookup"><span data-stu-id="b9c67-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="b9c67-110">**grunt-contrib-clean**：移除檔案或目錄的外掛程式。</span><span class="sxs-lookup"><span data-stu-id="b9c67-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="b9c67-111">**grunt-contrib-jshint**：可檢查 JavaScript 程式碼品質的外掛程式。</span><span class="sxs-lookup"><span data-stu-id="b9c67-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="b9c67-112">**grunt-contrib-concat**：將檔案加入單一檔案的外掛程式。</span><span class="sxs-lookup"><span data-stu-id="b9c67-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="b9c67-113">**grunt-contrib-uglify**：縮短 JavaScript 以縮減大小的外掛程式。</span><span class="sxs-lookup"><span data-stu-id="b9c67-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="b9c67-114">**grunt-contrib-watch：監看** 檔案活動的外掛程式。</span><span class="sxs-lookup"><span data-stu-id="b9c67-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="b9c67-115">準備應用程式</span><span class="sxs-lookup"><span data-stu-id="b9c67-115">Preparing the application</span></span>

<span data-ttu-id="b9c67-116">若要開始，請設定新的空白 web 應用程式，並新增 TypeScript 範例檔案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="b9c67-117">TypeScript 檔案會使用預設 Visual Studio 設定自動編譯成 JavaScript，而且將會是使用 Grunt 處理的原始資料。</span><span class="sxs-lookup"><span data-stu-id="b9c67-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="b9c67-118">在 Visual Studio 中，建立新的 `ASP.NET Web Application` 。</span><span class="sxs-lookup"><span data-stu-id="b9c67-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="b9c67-119">在 [ **新增 ASP.NET 專案** ] 對話方塊中，選取 ASP.NET Core **空白** 範本，然後按一下 [確定] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b9c67-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="b9c67-120">在 [方案總管中，檢查項目結構。</span><span class="sxs-lookup"><span data-stu-id="b9c67-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="b9c67-121">此 `\src` 資料夾包含空白 `wwwroot` 和 `Dependencies` 節點。</span><span class="sxs-lookup"><span data-stu-id="b9c67-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![空白的 web 方案](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="b9c67-123">將名為的新資料夾新增 `TypeScript` 至您的專案目錄。</span><span class="sxs-lookup"><span data-stu-id="b9c67-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="b9c67-124">新增任何檔案之前，請 Visual Studio 確定已核取 [在儲存時編譯] 選項。</span><span class="sxs-lookup"><span data-stu-id="b9c67-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="b9c67-125">流覽至 **工具**  >  **選項**  >  **文字編輯器**  >  **Typescript**  >  **專案**：</span><span class="sxs-lookup"><span data-stu-id="b9c67-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![設定自動編譯 TypeScript 檔案的選項](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="b9c67-127">以滑鼠右鍵按一下 `TypeScript` 目錄，然後從操作功能表中選取 [ **加入 > 新專案** ]。</span><span class="sxs-lookup"><span data-stu-id="b9c67-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="b9c67-128">選取 **JavaScript** 檔案專案並將檔案命名為 *Tastes* ， (記下 \*) 的 ts 延伸模組。</span><span class="sxs-lookup"><span data-stu-id="b9c67-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="b9c67-129">將下方的 TypeScript 程式程式碼複製到檔案 (當您儲存時，JavaScript 來源) 會出現新的 *Tastes.js* 檔案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="b9c67-130">將第二個檔案新增至 **TypeScript** 目錄，並為其命名 `Food.ts` 。</span><span class="sxs-lookup"><span data-stu-id="b9c67-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="b9c67-131">將下列程式碼複製到檔案中。</span><span class="sxs-lookup"><span data-stu-id="b9c67-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="b9c67-132">設定 NPM</span><span class="sxs-lookup"><span data-stu-id="b9c67-132">Configuring NPM</span></span>

<span data-ttu-id="b9c67-133">接下來，設定 NPM 以下載 grunt 和 grunt 工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="b9c67-134">在方案總管中，以滑鼠右鍵按一下專案，然後從內容功能表中選取 [ **加入 > 新專案** ]。</span><span class="sxs-lookup"><span data-stu-id="b9c67-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="b9c67-135">選取 [ **NPM] 設定檔** 專案，保留預設名稱， *package.js*]，然後按一下 [ **新增** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b9c67-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="b9c67-136">在 *package.json* 檔案的 `devDependencies` 物件括弧內，輸入 "grunt"。</span><span class="sxs-lookup"><span data-stu-id="b9c67-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="b9c67-137">`grunt`從 Intellisense 清單中選取，然後按下 enter 鍵。</span><span class="sxs-lookup"><span data-stu-id="b9c67-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="b9c67-138">Visual Studio 會將 grunt 套件名稱加上引號，並新增冒號。</span><span class="sxs-lookup"><span data-stu-id="b9c67-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="b9c67-139">在冒號右邊，從 Intellisense 清單頂端選取封裝的最新穩定版本， (按下 [ `Ctrl-Space` intellisense 未) 出現]。</span><span class="sxs-lookup"><span data-stu-id="b9c67-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="b9c67-141">NPM 使用 [語義版本](https://semver.org/) 設定來組織相依性。</span><span class="sxs-lookup"><span data-stu-id="b9c67-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="b9c67-142">語義版本設定（也稱為 SemVer）會識別具有 \<major> 編號配置的封裝 \<minor> ... \<patch>Intellisense 只會顯示一些常見的選項，以簡化語義版本控制。</span><span class="sxs-lookup"><span data-stu-id="b9c67-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="b9c67-143">Intellisense 清單中的最上層專案 (上述範例中的0.4.5，) 被視為套件的最新穩定版本。</span><span class="sxs-lookup"><span data-stu-id="b9c67-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="b9c67-144">插入號 (^) 符號符合最新的主要版本，而波狀符號 (~) 符合最新的次要版本。</span><span class="sxs-lookup"><span data-stu-id="b9c67-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="b9c67-145">如需 SemVer 所提供完整講求表現度的指南，請參閱 [NPM semver 版本](https://www.npmjs.com/package/semver) 剖析器參考。</span><span class="sxs-lookup"><span data-stu-id="b9c67-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="b9c67-146">新增更多相依性以載入 grunt-contrib- \* 適用于 *clean*、 *jshint*、 *concat*、 *uglify* 和 *watch* 的套件，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="b9c67-146">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="b9c67-147">版本不需要符合範例。</span><span class="sxs-lookup"><span data-stu-id="b9c67-147">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="b9c67-148">儲存 package.json 檔案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-148">Save the *package.json* file.</span></span>

<span data-ttu-id="b9c67-149">每個專案的封裝 `devDependencies` 將會下載，以及每個封裝所需的任何檔案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="b9c67-150">您可以啟用 **方案總管** 中的 [**顯示所有** 檔案] 按鈕，在 *node_modules* 目錄中找到封裝檔案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="b9c67-152">如有需要，您可以在 **方案總管** 中，以滑鼠右鍵按一下 `Dependencies\NPM` 並選取 [ **還原套件** ] 功能表選項，以手動還原相依性。</span><span class="sxs-lookup"><span data-stu-id="b9c67-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![還原套件](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="b9c67-154">設定 Grunt</span><span class="sxs-lookup"><span data-stu-id="b9c67-154">Configuring Grunt</span></span>

<span data-ttu-id="b9c67-155">Grunt 會使用名為 *Gruntfile.js* 的資訊清單進行設定，此指令程式會定義、載入和註冊可以手動執行或設定為根據 Visual Studio 中事件自動執行的工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="b9c67-156">以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新專案**]。</span><span class="sxs-lookup"><span data-stu-id="b9c67-156">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="b9c67-157">選取 **JavaScript** 檔案專案範本，將名稱變更為 *Gruntfile.js*，然後按一下 [ **加入** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="b9c67-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="b9c67-158">將下列程式碼新增至 *Gruntfile.js*。</span><span class="sxs-lookup"><span data-stu-id="b9c67-158">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="b9c67-159">此函式會 `initConfig` 設定每個封裝的選項，而模組的其餘部分則會載入和註冊工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="b9c67-160">在函式中 `initConfig` ，新增工作的選項， `clean` 如下列範例 *Gruntfile.js* 所示。</span><span class="sxs-lookup"><span data-stu-id="b9c67-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="b9c67-161">此工作會 `clean` 接受目錄字串的陣列。</span><span class="sxs-lookup"><span data-stu-id="b9c67-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="b9c67-162">此工作會從 *wwwroot/lib* 移除檔案，並移除整個 */temp* 目錄。</span><span class="sxs-lookup"><span data-stu-id="b9c67-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="b9c67-163">在函式下方 `initConfig` ，加入的呼叫 `grunt.loadNpmTasks` 。</span><span class="sxs-lookup"><span data-stu-id="b9c67-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="b9c67-164">這會讓工作從 Visual Studio 中執行。</span><span class="sxs-lookup"><span data-stu-id="b9c67-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="b9c67-165">儲存 *Gruntfile.js*。</span><span class="sxs-lookup"><span data-stu-id="b9c67-165">Save *Gruntfile.js*.</span></span> <span data-ttu-id="b9c67-166">檔案看起來應該像下面的螢幕擷取畫面。</span><span class="sxs-lookup"><span data-stu-id="b9c67-166">The file should look something like the screenshot below.</span></span>

    ![初始 gruntfile.js](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="b9c67-168">以滑鼠右鍵按一下 *Gruntfile.js* ，然後從內容功能表中選取 [工作執行 **器瀏覽器** ]。</span><span class="sxs-lookup"><span data-stu-id="b9c67-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="b9c67-169">[工作執行 **器瀏覽器** ] 視窗隨即開啟。</span><span class="sxs-lookup"><span data-stu-id="b9c67-169">The **Task Runner Explorer** window will open.</span></span>

    ![工作執行器功能表](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="b9c67-171">確認 `clean` 在 [工作執行 **器瀏覽器**] 中的 [工作] 底下顯示。</span><span class="sxs-lookup"><span data-stu-id="b9c67-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![工作執行器瀏覽器工作清單](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="b9c67-173">以滑鼠右鍵按一下清除工作，然後從內容功能表中選取 [ **執行** ]。</span><span class="sxs-lookup"><span data-stu-id="b9c67-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="b9c67-174">命令視窗會顯示工作的進度。</span><span class="sxs-lookup"><span data-stu-id="b9c67-174">A command window displays progress of the task.</span></span>

    ![工作執行器瀏覽器執行清除工作](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="b9c67-176">尚未清除任何檔案或目錄。</span><span class="sxs-lookup"><span data-stu-id="b9c67-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="b9c67-177">如果您想要的話，可以在方案總管中手動建立它們，然後以測試的形式執行清除工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="b9c67-178">在函 `initConfig` 式中，加入 `concat` 使用下列程式碼的專案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="b9c67-179">`src`屬性陣列會依應結合的順序，列出要合併的檔案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="b9c67-180">`dest`屬性會將路徑指派給所產生的合併檔案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="b9c67-181">`all`上述程式碼中的屬性是目標的名稱。</span><span class="sxs-lookup"><span data-stu-id="b9c67-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="b9c67-182">目標用於某些 Grunt 工作，以允許多個組建環境。</span><span class="sxs-lookup"><span data-stu-id="b9c67-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="b9c67-183">您可以使用 IntelliSense 來查看內建目標，或指派自己的目標。</span><span class="sxs-lookup"><span data-stu-id="b9c67-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="b9c67-184">`jshint`使用下列程式碼來新增工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="b9c67-185">Jshint `code-quality` 公用程式會針對在 *temp* 目錄中找到的每個 JavaScript 檔案執行。</span><span class="sxs-lookup"><span data-stu-id="b9c67-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="b9c67-186">當 JavaScript 使用括弧語法來指派屬性，而不是以點標記法（例如，而不是）時，W069 是由 jshint 所產生的錯誤。 `Tastes["Sweet"]` `Tastes.Sweet`</span><span class="sxs-lookup"><span data-stu-id="b9c67-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="b9c67-187">選項會關閉警告，以允許其餘的進程繼續執行。</span><span class="sxs-lookup"><span data-stu-id="b9c67-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="b9c67-188">`uglify`使用下列程式碼來新增工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="b9c67-189">此工作會縮短在 temp 目錄中找到的 *combined.js* 檔案，並在 *\<file name\>.min.js* 標準命名慣例之後，在 wwwroot/lib 中建立結果檔。</span><span class="sxs-lookup"><span data-stu-id="b9c67-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="b9c67-190">在對該載入的呼叫下 `grunt.loadNpmTasks` `grunt-contrib-clean` ，使用下列程式碼，包含對 jshint、concat 和 uglify 的相同呼叫。</span><span class="sxs-lookup"><span data-stu-id="b9c67-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="b9c67-191">儲存 *Gruntfile.js*。</span><span class="sxs-lookup"><span data-stu-id="b9c67-191">Save *Gruntfile.js*.</span></span> <span data-ttu-id="b9c67-192">檔案看起來應該會像下面的範例。</span><span class="sxs-lookup"><span data-stu-id="b9c67-192">The file should look something like the example below.</span></span>

    ![完整的 grunt 檔案範例](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="b9c67-194">請注意，[工作執行 **器** ] 的 [工作] 清單包括 `clean` 、 `concat` 和工作 `jshint` `uglify` 。</span><span class="sxs-lookup"><span data-stu-id="b9c67-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="b9c67-195">依序執行每項工作，並觀察 **方案總管** 的結果。</span><span class="sxs-lookup"><span data-stu-id="b9c67-195">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="b9c67-196">每項工作都應該執行而不會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="b9c67-196">Each task should run without errors.</span></span>

    ![工作執行器瀏覽器執行每項工作](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="b9c67-198">Concat 工作會建立新的 *combined.js* 檔，並將它放入 temp 目錄中。</span><span class="sxs-lookup"><span data-stu-id="b9c67-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="b9c67-199">此工作 `jshint` 只會執行，而不會產生輸出。</span><span class="sxs-lookup"><span data-stu-id="b9c67-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="b9c67-200">此工作 `uglify` 會建立新的 *combined.min.js* 檔，並將它放入 *wwwroot/lib* 中。</span><span class="sxs-lookup"><span data-stu-id="b9c67-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="b9c67-201">完成時，解決方案看起來應該像下面的螢幕擷取畫面：</span><span class="sxs-lookup"><span data-stu-id="b9c67-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![所有工作之後的方案 explorer](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="b9c67-203">如需每個封裝之選項的詳細資訊，請 [https://www.npmjs.com/](https://www.npmjs.com/) 在主頁面上的 [搜尋] 方塊中，流覽並查閱封裝名稱。</span><span class="sxs-lookup"><span data-stu-id="b9c67-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="b9c67-204">例如，您可以查詢 grunt-contrib-clean 封裝，以取得說明其所有參數的檔連結。</span><span class="sxs-lookup"><span data-stu-id="b9c67-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="b9c67-205">摘要</span><span class="sxs-lookup"><span data-stu-id="b9c67-205">All together now</span></span>

<span data-ttu-id="b9c67-206">使用 Grunt `registerTask()` 方法，以特定循序執行一系列的工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="b9c67-207">例如，若要執行上述 order clean > concat-> jshint-> uglify 中的範例步驟，請將下列程式碼新增至模組。</span><span class="sxs-lookup"><span data-stu-id="b9c67-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="b9c67-208">程式碼應該加入至與 loadNpmTasks ( # A1 呼叫相同的層級，而不是 initConfig 外部。</span><span class="sxs-lookup"><span data-stu-id="b9c67-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="b9c67-209">新的工作會顯示在 [工作執行器] 的 [別名工作] 下。</span><span class="sxs-lookup"><span data-stu-id="b9c67-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="b9c67-210">您可以用滑鼠右鍵按一下並執行它，就像是其他工作一樣。</span><span class="sxs-lookup"><span data-stu-id="b9c67-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="b9c67-211">此工作 `all` 將依 `clean` 序執行、 `concat` `jshint` 和 `uglify` 。</span><span class="sxs-lookup"><span data-stu-id="b9c67-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![別名 grunt 工作](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="b9c67-213">監看變更</span><span class="sxs-lookup"><span data-stu-id="b9c67-213">Watching for changes</span></span>

<span data-ttu-id="b9c67-214">工作會 `watch` 持續留意檔案和目錄。</span><span class="sxs-lookup"><span data-stu-id="b9c67-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="b9c67-215">如果偵測到變更，監看會自動觸發工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="b9c67-216">將下列程式碼新增至 initConfig，以監看 \* TypeScript 目錄中的 .js 檔案是否有變更。</span><span class="sxs-lookup"><span data-stu-id="b9c67-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="b9c67-217">如果 JavaScript 檔案已變更， `watch` 將會執行工作 `all` 。</span><span class="sxs-lookup"><span data-stu-id="b9c67-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="b9c67-218">加入的呼叫 `loadNpmTasks()` ，以顯示工作執行 `watch` 器瀏覽器中的工作。</span><span class="sxs-lookup"><span data-stu-id="b9c67-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="b9c67-219">以滑鼠右鍵按一下 [工作執行器瀏覽器] 中的監看式工作，然後從操作功能表選取 [執行]。</span><span class="sxs-lookup"><span data-stu-id="b9c67-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="b9c67-220">顯示正在執行之監看式工作的命令視窗將會顯示「正在等候 ...」消息。</span><span class="sxs-lookup"><span data-stu-id="b9c67-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="b9c67-221">開啟其中一個 TypeScript 檔案，新增空格，然後儲存檔案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="b9c67-222">這會觸發 watch 工作，並觸發其他工作以依序執行。</span><span class="sxs-lookup"><span data-stu-id="b9c67-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="b9c67-223">下列螢幕擷取畫面顯示範例執行。</span><span class="sxs-lookup"><span data-stu-id="b9c67-223">The screenshot below shows a sample run.</span></span>

![正在執行工作輸出](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="b9c67-225">系結至 Visual Studio 事件</span><span class="sxs-lookup"><span data-stu-id="b9c67-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="b9c67-226">除非您想要在每次於 Visual Studio 中工作時手動啟動工作，否則請 **在組建、\*\*\*\*清除** 和 **專案開啟** 事件 **之後**，將工作系結至。</span><span class="sxs-lookup"><span data-stu-id="b9c67-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="b9c67-227">系結， `watch` 以便在每次 Visual Studio 開啟時執行。</span><span class="sxs-lookup"><span data-stu-id="b9c67-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="b9c67-228">在工作執行器瀏覽器中，以滑鼠右鍵按一下 [監看式] 工作，然後從內容功能表 **選取 [** 系  >  **結專案開啟**]。</span><span class="sxs-lookup"><span data-stu-id="b9c67-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![將工作系結至開啟的專案](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="b9c67-230">卸載並重載專案。</span><span class="sxs-lookup"><span data-stu-id="b9c67-230">Unload and reload the project.</span></span> <span data-ttu-id="b9c67-231">當專案再次載入時，[監看式] 工作會自動開始執行。</span><span class="sxs-lookup"><span data-stu-id="b9c67-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="b9c67-232">摘要</span><span class="sxs-lookup"><span data-stu-id="b9c67-232">Summary</span></span>

<span data-ttu-id="b9c67-233">Grunt 是功能強大的工作執行器，可用來將大部分的用戶端組建工作自動化。</span><span class="sxs-lookup"><span data-stu-id="b9c67-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="b9c67-234">Grunt 利用 NPM 來提供其套件，並將功能工具與 Visual Studio 整合。</span><span class="sxs-lookup"><span data-stu-id="b9c67-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="b9c67-235">Visual Studio 的工作執行器 Explorer 會偵測設定檔的變更，並提供便利的介面來執行工作、查看執行中的工作，以及將工作系結至 Visual Studio 事件。</span><span class="sxs-lookup"><span data-stu-id="b9c67-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
