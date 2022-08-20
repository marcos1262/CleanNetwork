# CleanNetwork

![swift package manager](https://img.shields.io/badge/swift%20package%20manager-compatible-brightgreen)
![swift](https://img.shields.io/badge/swift-5.5-orange?logo=swift)
![platforms](https://img.shields.io/badge/platforms-macOS--10.15_iOS--13_tvOS--13_watchOS--6-yellowgreen)
![license](https://img.shields.io/badge/license-MIT-green)

CleanNetwork is a URLSession wrapper for using async/await. You can use CleanNetwork for creating a modular network layer in projects.

## Installation
### Swift Package Manager

Once you have your Swift package set up, adding CleanNetwork as a dependency is as easy as adding it to the `dependencies` value of your `Package.swift`.

```swift
dependencies: [
    .package(url: "https://github.com/alperen23230/CleanNetwork", .upToNextMajor(from: "1.0.0"))
]
```

## Usage

### Simple Usage
Firstly you have to create a request object for sending request. You can use `CLNetworkDecodableRequest` type for this.

```swift
struct ExampleRequest: CLNetworkDecodableRequest {
    typealias ResponseType = ExampleDecodableModel

    let endpoint: CLEndpoint
    let method: CLHTTPMethod
    
    init(endpoint: CLEndpoint, method: CLHTTPMethod) {
        self.endpoint = endpoint
        self.method = method
    }
}
```
#### Creating CLEndpoint
CLEndpoint is a struct which represents an API endpoint. It has a baseURL, path and queryItems variables. For baseURL, there is a global variable for this. It's called `BASE_URL`. (For example you can set BASE_URL in AppDelegate) For url scheme, it uses `https` by default but you can change using `URL_SCHEME` global variable.

```swift
public struct CLEndpoint {
    public var baseURL: String
    public var path: String
    public var queryItems: [URLQueryItem]
    
    public init(baseURL: String = BASE_URL, path: String, queryItems: [URLQueryItem] = []) {
        self.baseURL = baseURL
        self.path = path
        self.queryItems = queryItems
    }
}
```

#### Creating Request Object
```swift
let endpoint = CLEndpoint(path: "/example")
let method: CLHTTPMethod = .get
let exampleRequest = ExampleRequest(endpoint: endpoint, method: method) 
```

#### Sending Request
You have to use `CLNetworkService` for sending request. You can use both shared object or creating object. Use `fetch` method of network service object.

```swift
do {
    let response = try await CLNetworkService.shared.fetch(exampleRequest)
    await MainActor.run {
        // Switch to main thread
    }
} catch {
    // Handle error here
}
```

### Advance Usage

#### Requests
There are 3 request protocol. 

`CLNetworkRequest` is the basic protocol. It's for fetching raw `Data`.

```swift
public protocol CLNetworkRequest {
    var endpoint: CLEndpoint { get }
    var method: CLHTTPMethod { get }
    var headers: [String: String] { get }
}
```

`CLNetworkDecodableRequest` is for fetching decodable response. You have to specify response type in your request. It has to be `Decodable`.

```swift
public protocol CLNetworkDecodableRequest: CLNetworkRequest {
    associatedtype ResponseType: Decodable
    
    var endpoint: CLEndpoint { get }
    var method: CLHTTPMethod { get }
    var headers: [String: String] { get }
}
```

`CLNetworkBodyRequest` is for sending body request. You have to specify body type in your request. It has to be `Encodable`.

```swift
public protocol CLNetworkBodyRequest: CLNetworkDecodableRequest {
    associatedtype RequestBodyType: Encodable
    
    var requestBody: RequestBodyType { get }
}
```

