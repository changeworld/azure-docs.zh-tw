---
title: 快速入門：使用 JavaScript 建立管理群組
description: 在本快速入門中，您會使用 JavaScript 來建立管理群組，以將您的資源組織成資源階層。
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-javascript
ms.openlocfilehash: 7a234a6619eafd650451ae5d6bce37388c824f33
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604531"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>快速入門：使用 JavaScript 建立管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。 如需詳細資訊，請參閱[管理群組的初始設定](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 開始之前，請確定已安裝最新的 [Node.js](https://nodejs.org/) 版本 12。

- 如果未啟用[階層保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，則租用戶中的任何 Azure AD 使用者都可以建立管理群組，而不需將管理群組寫入權限指派給該使用者。 這個新管理群組會變成根管理群組或[預設管理群組](./how-to/protect-resource-hierarchy.md#setting---default-management-group)的子系，而建立者會獲得「擁有者」角色指派。 管理群組服務允許此功能，因此在根層級不需要角色指派。 沒有任何使用者可以存取建立的根管理群組。 為了避免尋找 Azure AD 全域管理員的困難以開始使用管理群組，我們允許在根層級建立初始管理群組。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>應用程式設定

若要啟用 JavaScript 來查詢 Azure Resource Graph，必須設定環境。 此設定適用於可以使用 JavaScript 的任何位置，包括 [Windows 10 上的 Bash](/windows/wsl/install-win10)。

1. 執行下列命令以設定新的 Node.js 專案。

   ```bash
   npm init -y
   ```

1. 新增 yargs 模組的參考。

   ```bash
   npm install yargs
   ```

1. 新增 Azure Resource Graph 模組的參考。

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. 新增 Azure 驗證程式庫的參考。

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > 確認 _package.json_ `@azure/arm-managementgroups` 的版本是 **1.1.0** 版或更高版本，以及確認 `@azure/ms-rest-nodeauth` 是 **3.0.5** 版或更高版本。

## <a name="create-the-management-group"></a>建立管理群組

1. 建立名為 _index.js_ 的新檔案，並且輸入下列程式碼。

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. 在終端機中輸入下列命令：

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   請務必將每個權杖 `<>` 預留位置分別取代為您的_管理群組識別碼_和_管理群組易記名稱_。

   當指令碼嘗試進行驗證時，終端機中會顯示類似下列訊息的訊息：

   > 若要登入，請使用網頁瀏覽器開啟頁面 https://microsoft.com/devicelogin ，並輸入代碼 FGB56WJUGK 以進行驗證。

   在瀏覽器中驗證之後，指令碼會繼續執行。

建立管理群組的結果會輸出至主控台。

## <a name="clean-up-resources"></a>清除資源

如果您想要從應用程式中移除已安裝的程式庫，請執行下列命令。

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立管理群組來組織您的資源階層。 管理群組可以保留訂用帳戶或其他管理群組。

若要深入了解管理群組，以及如何管理您的資源階層，請繼續：

> [!div class="nextstepaction"]
> [利用管理群組來管理您的資源](./manage.md)