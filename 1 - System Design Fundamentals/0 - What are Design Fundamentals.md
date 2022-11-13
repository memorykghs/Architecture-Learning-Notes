# 0 - System Design Fundamentals
## Outline
1. Waht are Design Fundamental
2. Client-Server Model
3. Network Prtocols
4. Storage
5. Latency and Throughput
6. Availability
7. Caching
8. Proxies
9. Load Balancers
10. Hashing
11. Relational Databases
12. Key-value Stores
13. Specialized Storage Paradigms

## 4 個系統設計的基礎知識
1. **Underlying or foundational knowledge**
  必須對某一些結構或是架構有基本的理解，才有辦法對其進行改良或設計。像是 client server model，你必須先知道這是什麼，才能夠設計出好的現代 (moder-day system???) 系統。
2. **Key characteristics of system**
  像是：
  * availability
  * wait and see
  * throughput
  * redundancy
  * consistency
  定義這個系統的主要特性，再針對這些特性去做延伸的設計。
3. **Actual component of system**
  比 key characteriscs 更有型的，且可以實際被 implement 到系統中的元素，如：
  * load balancers
  * proxies
  * caches
  * rate limiting
  * leader election
4. **Actaul tech real existing products or services**
  是既有或現存的工具或服務，可以直接引入系統，如：
  * Zookeeper
  * XCD
  * nginx
  * Reddits
  * Amazon S3
  * Google Cloud storage

## Client-Server Model
connections on the same machine without colliding, the pick a port to listen on. A port is an integer between 0 and 65,535.

Typically, ports 0-1023 are reserved for system ports ( also called well-known ports) and shouldn't be used by user-level processes. Certain ports have pre-define uses, and although you usually won't be required to have them memorized, they can sometimes come in handy.  

DNS
Short  for Domain Name System, it describes the entites and protocols involved in the translation from domain names to IP Address. Typically machines make a DNS query to a well known entity whicj is resonsible for returning the IP address (or multiple ones) of the requestes domain name in the response.
762 / 5,000

為了讓多個程序在同一台機器上偵聽新的網絡連接而不會發生衝突，請選擇一個要偵聽的端口。 端口是 0 到 65,535 之間的整數。

通常，端口 0-1023 是為系統端口（也稱為眾所周知的端口）保留的，不應由用戶級進程使用。 某些端口具有預先定義的用途，儘管您通常不需要記住它們，但它們有時會派上用場。

域名系統
域名系統的縮寫，它描述了從域名到 IP 地址的轉換所涉及的實體和協議。 通常，機器對眾所周知的實體進行 DNS 查詢，該實體負責在響應中返回請求域名的 IP 地址（或多個 IP 地址）。

