---
title: 教學課程 - 在 Azure 中使用 Terratest 測試 Terraform 模組
description: 了解如何使用 Terratest 來測試 Terraform 模組。
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 687a793af2b9b75efe463b042d121c32f18974d6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370792"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>教學課程：在 Azure 中使用 Terratest 測試 Terraform 模組

> [!NOTE]
> 本文中的範例程式碼無法搭配 0.12 版 (和更新版本) 使用。

您可以使用 Azure Terraform 模組來建立可重複使用、可組合和可測試的元件。 Terraform 模組納入封裝，其在實作基礎結構作為程式碼程序時很實用。

當您建立 Terraform 模組時，請務必實作品質保證。 不幸的是，可以取得有限的文件，其中說明如何在 Terraform 模組中撰寫單元測試和整合測試。 本教學課程會介紹測試基礎結構，以及我們在建置 [Azure Terraform 模組](https://registry.terraform.io/browse?provider=azurerm)時所採用的最佳做法。

我們已了解所有最熱門的測試基礎結構，然後選擇了 [Terratest](https://github.com/gruntwork-io/terratest) 用來測試 Terraform 模組。 Terratest 可實作為 Go 程式庫。 Terratest 集合了常見基礎結構測試工作的協助程式函式和模式，例如，提出 HTTP 要求及使用 SSH 來存取特定虛擬機器。 下列清單說明使用 Terratest 的一些主要優點：

- **可提供便利的協助程式來檢查基礎結構**。 當您想要在實際環境中驗證實際基礎結構時，此功能非常有用。
- **資料夾結構的組織方式很清楚**。 測試案例的組織方式很清楚，並遵循[標準 Terraform 模組資料夾結構](https://www.terraform.io/docs/modules/create.html#standard-module-structure)。
- **所有測試案例均以 Go 撰寫**。 大部分使用 Terraform 的開發人員都是 Go 開發人員。 如果您是 Go 開發人員，則不需要學習另一種程式設計語言來使用 Terratest。 此外，您在 Terratest 中執行測試案例所需的相依性只有 Go 與 Terraform。
- **此基礎結構具有高度擴充性**。 您可以在 Terratest 的基礎上延伸額外功能，例如包含 Azure 特定的功能。

## <a name="prerequisites"></a>Prerequisites

這篇實際操作文章與平台無關。 您可以在 Windows、 Linux 或 MacOS 上，執行我們在本文中使用的程式碼範例。 

開始之前，請先安裝下列軟體：

- **Go 程式設計語言**：Terraform 測試案例是以 [Go](https://golang.org/dl/) 撰寫的。
- **dep**：[dep](https://github.com/golang/dep#installation) 是適用於 Go 的相依性管理工具。
- **Azure CLI**：[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 是一個可供您用來管理 Azure 資源的命令列工具。 (Terraform 支援透過服務主體或[透過 Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html) 來對 Azure 進行驗證。)
- **mage**：我們會使用 [mage 可執行檔](https://github.com/magefile/mage/releases)來說明如何簡化 Terratest 案例的執行。 

## <a name="create-a-static-webpage-module"></a>建立靜態網頁模組

在本教學課程中，您會建立一個 Terraform 模組，其藉由將單一 HTML 檔案上傳至 Azure 儲存體 Blob，以佈建靜態網頁。 此模組可讓全球各地的使用者透過模組傳回的 URL 存取網頁。

> [!NOTE]
> 在 [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) 位置之下，建立這一節描述的所有檔案。

首先，在 GoPath `src` 資料夾底下，建立名為 `staticwebpage` 的新資料夾。 本教學課程的整體資料夾結構如下列範例所示。 以星號 `(*)` 標示的檔案是本節的主要重點。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

靜態網頁模組接受三項輸入。 這些輸入會在 `./variables.tf` 中宣告：

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

如本文稍早所述，此模組也會輸出在 `./outputs.tf` 中宣告的 URL：

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

模組的主要邏輯會佈建四個資源：
- **資源群組**：資源群組的名稱是 `-staging-rg` 所附加的 `website_name` 輸入。
- **儲存體帳戶**：儲存體帳戶的名稱是 `data001` 所附加的 `website_name` 輸入。 為了遵守儲存體帳戶的名稱限制，此模組會移除所有特殊字元，並且在整個儲存體帳戶名稱中使用小寫字母。
- **已修正的名稱容器**：容器會命名為 `wwwroot`，並且建立在儲存體帳戶中。
- **單一 HTML 檔案**：HTML 檔案的讀取來源為 `html_path` 輸入，並且會上傳至 `wwwroot/index.html`。

靜態網頁模組邏輯會實作在 `./main.tf` 中：

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>單元測試

Terratest 主要用於整合測試。 基於這個目的，Terratest 會在真實環境中佈建真實資源。 有時候，整合測試作業會變得特別巨大，當您要佈建大量資源時更會如此。 轉換上一節中所參考儲存體帳戶名稱的邏輯是個好例子。 

但是，我們真的不需要佈建任何資源； 只想要確定命名轉換邏輯正確。 多虧了 Terratest 的彈性，我們才可使用單元測試。 單元測試是在本機執行的測試案例 (雖然需要存取網際網路)。 單元測試案例會執行 `terraform init` 和 `terraform plan` 命令，以剖析 `terraform plan` 的輸出並尋找要比較的屬性值。

本節其餘部分說明如何使用 Terratest 來實作單元測試，以確定用來轉換儲存體帳戶命名的邏輯是正確的。 我們只對以星號 `(*)` 標示的檔案感興趣。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

首先，我們會使用名為 `./test/fixtures/storage-account-name/empty.html` 的空白 HTML 檔案作為預留位置。

`./test/fixtures/storage-account-name/main.tf` 檔案是測試案例架構。 它會接受一個輸入 `website_name`，這也是單元測試的輸入。 邏輯如下所示：

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

主要元件是在 `./test/storage_account_name_unit_test.go` 中的單元測試實作。

Go 開發人員可能會發現單元測試藉由接受 `*testing.T` 類型的引數來符合傳統 Go 測試函式的簽章。

在單元測試的本文中，我們在 `testCases` 變數中總共定義了五個案例 (`key` 作為輸入，而 `value` 作為預期的輸出)。 針對每個單元測試案例，我們會先執行 `terraform init`，其目標為測試固件資料夾 (`./test/fixtures/storage-account-name/`)。 

接下來，使用特定測試案例輸入的 `terraform plan` 命令 (請看 `tfOptions` 中的 `website_name` 定義) 會將結果儲存至 `./test/fixtures/storage-account-name/terraform.tfplan` (未在整體資料夾結構中列出)。

系統會使用官方 Terraform 方案剖析器，將這個結果檔案剖析為可由程式碼讀取的結構。

現在，我們可以尋找感興趣的屬性 (在此案例中為 `azurerm_storage_account` 的 `name`)，並且比較結果與預期的輸出：

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

若要執行單元測試，請在命令列上完成下列步驟：

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

傳統的 Go 測試結果大約會在一分鐘內傳回。

### <a name="integration-test"></a>整合測試

從端對端的觀點來看，與單元測試相比，整合測試必須在真實環境中佈建資源。 Terratest 很擅長執行這類工作。 

Terraform 模組的最佳做法包含安裝 `examples` 資料夾。 `examples` 資料夾包含一些端對端範例。 若要避免使用真實資料，何不測試這些範例作為整合測試？ 在這一節中，我們著重於下列資料夾結構中三個以星號 `(*)` 標示的檔案：

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

讓我們從這些範例著手。 在 `./examples/` 資料夾中建立了名為 `hello-world/` 的新範例資料夾。 我們在此提供了簡單的 HTML 網頁 `./examples/hello-world/index.html` 供您上傳。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Terraform 範例 `./examples/hello-world/main.tf` 類似於單元測試中所示的範例。 有一個顯著的差異：此範例也會將已上傳 HTML 的 URL 輸出為名為 `homepage` 的網頁。

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

我們會在整合測試檔案 `./test/hello_world_example_test.go` 中再次使用 Terratest 和傳統 Go 測試函式。

不同於單元測試，整合測試會在 Azure 中建立實際資源。 這就是您為何需要小心避免命名衝突。 (請特別注意某些全域唯一的名稱，例如儲存體帳戶名稱。)因此，測試邏輯的第一個步驟就是使用 Terratest 所提供的 `UniqueId()` 函式，來產生隨機的 `websiteName`。 此函式會產生隨機名稱，其中包含小寫字母、大寫字母或數字。 `tfOptions` 讓所有的 Terraform 命令以 `./examples/hello-world/` 資料夾為目標。 這也可確保 `website_name` 設為隨機的 `websiteName`。

然後，系統會逐一執行 `terraform init`、`terraform apply` 和 `terraform output`。 我們會使用 Terratest 所提供的另一個協助程式函式 `HttpGetWithCustomValidation()`。 我們會使用協助程式函式，確定 HTML 已上傳至 `terraform output` 所傳回的輸出 `homepage` URL。 我們會比較 HTTP GET 狀態碼與 `200`，並且在 HTML 內容中尋找一些關鍵字。 最後，系統會利用 Go 的 `defer` 功能來「保證」`terraform destroy` 的執行。

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

若要執行整合測試，請在命令列上完成下列步驟：

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

傳統的 Go 測試結果大約會在兩分鐘內傳回。 您也可以藉由執行下列命令，同時執行單元測試與整合測試：

```shell
go fmt
go test
```

整合測試所需的進行時間比單元測試還久 (相較於五個單元案例需要一分鐘，一個整合案例就需要兩分鐘)。 但是您可以決定在案例中使用單元測試或整合測試。 一般而言，如果是使用 Terraform HCL 函式的複雜邏輯，我們喜歡使用單元測試。 對於使用者的端對端觀點，我們通常會使用整合測試。

## <a name="use-mage-to-simplify-running-terratest-cases"></a>使用 mage 來簡化 Terratest 案例的執行 
在 Azure Cloud Shell 中執行測試案例，需要在不同的目錄中執行多種命令。 為了讓這個程序更有效率，我們會在專案中引進建置系統。 在本節中，我們會使用 Go 建置系統 mage 來執行這項作業。

mage 唯一的要求是專案根目錄中有 `magefile.go` (在下列範例中以 `(+)` 標出)：

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

以下是 `./magefile.go` 的 範例。 在這個以 Go 撰寫的建置指令碼中，我們實作了五個建置步驟：
- `Clean`:此步驟會移除在測試執行期間產生的所有產生及暫存檔案。
- `Format`:此步驟會執行 `terraform fmt` 和 `go fmt` 來設定程式碼基底的格式。
- `Unit`:此步驟會在 `./test/` 資料夾底下執行所有單元測試 (使用函式名稱慣例 `TestUT_*`)。
- `Integration`:此步驟類似於 `Unit`，但不會執行單元測試，而是會執行整合測試 (`TestIT_*`)。
- `Full`:此步驟會依序執行 `Clean`、`Format`、`Unit` 和 `Integration`。

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

您可以使用下列命令來執行完整的測試套件。 此程式碼類似於我們在先前章節中使用的執行步驟。 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

您可以使用其他 mage 步驟來取代最後一個命令列。 例如，您可以使用 `mage unit` 或 `mage clean`。 在 magefile 中內嵌 `dep` 命令和 `az login` 是個不錯的主意。 我們不會在此顯示程式碼。 

透過 mage，您也可以使用 Go 套件系統來共用步驟。 在那種情況下，只要藉由參考通用實作並宣告相依性 (`mg.Deps()`)，便能簡化所有模組的 magefiles。

**選擇性：設定服務主體環境變數來執行接受度測試**
 
您不必在測試之前執行 `az login`，反而可以藉由設定服務主體的環境變數來完成 Azure 驗證。 Terraform 會發佈[環境變數名稱的清單](https://www.terraform.io/docs/providers/azurerm/index.html#testing)。 (在這些環境變數中，只有前四個是必要變數)。Terraform 也會發佈詳細指示，說明如何[取得這些環境變數的值](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [Terratest GitHub 頁面](https://github.com/gruntwork-io/terratest)。