# Assignment: Understanding Arrays in X++

## Objective

The purpose of this assignment is to understand the concept of **fixed-size arrays** in X++ and perform basic operations such as initialization, traversal, sum calculation, updating elements, and modifying array values.

---

## Problem Statement

Analyze the following X++ class `ANSARI_ArrayAssignment`:

```x++
class ANSARI_ArrayAssignment
{
    public static void main(Args _args)
    {
        int numberArray[5]; // fixed-size array of 5 elements
        int i;
        int total;          // renamed from 'sum' to avoid keyword conflict
        int arrSize = 5;    // manually set the size of the array

        // Initialize array
        numberArray[1] = 5;
        numberArray[2] = 10;
        numberArray[3] = 15;
        numberArray[4] = 20;
        numberArray[5] = 25;

        // Display array
        info("Original Array:");
        for (i = 1; i <= arrSize; i++)
        {
            info(int2str(numberArray[i]));
        }

        // Calculate sum
        total = 0;
        for (i = 1; i <= arrSize; i++)
        {
            total += numberArray[i];
        }
        info(strFmt("Total: %1", total));

        // Update third element
        numberArray[3] = 30;

        // Add 5 to each element
        for (i = 1; i <= arrSize; i++)
        {
            numberArray[i] += 5;
        }

        // Display updated array
        info("Updated Array:");
        for (i = 1; i <= arrSize; i++)
        {
            info(int2str(numberArray[i]));
        }
    }
}
```

---

## Assignment Tasks

1. **Initialization:**
   Understand how the array `numberArray` is initialized with 5 elements.

2. **Display Array:**
   Print all elements of the array using a `for` loop.

3. **Sum Calculation:**
   Calculate the total of all elements in the array.

4. **Element Update:**
   Update the 3rd element of the array.

5. **Modify Array Elements:**
   Add 5 to each element in the array.

6. **Display Updated Array:**
   Print the updated array elements after modification.

---

## Solution and Explanation

### 1. Array Declaration

```x++
int numberArray[5];
```

* Declares a **fixed-size array** of 5 integer elements.
* Arrays in X++ are **1-based**, so the first element is `numberArray[1]`.

### 2. Array Initialization

```x++
numberArray[1] = 5;
numberArray[2] = 10;
numberArray[3] = 15;
numberArray[4] = 20;
numberArray[5] = 25;
```

* Each element is assigned a specific integer value.
* `numberArray[3]` initially holds the value **15**.

### 3. Display Original Array

```x++
for (i = 1; i <= arrSize; i++)
{
    info(int2str(numberArray[i]));
}
```

* Loop runs from **1 to 5**.
* `int2str()` converts integer values to string for display.
* Original array output:

  ```
  5
  10
  15
  20
  25
  ```

### 4. Calculate Total

```x++
total = 0;
for (i = 1; i <= arrSize; i++)
{
    total += numberArray[i];
}
info(strFmt("Total: %1", total));
```

* Sum of elements: 5 + 10 + 15 + 20 + 25 = **75**
* `strFmt()` is used to format the output string.

### 5. Update Third Element

```x++
numberArray[3] = 30;
```

* Changes the value of the **3rd element** from 15 to 30.

### 6. Add 5 to Each Element

```x++
for (i = 1; i <= arrSize; i++)
{
    numberArray[i] += 5;
}
```

* Each element is incremented by **5**.
* Updated array values:

  ```
  10   // 5 + 5
  15   // 10 + 5
  35   // 30 + 5
  25   // 20 + 5
  30   // 25 + 5
  ```

### 7. Display Updated Array

```x++
for (i = 1; i <= arrSize; i++)
{
    info(int2str(numberArray[i]));
}
```

* Prints the **modified array** values.

---

## Output

```
Original Array:
5
10
15
20
25
Total: 75
Updated Array:
10
15
35
25
30
```

---

## Key Concepts Learned

* Declaration and initialization of **fixed-size arrays** in X++.
* Traversing arrays using **for loops**.
* Performing calculations like **sum**.
* Updating **individual elements**.
* Applying **modifications to all elements** of an array.

---

## Conclusion

This assignment demonstrates the basic operations on arrays in X++, including initialization, traversal, updating, and summing array elements. These concepts are fundamental for working with collections of data efficiently in Dynamics 365 Finance and Operations.
