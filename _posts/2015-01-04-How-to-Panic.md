---
layout: post
title: How to Panic
---

In Go, there is an aptly named function called panic. When a panic function is called, the program will give up on all remaining instructions, run backwards, and crash the program. At first, this seems like a strange and not at all useful function. However, just as with regular human panicking, there are scary and unpredictable things one encounters and when you do not know how to proceed the only option is to abandon the task at hand and flee. Go gives your program the ability to do the same thing.   
My experience with Go has been solely with mathematical libraries, so the big scary thing I encounter which would cause a panic is division by zero. If somehow in an equation or algorithm, you (or your program) accidentally find yourself with zero as a denominator, you know something has gone horribly wrong. This should never happen and there is absolutely no sensible way to proceed with the remainder of your instructions. You can only give up, go back, and try again.   
When I wrote a division function, I followed suit and included a panic function which would be called if input variable for the denominator was zero.   
`func (numerator *Float) Div(denominator *Float) *Float {`  
`    if denominator == 0 {`  
`        panic("division by zero is undefined")`  
`    }`  
`    ...`  
When the panic function is called, you can/should/must give it a little error message. I will address this later.  
I also had to write a test for the division function to make sure that it does in fact panic when it should. This raised a strange question, "how do I make sure the function crashes correctly?" I never thought I would ask that.  
The panic function is not an uncontrolled panic. It is possible for the program to begin panicking, then calm down and carry on. The test tries to divide by zero, initiating a panic, catching it, and proceeds with the remainder of the tests. To understand how to panic and recover, we first have to understand a function called defer.  
I learned defer in the context of panic, so my initial understanding of the defer function was sort of like Holden Caulfield’s idea of a catcher in the rye, a guardian who protects children running in the rye from accidentally running off of a cliff. This is incorrect. The defer function has many purposes beyond catching runaway programs.  
Let’s do metaphors for a sec. Let’s say you go to a friends house and she says "don’t forget to lock the door when you leave". She does not expect you to lock the door right now, but you must remember to later. Actually she will stand by the door and make sure you lock up. She won’t even let you leave until you have locked up. When you go into the library and take a book off the shelf, her roommate says "don't forget to put the book back before you leave". Again, he does not expect you to put it back before you have read it, but he wont let you leave the library unless the book is back where it belongs. These reminders are defers, an instruction that must be dealt with in the future before you leave.  
  
When a defer is called within a function f(), the defer instructions will be executed when f() returns. You can return from a function in many different ways, but every defer you encounter will be dealt with regardless of how you exit the function f().  
Let’s talk about panic again. When a function triggers a panic, the function will not complete the remainder of its tasks, but it will return to the function that called it and in returning it will have to deal with the defer statements because even while panicking the defer function still gets called. This means you can add a "calm down" function within your defer to end the panic, which is called recover. The syntax the Go library uses to call recover is irregular and confused me at first.  
`defer func() {`   
`        if r := recover(); r != nil {`  
`            fmt.Println("Recovering from panic", r)`  
`        }`  
`}`  
Remember how I said you can/should/must give the panic function an error message? The recover function will typically return nil, unless the program is panicking in which case it will return the panic error message. I know this first line is a little strange to read but its conceptually straightforward. This first line within the function will set r to the error message. On the same line, the if statement will check if the value is not nil, which just means if there is an error message. The next line prints "Recovering from panic" and the error message from the panic. After that, the program will continue as normal, as though it had never entered the function which caused the panic.  
Now let’s take a look at my code and see how the panic, defer, and recover play together.  
  
Here is what the defer and recover combo looks like in my divide-by-zero test.    
  
`TestFloatDivZero(t *testing.T) {`  
`    x := NewFloat(10.0)`  
`    y := NewFloat(0.0)`  
`    defer func() {`  
`         if r := recover(); r != nil {`  
`             fmt.Printf("Panicking because %v\n", r)`  
`             fmt.Print("TestFloatDivZero recovers from panic\n")`  
`         } else {`  
`             t.FailNow()`  
`         }`  
`     }()`  
`     x.Div(y)`  
`}`  
  
The first two lines in the test just assign a value to x and y. Next you can see the defer function, which as I said, must be above the function which induces a panic, so skip down to the next line that is not within defer. This x.Div(y) just means divide x by y. Since division by zero is undefined, the program will begin panicking and exit the div function and attempt to exit the TestFloatDivZero function, but it must deal with the defer function now.  
In the defer function we can see the standard way to return from the recover statement will cause some print statements and then will continue on with the rest of the program when it exits TestFloatDivZero().  
However, if the div function returns or gives a number (or does anything other than panic) there is no error statement, so the if statement won’t pass, and the else will be called instead. In the else, we have the standard test fail function, which will end the test, and print the name of the test that failed to the terminal. There you have it, a test that passes if dividing by zero fails and fails if dividing by zero returns a number.  
As you can see there is a good reason to panic, and a built in way to deal with a panicking program. If you want to know how to solve this type of problem in another language, try googling the words 'try' and 'exception'. I have not worked with these, but I have been informed by intelligent people that those are the names of the functions that solve this problem in other languages.  
