## 3. BAD SMELLS IN CODE

### 1. Mysterious name
Name should clearly communicate what they do and how to use them

### 2. Duplicated code
Same code structure in more than one place

### 3. Long function
the longer a function is, the more difficult it is to understand

### 4. Long parameter list
Difficult to understand and easily introduce bug

### 5. Global data
Global variable is difficult to track and debug

### 6. Mutable data
Changes to data can often lead to unexpected consequences and tricky bugs

### 7. Divergent change
One module is changed in different ways for different reasons

### 8. Shotgun surgery
When every time you make a change, you have to make a lot of little edits to a lot of different classes

### 9. Feature envy
When a function in one module spends more time communicating with functions or data inside another module than it does within its own module

### 10. Data clumps
Same three or four data items together in lots of places

### 11. Primittive Obsession
Use primitive types instead of custom fundamental types

### 12. Repeated switches
Same conditional switching logic pops up in different places

### 13. Loops
Using loops instead of first-class functions such as filter or map

### 14. Lazy element
A class, struct or function that isn't doing enough to pay for itself should be eliminated.

### 15. Speculative generality
All sorts of hooks and special cases to handle things that aren’t required

### 16. Temporary field
An instance variable that is set only in certain circumstances.

### 17. Message chains
Ưhen a client asks one object for another object, which the client then asks for yet another object...

### 18. Middle man
When an object delegates much of its functionality.

### 19. Insider trading
Modules that whisper to each other by the coffee machine need to be separated by using Move Function and Move Field to reduce the need to chat.

### 20. Large class
A class is trying to do too much, it often shows up as too many fields

### 21. Alternative Classes with Different Interfaces
Classes with methods that look to similar.

### 22. Data class
Classes that have fields, getting and setting methods for the fields, and nothing else

### 23. Refused Bequest
Subclasses doesn't make uses of parents method

### 24. Comment
The comments are there because the code is bad

## 6. MOST COMMON SET OF REFACTORING

### 1. Extract Function
Extract fragment of code into its own function named after its purpose.

```javascript
function printOwing(invoice) {
  printBanner()
  let outstannding = calculateOutstanding()

  // print details
  console.log(`name: ${invoice.customer}`)
  console.log(`amount: ${outstanding}`)
}
```

to

```javascript
function printOwing(invoice) {
  printBanner()
  let outstannding = calculateOutstanding()
  printDetails(outstanding)

  function printDetails(outstanding) {
    console.log(`name: ${invoice.customer}`)
    console.log(`amount: ${outstanding}`)
  }
}
```

**Motivation**:
- You know what's the code doing without reading the details
- Short function is easier to read
- Reduce comment

### 2. Inline Function
Get rid of the function when the body of the code is just as clear as the name

```javascript
function rating(aDriver) {
  return moreThanFiveLateDeliveries(aDriver) ? 2 : 1
}
function moreThanFiveLateDeliveries(aDriver) {
  return aDriver.numberOfLateDeliveries > 5
}
```

to

```javascript
function rating(aDriver) {
  return aDriver.numberOfLateDeliveries > 5 ? 2 : 1
}
```

**Motivation**
- When Indirection is needless (simple delegation) becomes irritating.
- If group of methods are badly factored and grouping them makes it clearer

### 3. Extract Variable
Add a name to an expression

```javascript
//price is base price ­ quantity discount + shipping
return order.quantity * order.itemPrice -
  Math.max(0, order.quantity - 500) * order.itemPrice * 0.05 +
  Math.min(order.quantity * order.itemPrice * 0.1, 100)
```

to

```javascript
const basePrice = order.quantity * order.itemPrice
const quantityDiscount = Math.max(0, order.quantity ­ 500) * order.itemP
const shipping = Math.min(basePrice * 0.1, 100)
return basePrice ­- quantityDiscount + shipping;
```

**Motivation**
- Break down and name a part of a more complex piece of logic
- Easier for debugging

### 4. Inline Variable
Remove variable which doesn't really communicate more than the expression itself.

```javascript
let basePrice = anOrder.basePrice
return (basePrice > 1000)
```

to

```javascript
return anOrder.basePrice > 1000
```

### 5. Change Function Declaration
Rename a function, change list of parameters

```javascript
function circum(radius) {...}
```

to

```javascript
function circumference(radius) {...}
```

**Motivation**
- Easier to understand
- Easier to reuse, sometime better encapsulation

### 6. Encapsulate Variable
Encapsulat a reference to some data structure

```javascript
let defaultOwner = {firstName: "Martin", lastName: "Fowler"}
```

to

```javascript
// defaultOwner.js
let defaultOwnerData = {firstName: "Martin", lastName: "Fowler"}
export function defaultOwner() { return defaultOwnerData }
export function setDefaultOwner(arg) { defaultOwnerData = arg }
```

**Motivation**
- Provide a clear point to monitor changes and use of the data, like validation.

### 7. Rename Variable
Make shared variable's name can self-explain

```javascript
let a = height * width
```

to

```javascript
let area = height * width
```

### 8. Introduce Parameter Object
Replace groups of data items that regularly travel together with a single data structure

```javascript
function amountInvoiced(startDate, endDate) {}
function amountReceived(startDate, endDate) {}
function amountOverdue(startDate, endDate) {}
```

to

```javascript
function amountInvoiced(aDateRange) {}
function amountReceived(aDateRange) {}
function amountOverdue(aDateRange) {}
```

**Motivation**
- Make explicit the relationship between the data items
- Reduce the size of parameter list
- Make code more consistent
- Enable deeper changes to the code

### 9. Combine Functions Into Class
Form a class base on group of functions that operate closely on a common data

```javascript
function base(aReading) {}
function taxableCharge(aReading) {}
function calculateBaseCharge(aReading) {}
```

to

```javascript
class Reading() {
  base() {}
  taxableCharge() {}
  calculateBaseCharge() {}
}
```

**Motivation**
- Simplify function call by removing many arguments
- Easier to pass object to other parts of the system

### 10. Combine Functions Into Transform
Takes the source data as input and calculates all the derivations, putting each derived value as a field in the output data

```javascript
function base(aReading) {}
function taxableCharge(aReading) {}
```

to

```javascript
function enrichReading(argReading) {
  const aReading = _.cloneDeep(argReading)
  aReading.baseCharge = base(aReading)
  aReading.taxableCharge = taxableCharge(aReading)
  return aReading
}
```

**Motivation**
- Avoid duplication of logic

### 11. Split Phase
Split code which do different things into separate modules

```javascript
const orderData = orderString.split(/\s+/)
const productPrice = priceList[orderData[0].split("-")[1]]
const orderPrice = parseInt(orderData[1]) * productPrice
```

to

```javascript
const orderRecord = parseOrder(orderString)
const orderPrice = price(orderRecord, priceList)

function parseOrder(aString) {
  const values = aString.split(/\s+/)
  return {
    productID: values[0].split("-")[1],
    quantity: parseInt(values[1])
  }
}
function price(order, priceList) {
  return order.quantity * priceList[order.productID]
}
```

**Motivation**
- Make the different explicit, revealing the different in the code
- Be able to deal with each module separately
## 7. ENCAPSULATION