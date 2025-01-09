### **Question 1:**

Write a PHP function that takes a string and returns the same string, but with the first letter of each word capitalized. The string can contain multiple words separated by spaces. 

For example:

```php
Input: "hello world"
Output: "Hello World"
```

The `ucwords()` function works as expected, but it does not handle edge cases like multiple spaces between words. Also, if the string has leading or trailing spaces, they should be trimmed.

Here’s an improved version of your function:

```php
function capitalizeWords($str)
{
    if (empty($str)) return '';
    // Trim spaces and then apply ucwords
    return ucwords(trim($str));
}
```

This way, even if there are extra spaces at the beginning or end, they won’t cause issues.

### **Question 2:**

Write a PHP function that checks if a given number is a prime number. The function should return `true` if the number is prime, and `false` otherwise.

For example:

```php
Input: 11
Output: true

Input: 4
Output: false
```

You're on the right track, but the current logic won't fully determine whether a number is prime.

A prime number is only divisible by 1 and itself, so your function needs to check whether the number has any divisors other than 1 and itself. Your current approach only checks for divisibility by 2.

Here’s an updated solution for you:

```php
function prime($num)
{
    if ($num <= 1) return false; // 1 or lower is not prime
    for ($i = 2; $i <= sqrt($num); $i++) {
        if ($num % $i == 0) {
            return false; // divisible by a number other than 1 or itself
        }
    }
    return true; // it's prime if no divisors are found
}

echo prime(11) ? 'true' : 'false'; // true
echo prime(4) ? 'true' : 'false';  // false
```

### **Explanation:**

- We check divisibility up to the square root of the number (`sqrt($num)`), which improves efficiency for larger numbers.
- We immediately return `false` if a divisor is found, indicating the number is not prime.

### **Question 3:**

Write a PHP function that takes a string as input and returns the most frequent character in the string. If there are multiple characters with the same frequency, return the first one.

For example:

```php
Input: "aabbbcc"
Output: "b"

Input: "abcabc"
Output: "a"
```

```php
/*
    Input: "aabbbcc"
    Output: "b"

    Input: "abcabc"
    Output: "a"
 */


function charFreq($str)
{
    if (empty($str)) return '';

    $frequency = [];
    foreach (str_split($str) as $char) {
        $frequency[$char] = (!isset($frequency[$char])) ? 1 : $frequency[$char] += 1;
    }

    $mostFreqChar = $str[0];
    $mostFreqCharCount = $frequency[$str[0]];
    foreach ($frequency as $key => $val) {
        if ($val > $mostFreqCharCount) {
            $mostFreqCharCount = $val;
            $mostFreqChar = $key;
        }
    }

    return $mostFreqChar;
}


echo charFreq("aabbbcc");
```

### **Question 4:**

Write a PHP function that finds the longest substring without repeating characters in a given string. The function should return the length of this substring.

For example:

```php
Input: "abcabcbb"
Output: 3  // "abc"

Input: "bbbbb"
Output: 1  // "b"

Input: "pwwkew"
Output: 3  // "wke"
```

### **Updated Solution:**

```php
function nonRepeatingLongestSubstr($str)
{
    if (empty($str)) return 0; // Return 0 for an empty string

    $charsBag = []; // Stores the current characters in the substring
    $longestLength = 0; // Tracks the longest substring length
    $start = 0; // Start index of the current window

    for ($end = 0; $end < strlen($str); $end++) {
        $char = $str[$end];

        // If the character is already in the bag, slide the window
        while (isset($charsBag[$char])) {
            unset($charsBag[$str[$start]]);
            $start++;
        }

        // Add the character to the bag and calculate the window length
        $charsBag[$char] = true;
        $longestLength = max($longestLength, $end - $start + 1);
    }

    return $longestLength;
}

// Test cases
echo nonRepeatingLongestSubstr("abcabcbb"); // Output: 3
echo "\n";
echo nonRepeatingLongestSubstr("bbbbb"); // Output: 1
echo "\n";
echo nonRepeatingLongestSubstr("bbbbbbbe"); // Output: 2
echo "\n";
echo nonRepeatingLongestSubstr("pwwkew"); // Output: 3
```

### **Explanation:**

1. **Sliding Window Technique**:
   - We use two pointers (`start` and `end`) to define a "window" that slides through the string.
   - As we iterate, if a duplicate character is found, we shrink the window by moving `start`.

2. **Using an Associative Array (`$charsBag`)**:
   - Keeps track of the characters in the current substring.
   - When a duplicate is found, we remove the leftmost character from `$charsBag` by sliding `start`.

3. **Longest Substring Calculation**:
   - On each iteration, calculate the length of the current substring (`$end - $start + 1`) and update the longest length if it's greater.

### **Question 5:**

