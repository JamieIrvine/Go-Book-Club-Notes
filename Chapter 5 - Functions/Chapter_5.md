# Chapter 5 - Functions

## Declaring and Writing Functions

Go uses First-class functions, like many other programming languages. 
- Go has methods too, but we won't cover them until chapter 7.

Go adds its own twist on function features - some are improvements and others should be avoided.

### Parameters

* Go is a typed language, so you must specify the types of the parameters
- If a function returns a value, you must supply a `return`

```
// A function that takes two int parameters, adds them together,
// and returns the result

func add(a int, b int) int {
    return a + b
}
```

```
// Parameters of the same type can be shortened:

func add(a, b int) int {	
	return a + b
}
```

If a function has no input parameters, use `()` - an empty parenthesis

```
func main() {
    fmt.Println("A main function")
}
```

### Return Statements

- If a function returns nothing, a `return` statement is not needed
	- But a `return` can be used to exit the function before you reach the last line

```
// Print a string, but only if it isn't empty.
// If it is empty, exit the function before printing anything.

func printAThing(thing string) {	
	if thing == "" {
		return
	}
	fmt.Println(thing)
}
```

### Calling Functions

```
func main() {
    fmt.Println("A main function") // nothing returned
    
	result := add(1, 2)            // assign the result of add(1, 2) to result
}
```


## Simulating Named and Optional Parameters

Go doesn't have named and optional parameters - you must supply all parameters for a function
- You can emulate named and optional parameters by defining a struct and passing it to function.

```
type PersonParams struct {  
    Name string  
    Age int  
}  
  
func printInfo(params PersonParams) {  
    fmt.Printf("Name: %s, Age: %d", params.Name, params.Age)  
}  
  
func main() {  
	// Still works even though we aren't passing in the age
	// If a field isn't defined in the struct, it will default to the zero value.
	// For Age (an int), this will be 0.

    printInfo(  
       PersonParams{  
          Name: "John", 
       })}
```

Not having named and optional parameters shouldn't be a bad thing.
- If a function has too many inputs then it is probably too complicated.


## Variadic Input Parameters and Slices

- Pass any number of parameters of a particular type into a function
- Must be the last (or only) parameter
- Indicated by `...`
- Variadic parameter is converted to a slice

```
func add(a ...int) int {  
    var result in

	// Converted to a slice so we can iterate through the inputs
    for _, v := range a {  
       result += v  
    }  
  
    return result  
}

func main() {  
    fmt.Println(add(1))               // 1  
    fmt.Println(add(1, 2, 3))         // 6  
    fmt.Println(add(1, 2, 3, 4, 5))   // 15  
}
```

You can also provide a slice instead of listing the inputs
- You must follow the slice declaration or variable with `...`

```
func main() {  
    input := []int{1, 2, 3, 4, 5}  
    
    fmt.Println(add(input...))                  // 15  
    fmt.Println(add([]int{1, 2, 3, 4, 5}...))   // 15  
}
```

## Returning Multiple Values

* If a function returns multiple values, you must return them all
* They should be inside brackets, e.g. `(int, int)`

```
func sort(a, b int) (int, int) {  
    if a < b {  
       return a, b  
    }  
    return b, a  
}  
  
func main() {  
    smallest, biggest := sort(2, 1)  
    fmt.Printf("%d, %d", smallest, biggest) // 1, 2  
}
```

### Returning an Error

- You can return an error if something goes wrong within a function
- If the function completes successfully, we should return `nil` for the error
- The error should always be the last (or only) return value

```
// We return an error here if the result is a negative number
func add(a, b int) (int, error) {  

    result := a + b  
    
    if result < 0 {  
       return 0, fmt.Errorf("result is negative: %d", result)  
    }    
    
    return result, nil  
}  
  
func main() {  
	
    result, err := add(1, 2)  

	// Always check the error after calling the function
    if err != nil {  
       fmt.Println(err)  
       os.Exit(1)  
    }    
    
    fmt.Println(result)  
}
```


### Ignoring Returned Values

Use `_` to ignore a returned value

```
func main() {  
	
    _, err := add(1, 2)  

    if err != nil {  
       fmt.Println(err)
    }    
    
    fmt.Println("Function completed successfully")  
}
```

You can also ignore all returned values
- `fmt.Println` actually returns two values, but are mostly ignored
- Most of the time, you should explicitly use `_` to show that you are ignoring returned values

```
func main() {  
	// Ignore all returned values
    add(1, 2)  
}
```

### Named Return Values

- Naming return values actually pre-declares them
- They're instantiated to their zero values
- The value names are local to the function - they won't be in scope outside of it

```
func add(a, b int) (result int, err error) {  

    result := a + b  
    
    if result < 0 {  
		err = errors.New("result is negative: %d", result)
		return result, err
    }    
    
    return result, err  
}  
  
func main() {  
	
    result, err := add(1, 2)  

    if err != nil {  
       fmt.Println(err)  
       os.Exit(1)  
    }    
    
    fmt.Println(result)  
}
```

Issues with named return values:
- Shadowing - you might accidentally be assigning a value to a variable of the same name in the outer scope
- You don't have to return them - you could return different variables as long as they're the same type

```
func add(a, b int) (result int, err error) {  
    result = a + b  
  
    if result < 0 {  
       err = fmt.Errorf("result is negative: %d", result)  
       return result, err  
    }  

	// Not returning `result` or `err` here. This is bad!
    return 10, nil  
}
```

