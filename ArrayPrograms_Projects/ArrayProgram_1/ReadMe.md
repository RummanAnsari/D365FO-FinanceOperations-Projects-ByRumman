# ANSARI_ArrayAssignment

This X++ program demonstrates **array declaration**, **initialization**, **iteration**, **sum calculation**, and **updating array elements** in Dynamics 365 Finance & Operations (D365 F&O).

---

## 📘 Class Name
`ANSARI_ArrayAssignment`

---

## 🧠 Overview
This program performs the following operations:
1. Declares and initializes a fixed-size array.
2. Displays the original array elements.
3. Calculates the total of all array elements.
4. Updates one element in the array.
5. Adds 5 to every element.
6. Displays the updated array.

---

## 🧩 Code Example
```x++
class ANSARI_ArrayAssignment
{
    public static void main(Args _args)
    {
        int numberArray[5]; // fixed-size array of 5 elements
        int i;
        int total;          
        int arrSize = 5;    

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



Step-by-Step Explanation
1. Declare Array

A fixed-size integer array of 5 elements is declared:

int numberArray[5];

2. Initialize Array

Values are assigned manually to each index:

numberArray[1] = 5;
numberArray[2] = 10;
numberArray[3] = 15;
numberArray[4] = 20;
numberArray[5] = 25;

3. Display Original Array

A for loop displays the original array values:

info("Original Array:");
for (i = 1; i <= arrSize; i++)
{
    info(int2str(numberArray[i]));
}

4. Calculate Total

The total (sum) of all array elements is calculated and displayed:

total = 0;
for (i = 1; i <= arrSize; i++)
{
    total += numberArray[i];
}
info(strFmt("Total: %1", total));

5. Update Third Element

The third element’s value is updated from 15 to 30:

numberArray[3] = 30;

6. Add 5 to Each Element

Each element in the array is increased by 5:

for (i = 1; i <= arrSize; i++)
{
    numberArray[i] += 5;
}

7. Display Updated Array

The updated array values are displayed again:

info("Updated Array:");
for (i = 1; i <= arrSize; i++)
{
    info(int2str(numberArray[i]));
}

Sample Output
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

Model Information
Property	Value
Model Name	D365_FO_Learning
Model Publisher	ANSARI
Layer	usr
Version	1.0.0.0
Model Description	Array Assignment Example
Model Display Name	D365_FO_Learning
Author

Rumman Ansari

Version History
Version	Date	Description
1.0.0.0	11-Oct-2025	Initial version of the class.
Learning Outcomes

After completing this assignment, students will be able to:

Declare and initialize arrays in X++

Iterate through arrays using for loops

Perform calculations on array elements

Modify array elements dynamically

Display output using the info() function

Requirements

Application: Microsoft Dynamics 365 Finance & Operations

Language: X++

Environment: Visual Studio with D365 F&O Model (Layer: usr)
