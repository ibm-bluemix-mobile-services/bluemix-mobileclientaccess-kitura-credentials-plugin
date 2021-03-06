# Kitura Credentials plugin for the Mobile Client Access service

[![Swift][swift-badge]][swift-url]
[![Platform][platform-badge]][platform-url]
[![Build Status](https://travis-ci.org/ibm-bluemix-mobile-services/bms-mca-kitura-credentials-plugin.svg)](https://travis-ci.org/ibm-bluemix-mobile-services/bms-mca-kitura-credentials-plugin)

[swift-badge]: https://img.shields.io/badge/Swift-3.0-orange.svg
[swift-url]: https://swift.org
[platform-badge]: https://img.shields.io/badge/Platforms-OS%20X%20--%20Linux-lightgray.svg
[platform-url]: https://swift.org

## Known limitations

The SDK is still in development phase and should not be used for production. At this point digital signatures of access and identity tokens are not validated, which makes it easier to fake these tokens by a malicious party.

## Installation

```swift
import PackageDescription

let package = Package(
    dependencies: [
        .Package(url: "https://github.com/ibm-bluemix-mobile-services/bms-mca-kitura-credentials-plugin.git", majorVersion: 0, minor: 5)
    ]
)
```

## Releases

* 0.5.x releases were tested on OSX and Linux with Swift 3.1
* 0.4.x releases were tested on OSX and Linux with Swift 3.0.1
* 0.3.x releases were tested on OSX and Linux with Swift DEVELOPMENT-SNAPSHOT-2016-06-20-a
* 0.2.x releases were tested on OSX and Linux with Swift DEVELOPMENT-SNAPSHOT-2016-06-06-a
* 0.1.x releases were tested on OSX and Linux with Swift DEVELOPMENT-SNAPSHOT-2016-05-03-a

## Usage

```swift
import Kitura
import KituraNet
import KituraSys
import MobileClientAccessKituraCredentialsPlugin
import MobileClientAccess
import Credentials

let credentials = Credentials()
credentials.register(plugin: MobileClientAccessKituraCredentialsPlugin())

let router = Router()
router.all("/public", handler: { (request, response, next) in
	response.status(.OK).send("Hello from a public resource!")
	next()
})

router.all("/protected", middleware: credentials)
router.all("/protected", handler: { (request, response, next) in

	// UserProfile is available as part of Kitura Credentials workflow
	let userProfile = request.userProfile

	// Additional Mobile Client Access Authorization Context is added after successful authorization token validation
	let authContext = request.userInfo["mcaAuthContext"] as! AuthorizationContext

	response.status(.OK).send("Hello from a protected resource, " + authContext.userIdentity!.id)
	next()
})

HTTPServer.listen(port: 1234, delegate: router)

Server.run()
```

## License

Copyright 2016 IBM Corp.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
