#RMStore

[![CocoaPods Version](https://cocoapod-badges.herokuapp.com/v/RMStore/badge.png)](http://cocoadocs.org/docsets/RMStore) [![Platform](https://cocoapod-badges.herokuapp.com/p/RMStore/badge.png)](http://cocoadocs.org/docsets/RMStore)
[![Build Status](https://travis-ci.org/robotmedia/RMStore.png)](https://travis-ci.org/robotmedia/RMStore)
[![Join the chat at https://gitter.im/robotmedia/RMStore](https://badges.gitter.im/robotmedia/RMStore.svg)](https://gitter.im/robotmedia/RMStore?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

A lightweight iOS library for In-App Purchases.

RMStore adds [blocks](#storekit-with-blocks) and [notifications](#notifications) to StoreKit, plus [receipt verification](#receipt-verification), [content downloads](#downloading-content) and [transaction persistence](#transaction-persistence). All in one class without external dependencies. 

##Warning

This cannot be used unless you ONLY use non-consumable in-apps. Do yourself a favor and use the APIs directly instead - it isn't that much code and you will need to know them well in order to mitigate the bugs Apple has in store for you, hidden in the framework.

##StoreKit with blocks

RMStore adds blocks to all asynchronous StoreKit operations. And StoreKit does not work with that - its just not designed around that kind of principle. 

###Add payment

Adding a payment sends a asynchronous call to Apple and storing a block - hoping they will correlate when the request comes back, which cannot be guaranteed.

Imagine you send of two purchases with the same productId, the first you cancel, the second is paid. RMStore will take the first that come back and send it with the first block, and the second one with the second. It may not be a big deal if these come in the wrong order - one will be paid and that will be handled. The problem arises when you get "respawned purchases".

###Respawned purchases

In certain cases StoreKit will "respawn" old purchases, as if they were new. This can make a cancelled purchase look paid, or vice versa. If the respawn comes back before your new payment, RMStore will use it instead of the "real" one and send it to the complete-block. The second payment request will be ignored.

At worst this leads to customers missing their purchased goods, and cannot be handled with a block approach, since storekit isn't built for that.

####When are there respawns?

1. When a subscription is bought that has not yet expired. 
2. When the internet connection on the divice is bad storeKit can sometimes assume that the device never got the first message - and send them again, (this is a guess but it seems to behave like that). The result is respawned purchases, which you cannot match together afterwards.
3. When you expect it the least.

Also when the app starts from having a non-completed transaction in the queue, it will seem as an old purchase coming back to life - but it could just as well be that the user quit your app before the finishTransaction: message was received (or sent depending on the situation). In either case, RMStore will not have a block and not know how to handle that situation. If the user is fast and on a slow network, discovers that the previous purchase did not make it through and makes a new one - you will have something behaving as a respawn.

RMStore can handle most of these cases by reporting all purchases through regular delegate callbacks or notifications, but then the point of having blocks in the first place is gone.

##License

 Copyright 2013-2014 [Robot Media SL](http://www.robotmedia.net)

 Licensed under the Apache License, Version 2.0 (the "License");
 you may not use this file except in compliance with the License.
 You may obtain a copy of the License at

 http://www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.
