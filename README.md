# SwiftLearning
New things i learn about swift programming language




### Toupls
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


### New way of handling getter setter

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


### Closed Range Operator
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

### init , failable init, convenience init

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

### Computed property

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


### Property Wrapper


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

### associatedtype
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

### Protocol Extention

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


### Useful Links
1. [Swift Documentation](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html)
2. [Code Cat Swift tutorial](https://www.youtube.com/watch?v=0HPOxthY6q0&list=PLb5R4QC2DtFv3MvfG42Cd5La34Hwj4pY6&index=1)
3. [Best Resources for Advanced iOS developer](https://medium.com/flawless-app-stories/best-resources-for-advanced-ios-developer-swift-ade30374593d)
