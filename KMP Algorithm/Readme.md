Sure, Chinmay! Here's the cleaned-up and corrected version of your Markdown file. It keeps only **one example** throughout (i.e., pattern = `"ababd"` and text = `"ababcabcabababd"`), removes redundancy, and keeps the explanation structured and clear.

---

## 🔍 **Problem**

We are given:

* **Text**: `"ababcabcabababd"`
* **Pattern**: `"ababd"`

We want to find **where the pattern occurs** in the text.

---

## 🔍 **Introduction to KMP Algorithm**

The **KMP algorithm** is a smart way to search for a **pattern** inside a **text**.  
Unlike the naive approach (which checks all positions one by one), KMP avoids **rechecking characters** that already matched by remembering past comparisons using a helper array.

**Time Complexity**:

* **Prefix Table Construction**: `O(m)`
* **Pattern Searching**: `O(n)`

Where `m` is the length of the pattern and `n` is the length of the text.

---

## 🧩 **Prefix Table (LPS Array)**

The core of KMP is the **LPS (Longest Prefix Suffix)** array.  
It tells us how far to shift the pattern when a mismatch happens — without going backward in the text.

### What is LPS?

* For each position in the pattern, it stores the length of the **longest proper prefix** that is also a **suffix** of the substring `pattern[0...i]`.

---

## 🧠 **Step 1: Build the LPS Array**

Let’s build the LPS for `"ababd"`:

Pattern: `"a b a b d"`  
Indexes: ` 0 1 2 3 4`

| i | pattern[i] | pattern[len] | Match? | Action                             | LPS so far        |
|---|------------|--------------|--------|------------------------------------|-------------------|
| 1 | b          | a            | ❌     | No match → lps[1] = 0, i++         | [0, 0]            |
| 2 | a          | a            | ✅     | Match → len++, lps[2] = 1, i++     | [0, 0, 1]         |
| 3 | b          | b            | ✅     | Match → len++, lps[3] = 2, i++     | [0, 0, 1, 2]      |
| 4 | d          | a            | ❌     | Mismatch → fallback to lps[1] = 0  | [0, 0, 1, 2]      |
| 4 | d          | a            | ❌     | Still mismatch → lps[4] = 0, i++   | [0, 0, 1, 2, 0]   |

**Final LPS array: `[0, 0, 1, 2, 0]`**

---

## 🔍 **Step 2: Start Matching the Pattern in the Text**

We use two pointers:

* `i` → index in the text  
* `j` → index in the pattern

We try to match the pattern in the text character-by-character using the LPS array.

Text: `"a b a b c a b c a b a b a b d"`  
Index: ` 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14`  
Pattern: `"a b a b d"`  
LPS: `[0, 0, 1, 2, 0]`

### 🔁 Matching Loop:

| i  | text[i] | pattern[j] | Match? | Action                                                                 | i → | j → |
|----|---------|------------|--------|------------------------------------------------------------------------|-----|-----|
| 0  | a       | a          | ✅     | Match → move both                                                      | 1   | 1   |
| 1  | b       | b          | ✅     | Match → move both                                                      | 2   | 2   |
| 2  | a       | a          | ✅     | Match → move both                                                      | 3   | 3   |
| 3  | b       | b          | ✅     | Match → move both                                                      | 4   | 4   |
| 4  | c       | d          | ❌     | Mismatch → j = lps[3] = 2                                              | 4   | 2   |
| 4  | c       | a          | ❌     | Mismatch → j = lps[1] = 0                                              | 4   | 0   |
| 4  | c       | a          | ❌     | Still mismatch → i++                                                   | 5   | 0   |
| 5  | a       | a          | ✅     | Match → move both                                                      | 6   | 1   |
| 6  | b       | b          | ✅     | Match                                                                  | 7   | 2   |
| 7  | c       | a          | ❌     | Mismatch → j = lps[1] = 0                                              | 7   | 0   |
| 7  | c       | a          | ❌     | Mismatch → i++                                                         | 8   | 0   |
| 8  | a       | a          | ✅     | Match                                                                  | 9   | 1   |
| 9  | b       | b          | ✅     | Match                                                                  | 10  | 2   |
| 10 | a       | a          | ✅     | Match                                                                  | 11  | 3   |
| 11 | b       | b          | ✅     | Match                                                                  | 12  | 4   |
| 12 | a       | d          | ❌     | Mismatch → j = lps[3] = 2                                              | 12  | 2   |
| 12 | a       | a          | ✅     | Match                                                                  | 13  | 3   |
| 13 | b       | b          | ✅     | Match                                                                  | 14  | 4   |
| 14 | d       | d          | ✅     | 🎉 Pattern matched at index `14 - 4 = 10` → Reset j = lps[4] = 0        | 15  | 0   |

---

### 🎉 Final Result:

```

Pattern found at index 10

````

---

## ✅ Why Each Step Matters

* **LPS** prevents rechecking characters in the text.
* On mismatch, we **reuse previous matches** instead of restarting.
* After a match, we continue to check for more occurrences.

---

## 🛠️ **How KMP Works – Step-by-Step**

1. **Preprocess** the pattern and build the LPS array.
2. **Scan the text** using two pointers:
   - `i` for text
   - `j` for pattern
3. If `text[i] == pattern[j]`, move both forward.
4. If mismatch:
   - If `j > 0`, set `j = lps[j - 1]`
   - Else, move `i` forward

---

## 💻 Java Implementation

```java
public class KMPAlgorithm {

    // Build LPS array
    public static int[] buildLPS(String pattern) {
        int m = pattern.length();
        int[] lps = new int[m];
        int len = 0;
        int i = 1;

        while (i < m) {
            if (pattern.charAt(i) == pattern.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        return lps;
    }

    // Search for pattern in text
    public static void search(String text, String pattern) {
        int n = text.length();
        int m = pattern.length();
        int[] lps = buildLPS(pattern);

        int i = 0, j = 0;

        while (i < n) {
            if (text.charAt(i) == pattern.charAt(j)) {
                i++;
                j++;
            }

            if (j == m) {
                System.out.println("Pattern found at index " + (i - j));
                j = lps[j - 1]; // prepare for next match
            } else if (i < n && text.charAt(i) != pattern.charAt(j)) {
                if (j != 0) {
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }
    }

    public static void main(String[] args) {
        String text = "ababcabcabababd";
        String pattern = "ababd";

        System.out.println("Text: " + text);
        System.out.println("Pattern: " + pattern);
        search(text, pattern);
    }
}
````

---

## 🧪 Example Input & Output

### Input

```java
text = "ababcabcabababd"
pattern = "ababd"
```

### Output

```
Pattern found at index 10
```

---

## ✅ Summary

* KMP uses previous match info via the **LPS array**.
* It avoids backtracking in the text, making it **efficient**.
* The algorithm runs in **O(n + m)** time.
* It’s ideal for searching repeated patterns in large texts.

```

---

Let me know if you'd like the same version exported as a `.md` file or want the walkthrough animated in GIF/HTML5 format.
```
