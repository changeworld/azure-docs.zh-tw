---
ms.openlocfilehash: d3a65122772a7cb89de6d3f7f27c2f49d3f74c98
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633313"
---
* 具有有效訂用帳戶的 Azure 帳戶。 如果您還未擁有帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
  > [!NOTE]
  > 您將需要 Azure 訂用帳戶，具有建立服務主體的許可權 (**擁有者角色** 提供此) 。 如果您沒有正確的許可權，請洽詢您的帳戶管理員，授與您正確的許可權。 
* 具有下列擴充功能的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
        > [!TIP]
        > 安裝 Azure IoT Tools 時，系統可能會提示您安裝 Docker。 請放心地忽略提示。
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* 如果您尚未完成[偵測動作並發出事件](../../../detect-motion-emit-events-quickstart.md)快速入門，請遵循下列步驟：
     1. [設定 Azure 資源](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [設定您的開發環境](../../../detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [產生及部署 IoT Edge 部署資訊清單](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [準備監視事件](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> 如果您遇到已建立的 Azure 資源問題，請檢視我們的 **[疑難排解指南](../../../troubleshoot-how-to.md#common-error-resolutions)** ，以解決一些經常發生的問題。