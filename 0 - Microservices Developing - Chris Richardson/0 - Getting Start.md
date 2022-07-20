# 0 - Getting Start
本篇為看完 Chris Richardson 的 developing microservices 演講的心得與筆記。

影片連結：https://www.youtube.com/watch?v=7kX3fs0pWwc&t=700s

![](/images/microservices/%E5%B1%A5%E6%AD%B7%E4%B8%8A%E7%9A%84%E6%88%91.png)

## 大綱
此篇分為五大主題。

##### 1. The Problem with Domain Models and Microservices
* Service Boundaries Enforce Modularity
    * Domain Model = Tangld Web of Classes
* Reliance on ACID Transactions to Enforce Invariants
* 2PC is Not a Viable Option

##### 2. Overview of Aggregates
* So what is Aggreate?

##### 3. Maintaining Consistency Between Aggregates
* How to maintain data consistency between aggregates?
    * Event Driven Model
    * Eventually Consistent Credit Checking
* Complexity of Compensating Transactions
* How Atomically Update Database and Publish an Event
    * Reliably Publish Events When State Changes
##### 4. Using Event Sourcing With Aggregates
##### 5. Example Application