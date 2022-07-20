# 3 - How to Maintain Data Consistency Between Aggregates?

## 本章大綱
**Maintaining Consistency Between Aggregates**
* How to maintain data consistency between aggregates?
    * Event Driven Model
    * Eventually Consistent Credit Checking
* Complexity of Compensating Transactions
* How Atomically Update Database and Publish an Event
    * Reliably Publish Events When State Changes

![](/images/microservices/3-1.png)

## Event Driven Model
當發生了某些事件，例如客戶下了訂單造成資料異動時，這時候 Order Service 就會發出一個 event，其他 service 則是會 subscribe 它並根據指示來更新自己的狀態。中間有可能有不同狀態的 event，像是 customer credit check 行為有機會建立 3 個 transaction ( create → reserve → credit check fail ) 且同時改變 Order Service、Customer Service 的狀態。

![](/images/microservices/3-2.png)

## Eventually Consistent Credit Checking

![](/images/microservices/3-3.png)

1. order created
2. event published
3. event consumed by customer service which updates the order
4. customer service updates the order with the credit reservation
5. customer service and then publish an event to say the credit has been reserved
6. event consumed by order service and turned status to APPROVED

以上是 microservices 實現交易一致性的方式，透過 Message Bus 來對所有的 service 溝通並更改狀態。

## Complexity of Compensating Transactions
問題來了!上面實現不同 database 的機制看似很簡單，但同時也因為每一次的動作都需要經歷長長的一串步驟，假設當第五個步驟發生錯誤，我們也沒有辦法 undo 前幾個步驟做的事情。

* ACID transactions can simply rollback

**BUT**!!!

* Developer must write application logic to “rollback” eventually consistent transactions
* ex：
    * CreditCheckFailed ⇒ cancel Order
    * Money transfer destination account closed ⇒ credit source account
* Careful design required!

## How Atomically Update Database and Publish an Event!
![](/images/microservices/3-4.png)

如果這個交易是可以被信賴的，代表它必須滿足原子性 ( atomaically )，以上圖的例子來說，如果現在 update Order Service，但是它發送 message 失敗導致 Message Broker 沒有接收到訊息，此時系統狀態變成是不一致的。這也是為什麼前面提到 2PC is not an option 的原因。

#### Reliably Publish Events When State Changes

以上的問題有許多策略可以解決，不過這邊將只會 focus 在其中一種上。

👇 Application Event Pattern
![](/images/microservices/3-5.png)

* Like eBay, they use table as a event queue
    
    update Order Service 的同時也 update event table
    
* LinkedIn 則是將 transaction log 作為 puslish events 的依據