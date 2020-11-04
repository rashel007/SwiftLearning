# SwiftLearning
New things i learn about swift programming language

## Table Of Contents
1. [Toupls](#toupls)<br/>
2. [Higher Order Function](#higherorderfunction)<br/>
3. [New way of handling getter setter](#gettersetter)<br/>
4. [Closed Range Operator](#closedrangedoperators)<br/>
5. [init, failable init, convenience init](#init)<br/>
6. [Computed Property](#computedproperty)<br/>
7. [Property Wrapper](#propertywrapper)<br/>
8. [Associatedtype](#associatedtype)<br/>
9. [Protocol Extention](#protocolextention)<br/>
10. [Closure](#closure)<br/>
11. [Mutating Function](#mutating)<br/>
12. [Lazy](#lazy)<br/>
13. [where keyword](#where)<br/>
14. [Error Handaling](#error)<br/>




### Toupls <a name="toupls"/>
> Tuples group multiple values into a single compound value. The values within a tuple can be of any type and don’t have to be of the same type as each other.

```swift
var httpError = (404, "Not Found")

print(httpError.0) // prints 404
print(httpError.1) // prints "Not Found"

var httpError2 = (statusCode: 404, message: "Not Found")

print(httpError2.statusCode) // prints 404
print(httpError2.message) // prints "Not Found"

let (statusCode, message) = httpError

print(statusCode) // prints 404
print(message) // prints "Not Found"

```


### New way of handling getter setter <a name="gettersetter"/>

```swift
class Person {
    private var name: String
    private var age: Int
    
    init(name: String, age: Int){
        self.name = name
        self.age = age
    }
    
    func getName() -> String {
        return self.name
    }
}


let person = Person(name: "Person 1", age: 23)
print(person.getName())

class Person2 {
    private(set) var name: String
    private(set) var age: Int
    var salary: Int {
        get { return 2000}
    }
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}


let person2 = Person2(name: "Person 2", age: 24)
//person2.name = "New Name"  // this will throw error
//person2.salary = 1000 // this will throw error coz salary is only getable 
print(person2.name)
```


### Closed Range Operator <a name="closedrangedoperators"/>
> The closed range operator (a...b) defines a range that runs from a to b, and includes the values a and b. The value of a must not be greater than b.

```swift
for (index in 1...5){
  print("\(index) times 5 is \(index * 5)")
}

// this prints 
//1 times 5 is 5
//2 times 5 is 10
//3 times 5 is 15
//4 times 5 is 20
//5 times 5 is 25
```

### Half Open Range Operator
> The half-open range operator (a..<b) defines a range that runs from a to b, but doesn’t include b

```swift
for index in 1..<5 {
    print("At \(index)") // prints 1,2,3,4
}
```

### One-Sided Range
```swift
let myArray = ["1", "2", "3", "4", "5"]

for item in  myArray[2...]{
    print(item)  // prints 3,4,5
}


for item in myArray[...2] {
    print(item) // prints 1,2,3
}
```


### init , failable init, convenience init. <a name="init"/>

```swift

// Init 

struct Person {
    let name: String
    let age: Int
    let designation: String
}

let person = Person(name: "Perosn", age: 30, designation: "iOS Engineer")

struct Person2 {
    let name: String
    let age: Int
    let designation: String
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
        designation = ""
    }
}

let person2 = Person2(name: "Perosn 2", age: 30)
```


```swift

// failable init

struct Item {
    let name: String
    let price: Int
    let quantity: Int
    
    init?(name: String, price: Int, quantity: Int) {
        
        guard quantity < 0 else {
            return nil
        }
        
        self.name = name
        self.price = price
        self.quantity = quantity
    }
}
```


```swift
// convenience init

class Item2 {
    var name: String
    var price: Int
    var quantity: Int
    
    init(name: String, price: Int, quantity: Int) {
        self.name = name
        self.price = price
        self.quantity = quantity
    }
//
    init(name : String, price: Int) {
        self.name = name
        self.price = price
        self.quantity = 1
    }
    
    convenience init(name: String) {
        self.init(name: name, price : 1, quantity : 1)
    }
}

let item2 = Item2(name: "", price: 2)
```

### Lazy <a name="lazy"/>

```swift
// lazy

struct Calculate {
    static func havyCalculation() -> Int{
        var mArray = [Int]()
        
        for item in 1...4000 {
            mArray.append(item)
        }
        
        return mArray.last!
    }
}

struct Person{
    let name: String
    let age: Int
    lazy var calculation = {
       return Calculate.havyCalculation()
    }()
    
    lazy var showPerson = {
        return "Name is \(name) , Age is \(age)"
    }()
}

var person = Person(name: "EA Rashel", age: 100)
print(person.showPerson) // Name is EA Rashel , Age is 100
print(person.calculation) // it will calculate first time and store it
print(person.calculation) // this time is will return the stored value
```

### Computed property <a name="computedproperty"/>

> Computed property are those which value are not set directly. We set the value by some calculation

```swift
struct Person{
    var monthlySalary: Int
    var yearlySalary : Int {
        get {
            return monthlySalary * 12
        }
    }
}

let person = Person(monthlySalary: 1000)
person.yearlySalary // this will return 120000

```

*** Computed property will calculate every time it is called. where as lazy property will calculate first time and store the value and return the value if called 2nd time. 

*** Lazy is not thread safe


### Property Wrapper <a name="propertywrapper"/>


```swift
extension String {
    
    func isValidEmail()-> Bool {
        let emailRegEx = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"

         let emailPred = NSPredicate(format:"SELF MATCHES %@", emailRegEx)
         return emailPred.evaluate(with: self)
    }
}


@propertyWrapper
struct EmailValidationWrapper {
    
    var value: String
    var wrappedValue: String {
       
        get{
            return isValidEmail(email: value) ? value : String()
        }
        
        set{
            value = newValue
        }
    }
    
    
    init(emailValue: String) {
        value = emailValue
    }
    
    
    private func isValidEmail(email: String) -> Bool {
        let emailRegEx = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"

         let emailPred = NSPredicate(format:"SELF MATCHES %@", emailRegEx)
         return emailPred.evaluate(with: email)
    }
}


struct User {
    
    var name: String
    @EmailValidationWrapper var email: String
    
    
   private func validate() -> Bool {
        if name.isEmpty || email.isEmpty {
            return false
        }
        
        return true
    }
    
    
    func register() {
        
        if validate() {
            print("done register")
        }else {
            print("Error")
        }
    }
}


let user = User(name: "test", email: EmailValidationWrapper(emailValue: "test@gmail.com"))


// another example
@propertyWrapper
class Prefixer{
    
    var wrappedValue: Int
    var prefixer: String?
    var projectedValue: String {
        guard let prefixer = prefixer else {
            return"\(wrappedValue)"
        }
        
        return "\(prefixer)\(wrappedValue)"
    }
    
    init(prefixer: String? = nil ,defaultValue: Int = 0) {
        self.wrappedValue = defaultValue
        self.prefixer = prefixer
    }
}

class Product{
    var name: String
    @Prefixer(prefixer: "$") var price: Int
    
    init(name: String, price: Int){
        self.name = name
        self.price = price
    }
}

let item = Product(name: "Book", price: 22)

print(item.$price) // $price will return the projectedValue && price will return the wrappedValue



// use in userdefaults

@propertyWrapper
class UserSettings<T>{
    
    var defaults = UserDefaults.standard
    
    var wrappedValue: T {
        set { defaults.set(newValue, forKey: key) }
        get { defaults.value(forKey: key) as? T ?? defaultValue }
    }
    var defaultValue: T
    var key: String
    init(_ key: String,_ defaultValue: T) {
        self.key = key
        self.defaultValue = defaultValue
    }
}

struct Settings {
    @UserSettings("language", "en") var language: String
}

let settings  = Settings()
print(settings.language)  // en
settings.language = "bn"
print(settings.language) // bn
```


### Associatedtype <a name="associatedtype"/>
```swift
struct Movie{
    let name:String
}

struct User {
    let firstname:String
}


protocol WebService: class {
    
    associatedtype Model
    
    func getAll(url: URL, completion: (Result<Model, Error>) -> Void)
    
}


class MovieService: WebService {
    typealias Model = Movie
    
    func getAll(url: URL, completion: (Result<Movie, Error>) -> Void) {
        
    }
}


class UserService: WebService {
    
    func getAll(url: URL, completion: (Result<User, Error>) -> Void) {
        
    }
}
```


### Protocol Extention <a name="protocolextention"/>

```swift
protocol Styling{
    func changeColor()
}

extension Styling {
    func changeColor() {
        print("Changed color to WHITE")
    }
}

class MyButton: Styling  {
    
}

class MyTextField: Styling {
    func changeColor() {
        print("Changed color to RED")
    }
}

let button = MyButton()
button.changeColor() // will print default value WHITE
let textField = MyTextField()
textField.changeColor() // will print overridien value RED
```

### Generic Protocol with associatetype 

```swift 
protocol MyGenericProtocol{
    associatedtype InputType
    associatedtype ReturnType
    
    func multiply(value: InputType) -> ReturnType
}

class Test1: MyGenericProtocol {
    
    typealias InputType = Int
    typealias ReturnType = Int
    
    func multiply(value: Int) -> Int {
        return value * value
    }
}

class Test2: MyGenericProtocol {
    func multiply(value: Int) -> Int {
        return value * value
    }
}
```

### Higher Order Function <a name="higherorderfunction"/>

#### Map & CompactMap
```swift
struct User{
    let name: String
    let age: Int
}

let users = [
    User(name: "User 1", age: 20),
    User(name: "User 2", age: 30),
    User(name: "User 3", age: 40)
]

//let userNames = users.map { (user) -> String in
//    return user.name
//}

let userNames = users.map { $0.name }


print(userNames)

let values = ["1", "two", "3", "four", "5"]

let intValues = values.map{ Int($0) } // accept nil value
print(intValues)
let intValuesWithoutNil = values.compactMap{ Int($0) } // dont accept nil value
print(intValuesWithoutNil)
```


### Closure <a name="closure"/>
```swift
// closure
func addition(num1: Int, num2: Int) -> Int{
    return num1 + num2
}

var addTwoNumber = addition


let add = addition(num1: 10, num2: 30)
print(add)

let add1 = addTwoNumber(10,30)
print(add1)


var addTwoNumber2: (Int, Int) -> Int = {
    (num1, num2) in
    return num1 + num2
}

let add2 = addTwoNumber2(20,20)
print(add2)

var addTwoNumber3: (Int, Int) -> Int = {
    return $0 + $1
}

let add3 = addTwoNumber3(20,20)
print(add3)
```

### Function Call Sequence in closure 

```swift 
// function call sequence in closure

func printGoodMorningMessage(isMorning: Bool, name: String){
    
    if isMorning {
        print("Good Morning \(name)")
    }
}

//printGoodMorningMessage(isMorning: true, name: "EA Rashel") // this will print Good morning message
//printGoodMorningMessage(isMorning: false, name: "Mark Zugarburg") // this will print nothing

func assignName(name: String) -> String {
    print("assign name \(name)")
    return name
}

//printGoodMorningMessage(isMorning: true, name: assignName(name: "EA Rashel 2"))
//printGoodMorningMessage(isMorning: false, name: assignName(name: "Mark Zugarburg 2"))

/**
 this is the output for above code
 
 assign name EA Rashel 2
 Good Morning EA Rashel 2
 assign name Mark Zugarburg 2
 */

func printGoodMorningMessage2(isMorning : Bool, name: () -> String ){
    
    if isMorning {
        print("Good Morning 2 \(name())")
    }
}


printGoodMorningMessage2(isMorning: true) { () -> String in
    return assignName(name: "EA Rashel 3")
}


printGoodMorningMessage2(isMorning: false) { () -> String in
    return assignName(name: "Mark Zugarburg 3")
}

/**
 this is the output for above code
 
 assign name EA Rashel 3
 Good Morning EA Rashel 3
 */
```

### Mutating Function <a name="mutating"/>

```swift
class Test{
    
    var items = [Int]()
    
    func push(value: Int) {
        items.append(value)
    }
    
    func pop() -> Int? {
        if !items.isEmpty {
            return items.removeLast()
        }
        return nil
    }
}

// struct values are immutable by default
// to chnage its value we need to make it mutable first
struct TestMutating {
    
    var items = [Int]()
    
    mutating func push(value: Int) {
        items.append(value)
    }
    
    mutating func pop () -> Int? {
        
        if !items.isEmpty {
            return items.removeLast()
        }
        
        return nil
    }
}

var mutate = TestMutating(items: [1,2,3,4])
mutate.pop()
mutate.push(value: 5)
```

### where keyword <a name="where"/>

```swift
// where
// where is a powerful keyword in swift to easily filter out value.
// usage of where

// in enums

enum Action{
    case createUser(age: Int)
    case createPost
}

func createAction(action: Action) {
    switch action {
    case .createUser(let age) where age < 30:
        print("User is young")
    case .createUser:
        print("User is aged")
    default:
        print("Default")

    }
}

createAction(action: .createUser(age: 20)) // User is young
createAction(action: .createUser(age: 40)) // User is aged

// in for-loop. printing even numbers

let items = [0,1,2,3,4,5,6,7,8,9,10]

for item in items where item % 2 == 0 {
    print(item) // 0 ,2, 4, 6, 8, 10
}


// in protocol extention

extension Array where Element == Int {
    func printAvgNumber() {
        let total = reduce(0, +)
        let avg  = total / count
        print("Avg is : \(avg)")
    }
}

items.printAvgNumber() // 5

// in first

let names = ["john", "adam", "jerry", "alan"]

let firstAName = names.first { (name) -> Bool in
    return name.first == "a"
}

print(firstAName) // adam

let cehckIfAdamExists = names.contains (where: { (name) -> Bool in
    return name == "adam"
})

print(cehckIfAdamExists) // true
```

### Error handaling <a name="error"/>

```swift
enum CustomError: Error {
    case NameFieldEmpty(msg: String)
    case PhoneFieldEmpty(msg: String)
    case TestError
}

func createUser(name: String, phone: String) throws {
    if name.isEmpty {
        throw CustomError.NameFieldEmpty(msg: "Name field is empty")
    }
    
    if phone.isEmpty{
        throw CustomError.PhoneFieldEmpty(msg: "Phone Field is empty")
    }
    
    print("User Created. Name \(name)")
}

do {
    try createUser(name: "asda", phone: "asdasd")
}catch CustomError.NameFieldEmpty(let msg) {
    print(msg)
}catch CustomError.PhoneFieldEmpty(let msg) {
    print(msg)
} catch is CustomError{
    print("Other error")
} catch (_) {
    print("No Custom Error.")
}

```


### Useful Links 
1. [Swift Documentation](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html)
2. [Code Cat Swift tutorial](https://www.youtube.com/watch?v=0HPOxthY6q0&list=PLb5R4QC2DtFv3MvfG42Cd5La34Hwj4pY6&index=1)
3. [Best Resources for Advanced iOS developer](https://medium.com/flawless-app-stories/best-resources-for-advanced-ios-developer-swift-ade30374593d)
4. [Avander lee (swiftlee)](https://www.avanderlee.com/category/swift/)
