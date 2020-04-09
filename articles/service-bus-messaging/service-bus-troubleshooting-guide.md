---
title: Azure 服務總線的故障排除指南 |微軟文件
description: 本文提供了 Azure 服務總線消息傳遞異常的清單,以及發生異常時要執行的操作。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887771"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure 服務總線的故障排除指南
本文針對使用 Azure 服務總線時可能看到的一些問題提供了故障排除提示和建議。 

## <a name="connectivity-certificate-or-timeout-issues"></a>連線、憑證或逾時問題
以下步驟可以幫助您解決 *.servicebus.windows.net 下所有服務的連接/證書/超時問題。 

- 瀏覽或[wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`。 它有助於檢查您是否存在 IP 篩選或虛擬網路或證書鏈問題(在使用 java SDK 時最為常見)。

    成功訊息的範例:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    失敗錯誤訊息的範例:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 運行以下命令以檢查防火牆上是否有任何埠被阻止。 使用的埠為 443 (HR)、5671 (AMQP) 和 9354(網路消息/SBMP)。 根據您使用的庫,也會使用其他埠。 下面是檢查 5671 埠是否被阻止的示例命令。 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    在 Linux 上：

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 當出現間歇性連接問題時,運行以下命令以檢查是否有任何丟棄的數據包。 此命令將嘗試每 1 秒與服務建立 25 個不同的 TCP 連接。 然後,您可以檢查它們中有多少成功/失敗,還可以查看 TCP 連接延遲。 你可以`psping`[從這裡](/sysinternals/downloads/psping)下載該工具。

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    如果使用其他工具(如`tnc`、`ping`等),則可以使用等效命令。 
- 如果前面的步驟不起作用,請獲取網路追蹤,並使用[Wireshark](https://www.wireshark.org/)等工具對其進行分析。 如果需要[,請與微軟支援部門](https://support.microsoft.com/)聯繫。 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>服務升級/重新啟動時可能出現的問題
後端服務升級和重新啟動可能會對應用程式造成以下影響:

- 請求可能會暫時受到限制。
- 傳入消息/請求可能會下降。
- 日誌檔可能包含錯誤消息。
- 應用程式可能會斷開與服務幾秒鐘的時間。

如果應用程式代碼使用 SDK,則重試策略已內建並處於活動狀態。 應用程式將重新連接,而不會對應用程式/工作流造成重大影響。

## <a name="unauthorized-access-send-claims-are-required"></a>未經授權的存取:需要傳送聲明
當嘗試使用具有發送許可權的使用者分配的託管標識從本地電腦上的 Visual Studio 訪問服務總線主題時,可能會看到此錯誤。

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

要解決此錯誤,請安裝[Microsoft.Azure.服務.App身份驗證](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)庫。  有關詳細資訊,請參閱[本機開發身份驗證](..\key-vault\service-to-service-authentication.md#local-development-authentication)。 

要瞭解如何為角色分配權限,請參閱使用[Azure 活動目錄對託管識別進行身份驗證以存取 Azure 服務總線資源](service-bus-managed-service-identity.md)。

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [Azure 資源管理器異常](service-bus-resource-manager-exceptions.md)。 它列出使用 Azure 資源管理員(透過樣本或直接呼叫)與 Azure 服務總線互動時生成的異常。
- [訊息例外](service-bus-messaging-exceptions.md)。 它提供了由 .NET 框架為 Azure 服務總線生成的異常清單。 