Write a PHP function that determines whether a given string is a **valid anagram** of another string. Two strings are considered anagrams if they contain the same characters with the same frequencies but in a different order.

For example:

```php
Input: "listen", "silent"
Output: true

Input: "hello", "world"
Output: false

Input: "anagram", "nagaram"
Output: true
```

### **Requirements:**

- The function should take two strings as input.
- It should return `true` if they are anagrams, and `false` otherwise.

Your solution is almost there and does a great job of comparing the character frequencies in both strings. However, there are some minor points that can be improved:

### **Observations:**

1. **`$str1Chars[$char]++` Issue**:
   - The way you increment `$str1Chars[$char]++` is problematic. It will assign `null` due to how the ternary operator is being used in your code. This can be fixed by directly incrementing the count without the ternary operator.

2. **Efficiency**:
   - While creating two separate arrays for character counts (`$str1Chars` and `$str2Chars`) works, it can be simplified to avoid the need for the second array.

---

### **Optimized Solution:**

Here’s a cleaner and optimized version of your function:

```php
function areAnagrams($str1, $str2)
{
    // Anagrams must have the same length
    if (strlen($str1) !== strlen($str2)) return false;

    // Count the frequency of characters in the first string
    $charCount = [];
    foreach (str_split($str1) as $char) {
        $charCount[$char] = ($charCount[$char] ?? 0) + 1;
    }

    // Reduce the frequency based on the second string
    foreach (str_split($str2) as $char) {
        if (!isset($charCount[$char])) return false; // Extra character in $str2
        $charCount[$char]--;
        if ($charCount[$char] < 0) return false; // Too many occurrences of a character
    }

    return true; // If all checks pass, they are anagrams
}

// Test cases
echo areAnagrams("listen", "silent") ? 'true' : 'false'; // Output: true
echo "\n";
echo areAnagrams("hello", "world") ? 'true' : 'false';   // Output: false
echo "\n";
echo areAnagrams("anagram", "nagaram") ? 'true' : 'false'; // Output: true
```

---

### **Key Improvements:**

1. **Single Array for Character Counting**:
   - Instead of creating separate arrays for each string, this solution uses a single array (`$charCount`) to track character frequencies. Positive values indicate excess characters in `$str1`, while negative values indicate extra characters in `$str2`.

2. **Efficient Handling**:
   - By decrementing the count during the second loop, we immediately return `false` if any character is mismatched or has an excess count.

3. **Ternary Operator Simplification**:
   - The `($charCount[$char] ?? 0)` ensures that the default value is `0` if the character doesn’t exist in the array yet.

---

### **Question 6:**

Write a PHP function to find **all pairs of integers in an array that sum up to a specific target**. The function should return the pairs as an array of arrays.

For example:

```php
Input: [2, 4, 3, 7, 5, -1], Target: 6
Output: [[2, 4], [7, -1]]

Input: [1, 2, 3, 4, 5], Target: 8
Output: [[3, 5]]
```

### **Requirements:**

