# PPJSONSerialization [中文介绍](https://github.com/PonyCui/PPJSONSerialization/wiki)
## Introduce
PPJSONSerialization is a Swift JSON Helper Library, it helps you to convert JSON string to Swift Class.

Thanks for SwiftyJSON(https://github.com/SwiftyJSON/SwiftyJSON) brings a great way to deal with JSON, and it's convenience for us.

The way you use SwiftyJSON like this.

```swift
let json = JSON(data: dataFromNetworking)
if let userName = json[0]["user"]["name"].string{
  //Now you got your value
}
```

In my opinion, Apple gaves us a strong type language, why should we still using the old type (Obj-C) codeing?

So, the way you use PPJSONSerialization may like this

```swift
// Define a Simple Struct;
// Be careful, All Struct are not support dictionary type;
// All properties must provide default value and type.
class SimpleStruct: PPJSONSerialization {
    var simpleStr = ""
    var simpleInt = 0
    var simpleBool = false
    var simpleDouble = 0.0
    var simpleArray = [0]
}

let simpleJSON = "{\"simpleStr\":\"String Value\", \"simpleInt\":1024, \"simpleBool\": true, \"simpleDouble\": 1024.00, \"simpleArray\": [1,0,2,4]}"
if let simpleObject = SimpleStruct(JSONString: simpleJSON) {
  print(simpleObject.simpleStr) // Use the JSON value as an object
}
```

Or this

```swift
class SimpleStruct: PPJSONSerialization {
    var simpleStr:String?
    var simpleInt:Int = 0
    var simpleBool:Bool = false
    var simpleDouble:Double = 0.0
    var simpleArray = [Int]()
}
```

Don't worry about the type, you assign the type of a property in Struct, PPJSONSerialization will try to convert it from JSON.
```swift
func testTypeTransfer() {
  let typeErrorJSON = "{\"simpleStr\": 1024, \"simpleInt\": \"1024\", \"simpleBool\": null, \"simpleDouble\": \"Bool Value\", \"simpleArray\": {}}"
  if let simpleObject = SimpleStruct(JSONString: typeErrorJSON) {
    XCTAssert(simpleObject.simpleStr == "1024", "Pass")
    XCTAssert(simpleObject.simpleInt == 1024, "Pass")
    XCTAssert(simpleObject.simpleBool == false, "Pass")
    XCTAssert(simpleObject.simpleDouble == 0.0, "Pass")
    XCTAssert(simpleObject.simpleArray.count <= 0, "Pass")
  }
}
```

Struct may contains another Struct or even an Array contains Struct objects, PPJSONSerialization can easily handle this case.
```swift
// Define a Building Struct to deal with dictionary contains JSON
class BuildingStruct: PPJSONSerialization {
    var buildNumber = ""
    var managementRoom = RoomStruct()
    var buildRooms = [RoomStruct]()
}

// Define a Room Struct to handle sub dictionary JSON
class RoomStruct: PPJSONSerialization {
    var roomNumber = 0
    var roomSize: Double = 0.0
}

func testDictionaryContainsJSON() {
  let dictionaryContainsJSON = "{\"buildNumber\": \"B\", \"managementRoom\":{\"roomNumber\":101, \"roomSize\":10.14}, \"buildRooms\":[{\"roomNumber\":632, \"roomSize\":6.6}, {\"roomNumber\":633, \"roomSize\":6.7}]}"
  if let buildingObject = BuildingStruct(JSONString: dictionaryContainsJSON) {
    XCTAssert(buildingObject.buildNumber == "B", "Pass")
    XCTAssert(buildingObject.managementRoom.roomNumber == 101, "Pass")
    XCTAssert(buildingObject.managementRoom.roomSize == 10.14, "Pass")
    XCTAssert(buildingObject.buildRooms.count == 2, "Pass")
    XCTAssert(buildingObject.buildRooms[0].roomNumber == 632, "Pass")
    XCTAssert(buildingObject.buildRooms[0].roomSize == 6.6, "Pass")
    XCTAssert(buildingObject.buildRooms[1].roomNumber == 633, "Pass")
    XCTAssert(buildingObject.buildRooms[1].roomSize == 6.7, "Pass")
  }
}
```

Override the mapping function and return your custom mapping Dictionary, so the JSON key can map to custom Property key.
```swift
// Define a Map Struct and override mapping() return, you can map the JSON key to Custom Property key
class MapStruct: PPJSONSerialization {
    override func mapping() -> [String : String] {
        return ["mapStr": "simpleStr"]
    }

    //ReverseMapping is high priority then mapping
    override func reverseMapping() -> [String : String] {
        return ["simpleStr": "mapStr"]
    }

    var simpleStr = ""
}
func testMapping() {
  let simpleJSON = "{\"mapStr\": \"String Value\"}"
  if let simpleObject = MapStruct(JSONString: simpleJSON) {
    XCTAssert(simpleObject.simpleStr == "String Value", "Pass")
  }
}
```

PPJSONSerialization could serialize Class to JSON, just try the following code
```swift
let simpleObject = SimpleStruct()
simpleObject.simpleStr = "String Value"
simpleObject.simpleBool = true
simpleObject.simpleInt = 1024
simpleObject.simpleDouble = 1024.01
simpleObject.simpleArray = [1, 0, 2, 4]
let JSONString = simpleObject.JSONString()
let JSONData = simpleObject.JSONData()
```

PPJSONSerialization is still in development, welcome to improve the project together.

## Requirements
* iOS 7.0+ / Mac OS X 10.10+
* Xcode 7.0
* Swift 2.0

## Integration
Add ```PPJSONSerialization.swift``` into your project, that's enough

## License
MIT License, Please feel free to use it.

## Thanks
* Thanks for @onevcat suggest use Failable init.
* Thanks for @neil-wu using and reported issues.
