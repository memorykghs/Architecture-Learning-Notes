# 1 - The Problem with Domain Models and Microservices

## 本章大綱
**The Problem with Domain Models and Microservices**
* Service Boundaries Enforce Modularity
    * Domain Model = Tangld Web of Classes
* Reliance on ACID Transactions to Enforce Invariants
* 2PC is Not a Viable Option

## Microservice Architecture

![](/images/microservices/1-1.png)

## Service Bounderies Enforce Modularity

最重要的是我們需要去強制地區分 Service 的邊界：

* 每個 service 有自己的 database、API
* 每個 service 內部的東西都是 private，唯一可以 access 的就是透過該 service 的 API
* modularity 模組化

> **OSGi ( Open Services Gateway initiative )**
Java 動態化模組的的一系列標準

因 Java 語言的特性，我們很難將程式內需要 packages 模組化，所以講者有提到這個。下面簡單提到幾個導入 microservices 會遇到的問題以及解決方式。

## Domain Model = Tangld Web of Classes
將單體式的 ( monolithic ) Application 拆分成多個 microservices，但這些 microservices 之間還是會有一些依賴存在，區分各個 service 的 domain 之後大概會像下面這樣。

![](/images/microservices/1-2.png)

## Reliance on ACID Transactions to Enforce Invariants

在單體式的應用程式中，當有一筆訂單進來時，我們通常會把這個行為視為一個交易，在這個交易中去 CRUD 相關的 table 像是 `ORDERS` 和 `CUSTOMERS`。但是在 microservices 的架構下，`ORDERS` 和 `CUSTOMERS` 分別有自己的 database，就沒有辦法把它放在同一個交易中處理，會有兩個問題：

1. 原本封裝的結構被破壞 ( 現在必須透過其他的 service 才能拿到想要的資訊 )
2. 必須拆成兩個獨立交易

## 2PC is Not a Viable Option
好處：
* 它可以確保一致性 Guarantees consistency

但是!但是!但是!

* 實作時必須去閱讀不同資料庫的細則才能了解它提供了什麼級別的隔離層級
* 在 Modern Application 中可能交雜了很多邏輯與 issue，所以通常會避開 2PC?
* 另外，2PC 也不支援一些技術像是 noSQL database、message broker 等等
    
    > p.s. **Message Broker ( 消息代理 )** 是一種跨應用程式的通訊技術，可用於建構集成機制，以支援 Cloud Native、microservices-based、serverless 和 hybrid cloud architectures ( 混和雲架構 ) 等。 
    
*  CAP theorem ⇒ 2PC impacts availibility
*  Doesn't fit with the NoSQL DB "transaction" model

> **2 Phases Commit**
由於前面提到資料庫 Transaction 必須滿足 ACID 特性，且一般常見的 Web Application 其實都是這類的大型架構。2PC 適當多個資料庫參與同一個 Transaction 時，為了保證 ACID 的特性以及 Strong Consistency 而衍生出的演算法。

補充資料：[2 Phases Commit](https://ithelp.ithome.com.tw/articles/10268612)

> **Consistency**

Consistency 是分散式系統中的多個節點或角色為了實現某個特定狀態或特定值的而達成的協議。

- **Strong Consistency** - 在各個分散式系統中，同一時間取得的值都一樣。例如在 A 系統中取得某 `key` 值為 1，那麼在 B 系統中的值也是 1。
- **Weak Consistency** - 與 Strong Consistency 相反，這些不同系統上的狀態在經過某段時間後仍然會達成一致性 ( Final Consistency )，但在事件發生地當下每個系統的值可能會是不一樣的。

補充資料：[Consistency](https://medium.com/@mena.meseha/understanding-of-consistency-in-distributed-systems-27da174cc05a)