- When using named return values, you should always return the actual named values.
- Author considers them to be of limited value as shadowing them and ignoring them can cause confusion. 
- They are essential for `defer` statements - but we'll get to this later in the book

### Blank/Naked Returns

Use a `return` statement on its own to automatically return the named values.

```
func add(a, b int) (result int, err error) {  

    if a == 0 {  
       err = fmt.Errorf("a cannot be 0")  
       return
    }  

	result = a + b  
    return
}
```

- In this case, when `a` is `0`, we will set the error and return. 
- `result` was never set to it will return the zero value of `0`

Problems with blank returns 
- They make it hard to understand the flow of the data
- Good software should be clear and readable. Blank returns can make code difficult to read, particularly in long functions - it's not always easy to see what values should be assigned to each named return value.
- Author recommends *never* using blank returns.


### Functions are Values

- A function's type signature is the combination of the `func` keyword + the input parameter types + the return types
- Multiple functions can have the same signature as each other as long as these conditions are all identical

`func add(a int, b int) int { return a + b }`

has the same type signature as 

`func subtract(a int, b int) int { return a - b }`

### Function Type Declarations

The `type` keyword is used to define a struct

```
type Person struct { 
	Name string 
	Age int 
} 
```

But it can also be used to define a `function type`

```
type mathFuncType func(int,int) int
```

Any function with the same type signature as `mathFuncType` will automatically be of that type

```
// type = mathFuncType
func addNums(x, y int) int {
	return x + y 
} 

// type = mathFuncType
func subtractNums(x, y int) int {
	return x - y 
} 

// type != mathFuncType
func addStrings(x, y string) string{ 
	return x + y
}

```

You can then do something like this:

```
func main() {
	var mathFunc mathFuncType

	mathFunc = add
	addResult := mathFunc(1, 2)        // 3

	mathFunc = subtract
	subtractResult := mathFunc(2, 1)   // 1
}
```

### Anonymous Functions

- Anonymous functions are functions defined within another function
- They have no name, but can be assigned to a variable
- If they aren't assigned to a variable, they will run immediately
- Functions declared inside other functions are called *closures*

Some examples:

```
// Calling an anonymous function immediately
func main() {
	input := 3

	func (i int) {
		fmt.Printf("Input: %d", i)
	}(input)
}

// Assigning an anonymous function to a variable
func main() {
	add := func(a, b int) int { 
		return a + b 
	} 
	
	result := add(3, 4) 
	fmt.Println("The sum is:", result)
}

```

Two reasons why you would want to declare an anonymous function without assigning to a variable:
- Launching Goroutines (chapter 10)
- Using a `defer` statement to call the anonymous function when the current function completes

### Passing Functions as Parameters

- Functions are values and you can specify the type of a function using its parameter and return types
- So you can pass functions into other functions as parameters

```
func applyFunction(x int, f func(int) int) int { 
	return f(x) 
} 

func double(x int) int { 
	return x * 2 
} 

func triple(x int) int { 
	return x * 23
} 

func main() { 
	// Call applyFunction with double function as a parameter 
	result := applyFunction(3, double) 
	fmt.Println("Result:", result) // Output: Result: 6 

	// Call applyFunction with triple function as a parameter 
	result := applyFunction(3, triple) 
	fmt.Println("Result:", result) // Output: Result: 9
}
```

- `double` and `triple` have the same signature - `func(int) int` so they can both be passed into `applyFunction`


### Returning Functions from Functions

- Functions can also be returned by other functions

```
func createMultiplier(factor int) func(int) int { 
	func(x int) int { 
		return x * factor 
	} 
} 

func main() { 
	multiplier := createMultiplier(5) 
	
	result := multiplier(4) 
	fmt.Println("Result:", result) // Output: Result: 20 
}
```

- Creating a `createMultiplier` function that returns a function to multiply a number by a particular factor

*A `higher-order function` is a function that has a function for an input parameter or a return value*

### defer

- Attaches code to the end of a function, even if it's called earlier in the function
- The deferred code runs once the surrounding function exits
- Commonly used to clean up file or network connections, etc
- You can have multiple defers in a function
	- The most recent defer runs first after the function exits

The deferred close of a resource should be written just after the resource is created and initial error checking is done:

```
f, err := os.Open(os.Args[1])
if err != nil {
	log.Fatal(err)
}
defer f.Close()

// ... more code ...
```

- If there is an error opening the resource then trying to `Close()` an object that doesn't exist can cause the code to panic
- Therefore we should defer the close of a resource only if there is no error when opening the resource 

#### `defer` runs after the return statement

`defer` runs after the `return` statement so there's no way to use it to modify returned values, *unless* you use named return values in the function.

```
func deferExample() (first int, second int) {
	defer func () {
		first = 10
		second = 20
	}

	first = 15
	second = 30
	return
}
```

This function will return `15` and `20`, but defer will then run and overwrite these values with `10` and `20`.


### Go Is Call By Value

- When you supply a variable for a parameter to a function, Go always makes a copy of the value of the variable

```
func modifyNumber(i int) {
	i = 10
}

func main() {
	i = 20

	modifyNumber(i)

	fmt.Println(i)   // still `20`
}
```

- The same logic doesn't apply to maps and slices
- This is because maps and slices are implemented using pointers (next chapter)
	- You can pass primitive variables to a function by reference using pointers (next chapter)