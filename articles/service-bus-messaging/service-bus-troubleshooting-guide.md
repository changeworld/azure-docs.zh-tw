---
title: Azure 服務匯流排的疑難排解指南 |Microsoft Docs
description: 本文提供在發生例外狀況時所要採取的 Azure 服務匯流排訊息例外狀況和建議的動作清單。
ms.topic: article
ms.date: 07/15/2020
ms.openlocfilehash: 822a97a230a8646ddadde21eedc6c23d5e3efbd6
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067047"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure 服務匯流排的疑難排解指南
本文提供使用 Azure 服務匯流排時，您可能會看到的幾個問題的疑難排解秘訣和建議。 

## <a name="connectivity-certificate-or-timeout-issues"></a>連線、憑證或超時問題
下列步驟可協助您針對 *. servicebus.windows.net 下所有服務的連線/憑證/超時問題進行疑難排解。 

- 流覽至或[wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` 。 它有助於檢查您是否有 IP 篩選或虛擬網路或憑證鏈問題，這在使用 java SDK 時很常見。

    成功訊息的範例：
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    失敗錯誤訊息的範例：

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 執行下列命令，以檢查防火牆上是否有任何埠遭到封鎖。 使用的埠為 443 (HTTPS) 、5671 (AMQP) 和 9354 (Net 訊息/SBMP) 。 視您使用的程式庫而定，也會使用其他埠。 以下是檢查5671埠是否已封鎖的範例命令。 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    在 Linux 上：

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 當發生間歇連線問題時，請執行下列命令來檢查是否有任何已丟棄的封包。 此命令會嘗試使用服務每隔1秒建立25個不同的 TCP 連線。 然後，您可以檢查其中有多少個成功/失敗，同時查看 TCP 連接延遲。 您可以 `psping` 從[這裡](/sysinternals/downloads/psping)下載此工具。

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    如果您使用其他工具（例如、等等），可以使用對等的命令 `tnc` `ping` 。 
- 如果先前的步驟沒有使用[Wireshark](https://www.wireshark.org/)之類的工具來協助並加以分析，請取得網路追蹤。 如有需要，請聯絡[Microsoft 支援服務](https://support.microsoft.com/)。 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>服務升級/重新開機時可能發生的問題

### <a name="symptoms"></a>徵狀
- 要求可能會短暫地進行節流。
- 傳入訊息/要求中可能會有一個下降。
- 記錄檔可能包含錯誤訊息。
- 應用程式可能會在幾秒內中斷與服務的連線。

### <a name="cause"></a>原因
後端服務升級和重新開機，可能會在您的應用程式中造成這些問題。

### <a name="resolution"></a>解決方法
如果應用程式程式碼使用 SDK，則重試原則已經內建且作用中。 應用程式會重新連線，而不會對應用程式/工作流程造成重大影響。

## <a name="unauthorized-access-send-claims-are-required"></a>未經授權的存取：需要傳送宣告

### <a name="symptoms"></a>徵狀 
當您嘗試使用具有傳送許可權的使用者指派受控識別，從內部部署電腦上的 Visual Studio 存取服務匯流排主題時，可能會看到此錯誤。

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>原因
身分識別沒有存取服務匯流排主題的許可權。 

### <a name="resolution"></a>解決方法
若要解決此錯誤，請安裝[AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)程式庫。  如需詳細資訊，請參閱[本機開發驗證](../key-vault/general/service-to-service-authentication.md#local-development-authentication)。 

若要瞭解如何將許可權指派給角色，請參閱[使用 Azure Active Directory 來驗證受控識別，以存取 Azure 服務匯流排資源](service-bus-managed-service-identity.md)。

## <a name="service-bus-exception-put-token-failed"></a>服務匯流排例外狀況： Put token 失敗

### <a name="symptoms"></a>徵狀
當您嘗試使用相同的服務匯流排連線傳送超過1000個訊息時，您會收到下列錯誤訊息： 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>原因
使用服務匯流排命名空間的單一連接來傳送和接收訊息時，會使用權杖數目限制。 其為1000。 

### <a name="resolution"></a>解決方法
開啟與服務匯流排命名空間的新連接，以傳送更多訊息。

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [Azure Resource Manager 例外](service-bus-resource-manager-exceptions.md)狀況。 它會列出使用 Azure Resource Manager (透過範本或直接呼叫) 來與 Azure 服務匯流排互動時所產生的例外狀況。
- [訊息例外](service-bus-messaging-exceptions.md)狀況。 它會提供 Azure 服務匯流排的 .NET Framework 所產生的例外狀況清單。