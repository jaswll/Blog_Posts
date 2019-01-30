## Programming 101, Efficient versus Inefficient Coding Structure in Python

by Jason Leung



As an intermediate or beginning level programmer, we are still learning the ins and outs and best practices of our 1st, 2nd or 3rd programming language. But no matter what the language, one focus should be learning and practicing efficient code. What does efficient in this context mean? Here are 3 general principles/metrics that add up to what it might mean to be efficient, and a concrete coding challenge in Python as an example. 

1. Code should be simple and concise. Every word of the code should be necessary and as much as possible straightforward, both in control flow and in looping behavior/iterations.
2. Code should be readable/interpretable. This sometimes clashes with principle one as compressing code often makes shorthand tricks and cool functions harder for a reader to follow. A reader with basic knowledge of the programming language should be able to follow the flow and purpose of each section of code.
3. One core feature of code in general is its reusability, both between parts of a project and across a programming career. Whenever you can reuse functions, sections and lines of code, which you've already made more efficient, go out of your way to do so! In the long run, it will usually save you time and make the overall code simpler and more readable. Make copy and paste your best friend.



The challenge is from https://www.codewars.com/kata/calculator, thank you to its author [obrok](https://www.codewars.com/users/obrok), and has this prompt:

```
Challenge: Create a simple calculator that given a string of operators (+ - * and /)
and numbers separated by spaces returns the value of that expression
```

Here's how these three efficiency principles apply to the relatively straightforward Calculator challenge. This problem is purposefully simple so it is easier to follow efficiency practices start to finish.  

The main function is short and sweet. It takes advantage of the python string function .split(), which splits the input string by a given separator, here it is the space character (' '), and returns a list of each separated substring. This function cuts out unnecessary work, where you might have to process the original string one character at a time. It seems both efficient and readable. We then change all digits from string type to float. The formatNum function included at end of article just simplifies this mapping by excluding all operations from this, which cannot be transformed to floats. This list comprehension is a good example of concise code sacrificing some readability. It's harder for us to parse the order within list/dict comprehension: of variable assignment, breaking down the structure of the list-like object being assigned from, and especially any conditionals which for Python are at the end of the list. We re-assign the output to lis, because we no longer need the string version and it also makes the code more concise.

```python
def calculator(string) :
    lis = string.split(' ')
    lis = [formatNum(x) for x in lis]
    while len(lis) > 1 : lis = NextOperation(lis)
    return lis[0]
```

The main loop is straightfoward. NextOperation performs one operation at a time, finding the right operation to do next, and returns a new list of the same structure with the result of that operation inserted in place of that operation and its two numerical inputs. Because this function always reduces the length of lis by two for every loop, we can use a while loop and run until the lis has been reduced to a length of one, which is returned as the final answer.

It is helpful when looking at efficiency to scheme up the desired output at each stage of our code. Here it is the desired returned list for each call of NextOperation, and a simple example is given below. Now we can optimize our code to deal with just the range of inputs that are possible and return desired output for each iteration.

```python
test1 = '2 + 3 * 4 / 6'

Every list returned by NextOperation:
[2.0, '+', 3.0, '*', 4.0, '/', 6.0]
[2.0, '+', 12.0, '/', 6.0]
[2.0, '+', 2.0]
[4.0]
```



NextOperation is the meat and potatoes of this challenge. It needs to determine the correct next operation to perform, then find and use the two numbers to the left and right of the operation to get its result, and finally output the next list to be iterated on. In practice, there are definitely ways to do this in less lines of code. However, I think it is most interpretable and simple to list out the conditional in which each of the four operations are performed. The correct next operation will always be division or multiplication until those are exhausted so these conditionals are checked first. Variable names are important here in terms of efficiency. While shorter names for index_div etc. would make code more concise, readability and being able to easily follow which index belongs to each operation is an acceptable trade-off. 

