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
