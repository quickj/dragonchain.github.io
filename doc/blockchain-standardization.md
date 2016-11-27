---
permalink: /blockchain-standardization
layout: documentation
section-type: documentation
title: Disney Blockchain Standardization Notes
sitemap:
priority: 1.0
---

## Notes From Disney

_Provided as input to the W3C Blockchain Community Group [6/2016] by Joe Roets - Disney - [Original email](https://lists.w3.org/Archives/Public/public-blockchain/2016May/0052.html)_

1. What features could DLT add to the Web Platform?
    - Secure distribution of data, i.e. Moving data to the edge/web service (lower latency and downtime)
    - Lightweight verification of data quality or information assurance at the endpoint
    - Ability to independently verify local data (e.g. authN/Z, configuration) with controlled risk (enterprise + trusted partner + external blockchain [Hyperledger or Bitcoin])
    - Real-time enterprise governance capabilities (e.g. in transactional information, configuration items, key management activities)
    - Micro-payment web service request applications
1. What would we need to add to the Web Platform to enable DLT?
    - For distributed data use cases, locally available network node or blockchain data (likely operated parallel)
    - For micro-payment applications, use of web service request components (e.g. 21.co marketplace headers HTTP_BITCOIN_MICROPAYMENT_SERVER and HTTP_RETURN_WALLET_ADDRESS)
    - Access to respective blockchain library(s)
1. Some aspects are nearing the point where standardization would be warranted – such as:
    - Proof/verification algorithms (e.g. PoW [SHA-256 v Scrypt v x11], PoS, proof of identity/trust, other proofs)
    - Abstraction of cryptographic libraries
    - Block verification structures
    - Consensus algorithm

WRT #3 above, it would be possible to create a foundation which defined pluggable component interfaces allowing the creation of custom blockchains with known best practice implementations to fit a given need. Most of these interfaces could theoretically be loosely defined today, and multiple implementations could readily be made available.

---

```

    Copyright 2016 Joe Roets and Super Happy Dragon Lucky
    
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
        http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

```

---

#### [Original Document and Comments](https://docs.google.com/document/d/1xtfK1C7JbOLx-YztqEDloKBRXWwtFBaLm9pcSwLygAg/edit#heading=h.ie9haz2a79hp)

