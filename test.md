# MFNetworking

MFNetworking makes networking a delightful experience!

## Features

* DataSourceManager provides high-level network abstractions as well as greatly simplifying switching between remote HTTP endpoints and local data. 
* JSON parser for easily consuming JSON data

## Requirements

- iOS 8.0+
- Xcode 6.1.1+

## Getting Started

Copy the MFNetworking.framework into you project and add it an as Embedded Binary

# DataSourceManager 

## Usage

### Configuration

DataSourceManager supports two modes of operation:

1. Local data
2. One or more remote HTTP endpoints

#### Local 

To enable local data mode:

```swift

DataSourceManager.sharedInstance.environment = .LOCAL

```

#### Remote

To setup remote HTTP endpoints, you configure DataSourceManager with a set of keys and base URL strings.  For example:

```swift

DataSourceManager.sharedInstance.configureWithRemoteEnvironments(["host1": "http://httpbin.org/ip", "host2": "http://example.org"])

```

Or using the `DataSourceHosts` property in `Info.plist`:

```swift

func configureHosts() {
    if let hosts = NSBundle.mainBundle().infoDictionary?["DataSourceHosts"] as? [String: String] {
        var environments: [String: String] = [:]
        for (hostKey, urlString) in hosts {
            environments[hostKey] = urlString
        }
        DataSourceManager.sharedInstance.configureWithRemoteEnvironments(environments)
    }
}

```

Once remote endpoints are configured, you can enable a remote host via:

```swift

DataSourceManager.sharedInstance.environment = .REMOTE("host1")

```

### Making a Request

```swift

DataSourceManager.request(Request.Flights)
 .responseJSON { (request, response, json, error) in
    println(request)
    println(response)
    println(error)
}

```

where the `request` parameter is a type that adopts the `DataSourceRequest` protocol:

```swift

public protocol DataSourceRequest {
    var localFilename: String { get }
    func urlRequestWithBaseURLString(baseURL: String) -> NSURLRequest
}

```

For example:

```swift

enum Request: DataSourceRequest {
    case Flights
    
    var localFilename: String {
        switch self {
        case .Flights:
            return "local_JSON_filename"
        }
    }
    
    func urlRequestWithBaseURLString(baseURL: String) -> NSURLRequest {
        switch self {
        case .Flights:
            return NSURLRequest(URL: NSURL(string: baseURL + "/flights")!)
        }
    }
}

```

### Validation 

By default, all completed requests are considered successful.  To enable validation of the response, a chainable `validate` method is available which causes the error parameter to be set accordingly in the completion handler.  For example:

#### Manual Validation


#### Automatic Validation

```swift

DataSourceManager.request(Request.Flights).validate().responseJSON { (_, _, data, error) -> Void in
    if ( error != nil ) {
        // Handle an error 
        self.simpleAlertwithTitle("A network error has occurred. Please try again.")
    }
    else {
        // Handle a successful response
    }
}

```

#### Manual Validation

```swift

DataSourceManager.request(Request.Flights).validate(statusCode: 200..<300).responseJSON { (_, _, data, error) -> Void in
    if ( error != nil ) {
        // Handle an error 
        self.simpleAlertwithTitle("A network error has occurred. Please try again.")
    }
    else {
        // Handle a successful response
    }
}

```


# JSON

## WIP
