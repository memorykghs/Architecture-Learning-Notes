# 4 - Using event sourcing with Aggregates

## 本章大綱
**Using Event Sourcing With Aggregates**
* Using Event Sourcing With Aggregates
    * Step 1 - Event Sourcing
    * Step 2 - Persistens Events NOT Current State
* Application Architecture
    * Request Handling in an Event Sourced Application
    * Event Store Publishes Events Consumed by Other Services
    * Event Store = Database + Message Broker
* Benefits and Drawbacks
    * Benefits of Event Sourcing
    * Drawbacks of Event Sourcing…

![](/images/microservices/4-1.png)

以事件為中心的持久性???

## Using Event Sourcing With Aggregates
#### Step 1 - Event Sourcing

總之當某個狀態發生變化時，你要做的就是寫入一筆資料到 message table。不過在此之前，你要先定義出一些事件狀態 ( 並非依照客戶的行為模式 )，像是 credit limit 其實不能算是客戶行為，但是因為額度限制這筆交易會是失敗的。

- For each domain object (i.e. DDD aggregate)：
    - Identify (state changing) domain events, e.g. use Event Stroming
    - Define Event classes
- For example, Order events: OrderCreated, OrderCancelled, OrderApproved, OrderRejected, OrderShipped…

> Event-sourcing Pattern 事件來源模式

補充資料：[Event-sourcing Pattern 事件來源模式](https://microservices.io/patterns/data/event-sourcing.html)

#### Step 2 - Persistens Events NOT Current State

顛覆以往我們持久化數據的方式 ( 像是幫 Order class 建立 Entity 並且使用 JPA hibernate 做映射寫進DB )，現在則是針對 events 去做持久化。

![](/images/microservices/4-2.png)

讓人難以想像因為這有點像是將一團 JSON 參數序列化 ( 到 DB 中 )，DB 裡面也不會含有 Order 的狀態。

![](/images/microservices/4-3.png)

所以如果要撈出 Order 的狀態或是資料，在這個系統中，你只需要去查詢特定的 event entity 然後 replay 他們去初始化原始訂單的狀態，然後重組 Order Entity。( 跟 snapshot 有關係???)

![](/images/microservices/4-4.png)

> 結論：**The present is a fold over history.**
currentState = fold (applyEvent, initialState, events)

## Application Architecture

經過上面一系列的調整，現在 Application 的結構看起來就像下面這個樣子。

* 每個 Aggregate 將被轉換成一系列的 events 並儲存在 Event Store 中
* 而且每個 Aggregate 各自都是 private，只有相對的 service 可以 access

![](/images/microservices/4-5.png)

#### Request Handling in an Event Sourced Application

這邊將示範 Event-driven Architecture 下的 Application 要怎麼處理 Request。( 這段好難懂也好難解釋QQ )

1. 接收到 Order Reqeust，先查詢跟該 Order 有關的 event???
2. 用 constructor 建立新實例
3. past 查出的 event 然後再次初始化 Order (可以看到 `applyEvent ( past Events )` 方法會更新當前的狀態 )
4. 產生新的 event 並進行 process 處理，處理完後會回傳一系列的 events，代表狀態被改變了
5. 新的 event 會被儲存到 Event Store 及 DB ( done by optimistic locking )

#### Event Store Publishes Events Consumed by Other Services

⌚42:36

事件可以被各個 services 訂閱，當完成一件事件後，有訂閱的 services 應該要收到通知，並 trigger 對應的 services 進行狀態的更新。

![](/images/microservices/4-6.png)

> **CQRS ( Command Query Responsibility Segregation ) 命令與查詢責任分離 ( 或讀寫分離 )**
> 

🙄不懂這個後面真的不用看…

這兩種操作的行為分別為：

- 命令 ( Command )：執行後**會改變物件狀態**。因為會改變物件狀態，所以**不會有回傳值**。
- 查詢 ( Query )：查看物件結果，而且**不會改變物件的狀態**、對物件本身**沒有副作用**。有回傳值。

總之就是在微服務的的架構下我們都不討論物件的行為或是狀態，取而代之的是服務的行為，也就是上面一直提到的 event。當服務系統的資料庫遭遇到效能瓶頸時，就會使用「讀寫分離」來解決問題。

![](/images/microservices/4-7.png)

補充資料一：[淺談 CQRS 的實現方法](https://medium.brobridge.com/%E6%B7%BA%E8%AB%87-cqrs-%E5%AF%A6%E7%8F%BE%E6%96%B9%E6%B3%95-3b4fcb8d5c86)
補充資料二：https://ithelp.ithome.com.tw/articles/10282435

#### Event Store = Database + Message Broker
![](/images/microservices/4-8.png)

Event Store 其實很像 Database 跟 Message Broker 的混和體，因為一方面他提供 API 給外部做 insert events，另一方面也能讓外部依 primary key 取得 event，這個行為就很像平常使用的 DB。而他也擁有 Message Broker 類型的 API，因為他可以讓其他的 services 訂閱。

有些人會用 MySQL 或是 Postgre 來實作他。

## Benefits and Drawbacks
#### Benefits of Event Sourcing

* Solves data consistency issues in a Microservice / NoSQL based architecture
    
    解決在微服務或是 NoSQL 架構中資料一致性的問題，因為我們可以透過發送 events 來讓資料最後狀態一致
    
* Reliable event publishing：publishers events needed by predictive analytics etc., user notifications…
* Eliminates O/R mapping problem (mostly)
    
    消除了一般在 Spring 當中的物件關係應設 ( Object Relational Mapping )，因為我再也不需要依賴儲存相對的物件到 DB，而是依照物件狀態轉換後的 event log
    
* Reifies state changes：
    
    另外你也可以隨時查閱每個物件的狀態，因為 event store 儲存的就是物件狀態的歷史紀錄
    
    * Built in, reliable audit log
    * temporal queries

#### Drawbacks of Event Sourcing
* Requires application rewrite
    
    最實際的問題，整個系統都要重寫😂要把原本單體式系統的扣 migrate 到 microservices 上
<br/>
    
* Weird and unfamiliar style of programming
* Events = a historical record of your bad design decisions
    
    像是擴充 DB 欄位的時候，舊資料會很麻煩???
<br/>

* Must detect and ignore duplicate events
    * Idempotent evetn handlers
    * Track most recent event and ignore older ones
    * …
<br/>

* Querying the event store can be challenging
    
    傳統的查詢只需要查詢 DB 這個 Order 的狀態及資料就好，但是在 event sourcing architecture 下，必須先找到與 Order 狀態相對應的 event，而且很有可能撈出一堆。
<br/>
    
* Some queries might be complex / inefficient, e.g. accounts with a balance > X
    
    承上，所以就必須要再做一些子查詢去過濾資料，像是 balance > X
<br/>
    
* Event store might only support lookup of events by entity id
    
    Event stors 很有可能只有一個，而且是一個只支援以 primary key 查詢 events 的 NoSQL database，這種狀況下根本不可能提供歷史查詢的功能。
<br/>
    
* Must use Command Query Responsibility Segregation (CQRS) to handle queries ⇒ application must handle eventually consistent data
    
    承上，這也是為什麼講者建議使用 CQES command 額外提專門用於查詢的功能。