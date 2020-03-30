---
title: Application Insights 和 Log Analytics 使用的 IP 位址 | Microsoft Docs
description: Application Insights 所需的伺服器防火牆例外狀況
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 12/19/2019
ms.openlocfilehash: 74d696c19ac2a2d0d367f5a018fde8cd3a0eedb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535199"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Application Insights 和 Log Analytics 使用的 IP 位址
[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 服務會使用一些 IP 位址。 如果您所監視的應用程式裝載於防火牆後面，您可能需要知道這些位址。

> [!NOTE]
> 雖然這些位址是靜態的，但可能隨時需要變更。 除了可用性監視和需要輸入防火牆規則的 Webhook 之外，Application Insights 的所有流量都表示輸出流量。
> 
> 

> [!TIP]
> 將 https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom 新增至您慣用的 RSS/ATOM 讀取器，以訂閱此頁面的 RSS 摘要，而在最新變更可用時收取通知。
> 
> 

## <a name="outgoing-ports"></a>連出連接埠
您需要在伺服器防火牆開啟某些連出連接埠，以允許 Application Insights SDK 和/或狀態監視器將資料傳送至入口網站：

| 目的 | URL | IP | 連接埠 |
| --- | --- | --- | --- |
| 遙測 |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170 | 443 |
| 即時指標流（美國東部） |use.rt.prod.applicationinsights.trafficmanager.net |23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207 |443 |
| 即時指標流（美國中南部） |ussc.rt.prod.applicationinsights.trafficmanager.net |157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113 |443 |
| 即時指標流（北歐） |eun.rt.prod.applicationinsights.trafficmanager.net |40.115.103.168<br/>40.115.104.31<br/>40.87.140.215<br/>40.87.138.220 |443 |
| 即時指標流（西歐） |euw.rt.prod.applicationinsights.trafficmanager.net |13.80.134.255<br/>40.68.61.229<br/>23.101.69.223<br/>52.232.106.242 |443 |
| 即時指標流（東亞） |ase.rt.prod.applicationinsights.trafficmanager.net |23.100.90.7<br/>23.101.13.65<br/>23.101.0.142<br/>23.101.9.4 |443 |
| 即時指標流（東南亞） |asse.rt.prod.applicationinsights.trafficmanager.net |207.46.224.101<br/>207.46.236.191<br/>137.116.151.139<br/>13.76.87.86 |443 |

## <a name="status-monitor"></a>狀態監視器
狀態監視器組態 - 只有在進行變更時才需要。

| 目的 | URL | IP | 連接埠 |
| --- | --- | --- | --- |
| 組態 |`management.core.windows.net` | |`443` |
| 組態 |`management.azure.com` | |`443` |
| 組態 |`login.windows.net` | |`443` |
| 組態 |`login.microsoftonline.com` | |`443` |
| 組態 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 組態 |`auth.gfx.ms` | |`443` |
| 組態 |`login.live.com` | |`443` |
| 安裝 | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>可用性集合
這是用來執行 [可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md) 的位址清單。 如果您想要在您的應用程式上執行 Web 測試，但您的 Web 伺服器限於為特定用戶端提供服務，則您必須允許來自我們的可用性測試伺服器的連入流量。

如果使用 Azure 網路安全性群組，只需添加**入站連接埠規則**，即可選擇**服務標記**作為**源****服務標記**，從而允許來自應用程式見解可用性測試的流量**ApplicationInsightsAvailability**。

>[!div class="mx-imgBorder"]
>![在"設置"下選擇"入站安全規則"，然後選擇在選項卡頂部添加](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![添加入站安全規則選項卡](./media/ip-addresses/add-inbound-security-rule2.png)

為來自這些位址 (IP 位址會依照位置分組) 的連入流量開啟連接埠 80 (http) 和 443 (https)︰

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

## <a name="application-insights-api"></a>Application Insights API
| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| API 文件 |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Azure 管道注釋擴展 |aigs1.aisvc.visualstudio.com |動態|443 |

## <a name="log-analytics-api"></a>Log Analytics API

| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| API |api.loganalytics.io<br/>*.api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| API 文件 |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |

## <a name="application-insights-analytics"></a>Application Insights 分析

| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| Analytics 入口網站 | analytics.applicationinsights.io | 動態 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 動態 | 80,443 |
| 媒體 CDN | applicationanalyticsmedia.azureedge.net | 動態 | 80,443 |

注意：*.applicationinsights.io 網域由 Application Insights 團隊所擁有。

## <a name="log-analytics-portal"></a>Log Analytics 入口網站

| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| 入口網站 | portal.loganalytics.io | 動態 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 動態 | 80,443 |

注意: *.loganalytics.io 網域由 Log Analytics 小組所擁有。

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure 入口網站擴充功能

| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| Application Insights 擴充功能 | stamp2.app.insightsportal.visualstudio.com | 動態 | 80,443 |
| Application Insights 擴充功能 CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | 動態 | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK

| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | 動態 | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.windows.net | 動態 | 80,443 |

## <a name="alert-webhooks"></a>警示 Webhook

| 目的 | IP | 連接埠
| --- | --- | --- |
| 警示 | 23.96.11.4 | 443 |

## <a name="profiler"></a>分析工具

| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| 代理程式 | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| 入口網站 | gateway.azureserviceprofiler.net | 動態 | 443
| 存放裝置 | *.core.windows.net | 動態 | 443

## <a name="snapshot-debugger"></a>快照集偵錯工具

> [!NOTE]
> 分析工具和快照集偵錯工具共用同一組 IP 位址。

| 目的 | URI | IP | 連接埠 |
| --- | --- | --- | --- |
| 代理程式 | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| 入口網站 | ppe.gateway.azureserviceprofiler.net | 動態 | 443
| 存放裝置 | *.core.windows.net | 動態 | 443