If the next division is earlier in the list than the next multiplication, division should be performed and vice versa. In each if statement, we use the handy list function .pop() which takes an index value and both removes that index's item and returns it, efficiently accomplishing both retrieving the 2 numbers for the actual division and starts to reduce the output list. The nextindex function uses the list function .index() to step through  the list starting with its first element and stop and return the first index with the specified value. In terms of computational complexity, this is good because it only does the minimum iterations (not counting skipping elements that are numbers) necessary for us to get the next operation and be readable. The one hidden action here is that the index with the division string element is also actually replaced by assigning to ( lis[index_div-1] ). It is now at index minus 1 because the element 'behind' it was removed by .pop(). This is not readable but is very concise, the result of the division is added in place of the division sign and this gives this iteration's desired output list.

```python
def nextOperation(lis) :
    index_div = nextindex(lis,'/')
    index_mult = nextindex(lis,'*')
    if index_div < index_mult :
        lis[index_div-1] = lis.pop(index_div-1) / lis.pop(index_div)
    elif index_mult < index_div :
        lis[index_mult-1] = lis.pop(index_mult-1) * lis.pop(index_mult)
    else : 
        index_subtract = nextindex(lis,'-')
        index_add = nextindex(lis,'+')
        if index_subtract < index_add :
            lis[index_subtract-1] = lis.pop(index_subtract-1) - lis.pop(index_subtract)
        elif index_add < index_subtract :
            lis[index_add-1] = lis.pop(index_add-1) + lis.pop(index_add)
        else : print('format error')
    return lis

def nextIndex(lis, operation) :
    try : return lis.index(operation)
    except : return len(lis)
```

Lastly, if there are no division/multiplication left we perform the first addition/subtraction, with the exact same structure. Luckily for this challenge, there are no exceptions or error cases that need to be coded up because our range of possible input strings is limited. If there is a division by zero or badly formatted input string nextOperation will just default to printing 'format error'.

<br></br>

<br></br>

And, that's a wrap. The biggest theme in this code implementation is that in general you also need to weigh your own trade-offs, between code simplicity, readability, and reusability and adaptability. The operation with two .pop() elements assigned back to the original list is a quite adaptable structure but formatNum can be very specific to match the constraints of the challenge. Naming convention and choice to maximize readability is important and taking advantage of built-in functions, for lists, strings etc., is essential to making your code have the right blend of efficiency. In addition, a good base structure to main functions such as calculator() and nextOperation() allows you to extend its functionality later on to include exception cases or new types of inputs (such as exponents or even parentheses here).

We don't touch on computational complexity in this article because we're focusing on an intermediate to beginners programming level. But for bigger data sets, optimizing run time efficiency is also important. In order to optimize runtime, minimizing the number and nesting of loops, it is often necessary to add code that is less interpretable and concise. 

Finally, I encourage you to keep going back to add customizability to functions. This both makes code less simple and less readable, but it greatly increases reusability and utility. We can keep a majority of a code section and adapt it to somewhat new purposes. This is a big strength within Python, where we can use its many modules and customize them for our own purposes everyday. Overall, remember, efficiency is a process. Through practice, you can iteratively get better at reviewing your code and implementing efficient structures.



<i>Secondary Code and Testing:</i>

```
def formatNum(s):
    try: return float(s)
    except: return s
```

```python
test = '2 - 3 * 4 - 6 * 2 / 2 + 3 * 4 - 6'
calculator(test)

Every list returned by NextOperation:
[2.0, '-', 12.0, '-', 6.0, '*', 2.0, '/', 2.0, '+', 3.0, '*', 4.0, '-', 6.0]
[2.0, '-', 12.0, '-', 12.0, '/', 2.0, '+', 3.0, '*', 4.0, '-', 6.0]
[2.0, '-', 12.0, '-', 6.0, '+', 3.0, '*', 4.0, '-', 6.0]
[2.0, '-', 12.0, '-', 6.0, '+', 12.0, '-', 6.0]
[-10.0, '-', 6.0, '+', 12.0, '-', 6.0]
[-16.0, '+', 12.0, '-', 6.0]
[-4.0, '-', 6.0]
[-10.0]
```

