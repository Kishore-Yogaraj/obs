**Two pointers** are a much broader pattern with various other patterns being a subset of two pointers. An example of this is **sliding windows.**

For further examination, what exactly is the difference between **two pointers** and **sliding windows**

Generally speaking, with two pointers we mainly care about the two individual points that the pointers are pointing to rather than a substring or sub array of the values between the two pointers.

### Pattern Recognized
I think the main pattern here to recognize is to check if we have to **traverse a linear data structure**, then check to see if we need **individual elements for our operation** then we **assign our left and right pointers to the left and right of the array or substring**, then we **increment the left pointer** or **decrement the right pointer** based on **some condition**
![[Leetcode-10 (1).jpg]]