1. Avoid pairing the same element twice (e.g., don't use the same index twice).
2. The order of pairs doesn’t matter.
3. Handle edge cases, like an empty array.

### **Suggested Optimization:**

While the above function works well, you can optimize it further to avoid redundant checks by using a **hash map**. This approach reduces the time complexity from **O(n²)** to **O(n)**.

Here’s an optimized version:

---

### **Optimized Version (Using Hash Map):**

```php
function targetSumUp($arr, $target)
{
    $seen = [];
    $result = [];

    foreach ($arr as $num) {
        $complement = $target - $num;
        if (isset($seen[$complement])) {
            $result[] = [$complement, $num];
        }
        $seen[$num] = true;
    }

    return $result;
}

// Test cases
print_r(targetSumUp([2, 4, 3, 7, 5, -1], 6)); // Output: [[2, 4], [7, -1]]
print_r(targetSumUp([1, 2, 3, 4, 5], 8));     // Output: [[3, 5]]
```

---

### **Explanation of Optimized Version:**

1. **Hash Map (`$seen`)**:
   - As you iterate through the array, you store each number in the hash map.
   - For each number, check if its complement (`$target - $num`) exists in the hash map. If yes, it forms a valid pair.

2. **Efficiency**:
   - This approach eliminates the need for nested loops and reduces the time complexity to O(n).

---

### **Question 7:**

Write a PHP function to **find the first non-repeating character** in a given string. If all characters are repeating, the function should return `null`.

For example:

```php
Input: "swiss"
Output: "w" // 's' repeats, but 'w' is non-repeating

Input: "racecars"
Output: "e" // 'r' and 'c' repeat, but 'e' does not

Input: "aabbcc"
Output: null // All characters repeat
```

---

### **Requirements:**

1. The function should handle edge cases like empty strings.
2. The function should be efficient, using a single traversal or minimal overhead.

---

You're on the right track, but there are a couple of issues in your implementation:

1. **Logic for Tracking Frequency**:
   - `$assocChars[$char] = isset($assocChars[$char]);` is not correctly counting occurrences of characters. It will only set a boolean value (`true` or `false`), which doesn’t reflect how many times a character appears.

2. **Missing Frequency Check**:
   - In the second loop, you're checking `$char` directly instead of ensuring the character occurs exactly once in the string.

---

### Fixed Version:
Here's a corrected version of your function:

```php
function firstNonRepeatingChar($str)
{
    $assocChars = [];

    // First pass: Count occurrences of each character
    foreach (str_split($str) as $char) {
        $assocChars[$char] = ($assocChars[$char] ?? 0) + 1;
    }

    // Second pass: Find the first character with a count of 1
    foreach (str_split($str) as $char) {
        if ($assocChars[$char] === 1) {
            return $char;
        }
    }

    return null; // No non-repeating character found
}

// Test cases
echo firstNonRepeatingChar("swiss") . "\n";    // Output: "w"
echo firstNonRepeatingChar("racecars") . "\n"; // Output: "e"
echo firstNonRepeatingChar("aabbcc") . "\n";   // Output: null
echo firstNonRepeatingChar("") . "\n";         // Output: null
```

---

### **Explanation:**

1. **First Pass (Counting Frequencies)**:
   - Use an associative array (`$assocChars`) to count how many times each character appears in the string.
   - `$assocChars[$char] = ($assocChars[$char] ?? 0) + 1` increments the count for the character, initializing it to `0` if it doesn't already exist.

2. **Second Pass (Finding the First Non-Repeating Character)**:
   - Iterate over the string again to maintain the original order of characters. For each character, check if its frequency in `$assocChars` is exactly `1`.

3. **Edge Case (Empty String)**:
   - If the input string is empty, the function will return `null`.

---

### Why This Approach?

This solution is efficient because:

- It only loops through the string twice, making the time complexity **O(n)**.
- The space complexity is **O(k)**, where `k` is the number of unique characters in the string.

### **Question 8:**

Write a PHP function to **find the longest common prefix** among an array of strings. If there is no common prefix, return an empty string (`""`).

For example:

```php
Input: ["flower", "flow", "flight"]
Output: "fl"

Input: ["dog", "racecar", "car"]
Output: "" // No common prefix

Input: ["interspecies", "interstellar", "interstate"]
Output: "inter"
```

---

### Requirements:

1. The function should handle edge cases like an empty array or an array with only one string.
2. Aim for an efficient solution that minimizes unnecessary comparisons.

---

You're on the right path, but there are a few areas where improvements can be made. Specifically:

1. **Finding the Smallest Word**:
   - You don’t need to find the smallest word and compare every word to it. Instead, you can simply iterate through the strings character by character until the common prefix is identified.
  
2. **Unnecessary Complexity**:
   - The solution is a bit overcomplicated with the use of `str_split` and checking each character. We can simplify it by directly comparing prefixes.

3. **Improved Logic**:
   - You can iterate through the strings and compare characters at the same index. Stop once you encounter a mismatch.

---

### **Optimized Solution:**

Here’s a simplified version of your function that efficiently finds the longest common prefix:

```php
function longestCommonPrefix($strArr)
{
    if (empty($strArr)) return ''; // Handle edge case of empty array

    // Assume the first string as the prefix
    $prefix = $strArr[0];

    foreach ($strArr as $word) {
        // Reduce the prefix to match the current word
        while (strpos($word, $prefix) !== 0) {
            $prefix = substr($prefix, 0, strlen($prefix) - 1);
            if (empty($prefix)) return ''; // No common prefix found
        }
    }

    return $prefix;
}

// Test cases
echo longestCommonPrefix(["flower", "flow", "flight"]) . "\n";  // Output: "fl"
echo longestCommonPrefix(["dog", "racecar", "car"]) . "\n";     // Output: ""
echo longestCommonPrefix(["interspecies", "interstellar", "interstate"]) . "\n"; // Output: "inter"
```

---

### **Explanation:**

1. **Initial Prefix**: We assume the first string as the longest common prefix initially.
2. **Loop Through the Array**: For each string in the array, we check if the current prefix exists at the start of the string using `strpos()`. If it doesn't, we shorten the prefix by one character and check again.
3. **Edge Case Handling**: If the prefix becomes an empty string at any point, we return an empty string (`""`), indicating no common prefix.

---

### **Time Complexity:**

- The worst-case time complexity is **O(n * m)**, where:
  - `n` is the number of strings in the array.
  - `m` is the length of the shortest string.
  
This is efficient compared to comparing every character of each string individually.

---