## What is it?

In algorithms, we generally have 1 major concern when comparing one to another: How long will it take to execute this algorithm?

Big O Notation is used to give an idea of how the execution time grows as the input grows. It has related concepts like Big Omega notation and Big Theta notation but they are less commonly used.

Speaking mathematically, Big O Notation represents the dominant term in the equation determining how many steps an algorithms takes relative to an input n.

### Example - Sort

Let's talk about sorting an array of ints.

A common way of doing this (you'll recognise from the opening chapters of [CLRS](https://en.wikipedia.org/wiki/Introduction_to_Algorithms)) is insertion sort.





```
for(int i = 1; i < myArray.length; i++) { // loops n times where n = length of input array
    for(int j = i-1; j >= 0; j--){ // loops i-1 times for 0 <= i < n
        if(myArray[j+1] < myArray[j]){
            int temp = myArray[j+1];
            myArray[j+1] = myArray[j];
            myArray[j] = temp;
        }
    }
}

```



  
The outer loop runs n times. Each run through of that loop runs i times. So the total number of times the inner loop runs is the SUM(i) for 0 <= i < n. The sum from 1 to n is equal to n(n+1)/2 [[source]](http://betterexplained.com/articles/techniques-for-adding-the-numbers-1-to-100/)  

So all the code inside the first loop runs n times; all the code inside the second loop runs n(n+1)/2 times (for worst case scenario).  

Therefore the total time taken is roughly n + n(n+1)/2\. The dominant term in this equation is a quadratic term (an n^2 term) and therefore the algorithm executes in O(n^2) time.  

### Dominant Terms

The concept of dominant terms is simple. For any function of n, f(n), as n grows, which term/terms dominate the result.

Think of the equation f(x) = 5x + 320.

Given x = 3, f(x) = 335.

335 is pretty close to 320 so the dominant term is 320.

Given x = 2500, f(x) = 12820.

Of the 12820, 12500 is contributed by the 5x term. Over 97% of it. In this case, the dominant term is 5x.

So for the equation f(x) = 5x + 320, we can say that for values of x near 0, the constant dominates. For large values of x, 5x dominates.

If we say f(x) = x^2 + 32x + 10 // x^2 = x to the power 2 = x squared

then the x^2 term will dominate for large values of x, because it grows faster.

Given x = 2500, f(x) = 6,330,010. 

6,250,000 is contributed by the x^2 term.

## Big O Notation

Big O Notation is all about the dominant terms and ballpark figures. It is a tool to rapidly and easily compare one algorithm to another. The most common values for Big O Notation are (in increasing time complexity):



1.  O(1)
2.  O(logn) // where log is log base 2
3.  O(n)
4.  O(nlogn)
5.  O(n^2)
6.  O(2^n)
7.  O(n!)



<div class="separator" style="clear: both; text-align: center;">[![](http://www.daveperrett.com/images/articles/2010-12-07-comp-sci-101-big-o-notation/Time_Complexity.png)](http://www.daveperrett.com/images/articles/2010-12-07-comp-sci-101-big-o-notation/Time_Complexity.png)

### Example - Merge Sort





We looked at an insertion sort algorithm earlier and found that in the worst case scenario, it has O(n^2) complexity. Let's look at merge sort now.

```

public static int[] mergeSort(int[] arr){ // this function is called log(n) times  

if(arr.length <= 1){  

        return arr;  

    }  

    int halfway = ((arr.length - 1) / 2) + 1; // O(1)  

    int[] arr1 = mergeSort(Arrays.copyOfRange(arr, 0, halfway)); // O(n) take first half of array  

    int[] arr2 = mergeSort(Arrays.copyOfRange(arr, halfway, arr.length)); // O(n) take second half of array  

    return merge(arr1, arr2); // O(n)  

}


public static int[] merge(int[] arr1, int[] arr2){  

    int[] result = new int[arr1.length + arr2.length];  

    int i1 = 0;  

int i2 = 0;  

    while(i1 < arr1.length || i2 < arr2.length){ // executes this a + b times  

        if(i1 >= arr1.length){ // each of these if statements in O(1)  

            result[i1 + i2] = arr2[i2];  

            i2 ++;  

            continue; // a control flow statement - it tells the compiler to skip the rest of what's inside the while loop and start the loop again (if the conditions are still met)  

        }  

        if(i2 >= arr2.length){  

            result[i1 + i2] = arr1[i1];  

            i1 ++;  

            continue;  

        }  

        if(arr1[i1] < arr2[i2]){  

            result[i1 + i2] = arr1[i1];  

            i1++;  

            continue;  

        }  

        result[i1 + i2] = arr2[i2];  

        i2++;  

    }  

    return result;  

}


```

This is a recursive function call. This means it calls itself during execution. Typically, a recursive function call is used to divide a problem into more manageable parts. In this case, we split the array in two repeatedly until we have a lot of single element (or empty) arrays. Merging two single element arrays is simple. Put the small one first, then the large one second and you have a sorted array of two elements.  

To find the space complexity (Big O notation) of the merge sort algorithm, we need to look at the two subroutines separately. merge and mergeSort.  

Merge is an O(a+b) operation where a is the length of arr1 and b is the length of arr2\. Since we know that in this case, a.length is roughly equal to b.length, we can just say that it is an O(n) operation where n is a or b (remember, it's all about ballpark figures).  

MergeSort is a little more complex. In order to find it, we need to understand logarithms. Essentially, the log(n) is the number of times you need to multiply 2 by itself to get n.  

If n = 8, then log(n) = 3 because 2^3 = 8.. If n = 16, log(n) = 4\. Or conversely, **log(n) is the number of times you need to divide n by 2 to end up with 1.**  

Since we are splitting the array in two each time, we need to know how many times it takes to get down to array length of 1\. The answer to that is log(n) where n is the length of the array.  

The Arrays.copyOfRange statement is an O(n) operation. The merge operation is also O(n).  

Since we call mergeSort log(n) times and each time, we have to copy the array (O(n)), that complexity is O(nlogn).  

The merging takes O(n) times but it is also called log(n) times. This is slightly different though (although actually the same logic applies to the copying). The mathematical series of the lengths of the arrays being merges goes as follows.  

n + n/2 + n/4 + n/8 + n/16... = 2n as n => infinity.  

Therefore the merging in an O(n) operation.  

The whole algorithm is O(nlogn + n) but since we throw away everything but the leading term, **the merge sort algorithm is O(nlogn).**
