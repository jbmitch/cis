---
copyright:
  years: 2018
lastupdated: "2018-03-13"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 關於 IBM Cloud Internet Services (CIS)
IBM Cloud Internet Services (CIS) 會針對在 IBM Cloud 上執行其業務的客戶，提供快速、高效能、可靠且安全的網際網路服務。   

IBM CIS 透過為您建立預設值快速進行，而您可以使用 API 或使用者介面輕鬆變更。以下是一些經常變更的參數：

 * **DNS 設定**：您可以使用 IBM CIS 來管理 DNS，也可以建立 CNAME 記錄。
 * **加密設定 (TLS)**：預設值是`彈性`模式，可加密主機與 IBM CIS 邊緣伺服器之間的連線，但不會加密 IBM CIS 邊緣伺服器與原點伺服器之間的通訊。

## Web 應用程式防火牆 (WAF)
IBM CIS 提供「Web 應用程式防火牆 (WAF)」功能，以檢查 Web 資料流量來尋找可疑的活動。它可以根據規則集自動過濾出非法的資料流量，以查看 GET 及 POST 型 Web 要求。您可以使用各種規則集來決定要封鎖、盤查或通過的資料流量。我們的 WAF 可以封鎖評論垃圾郵件、跨網站 Script 攻擊及 SQL 注入。

您可以自行決定啟用或停用 WAF。建議您一律開啟它。

## DDoS 攻擊保護
IBM CIS 提供您的網站與攻擊者之間的 DDoS 保護，不論攻擊的大小或持續時間為何。

## 負載平衡
「IBM CIS 負載平衡」是在「授權性 DNS 層次」運作。它包含進階性能檢查來監視原點的性能，並動態調整 DNS 回應。此外，您還可以配置「廣域負載平衡 (GLB)」的「地理」原則。

### 性能檢查
「負載平衡性能檢查」是透過定期 HTTP 或 HTTPS 要求對特定的 URL 執行，並且已配置可自訂的間隔、逾時及狀態碼。將原點伺服器標示為性能不佳時，會使用快速失效接手路徑，讓訪客從失敗狀況中離開。
 
### 地理原則及廣域負載平衡 (GLB)
「地理」原則可讓您根據該用戶端的地理位置，來控制將給定用戶端導向至其中的原點伺服器。例如，您可以配置「地理」原則，以將歐洲的訪客傳送至您網站或應用程式的最接近歐洲原點、將美國訪客傳送至北美原點等等。

## 快取
快取可以儲存最接近網站訪客的靜態 Web 內容，進而大幅加強網站的效能。我們的全球分散式遞送環境可讓 Web 內容擁有者在全球提供無縫經驗。  
 
## 使用 TLS 加密
使用「傳輸層安全 (TLS)」，加密往返於您的網站的通訊。網站變成作用中之後最多可能需要 24 小時，才會發出您的新憑證。

您可以在[常見問題](faq.html)中找到 TLS 的相關資訊。
