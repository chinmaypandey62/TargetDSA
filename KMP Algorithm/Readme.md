# KMP String Matching Algorithm
---

## 🔍 Problem

We are given:

* **Text**: `"ababcabcabababd"`
* **Pattern**: `"ababd"`

Goal: Find all occurrences of the **pattern** in the **text**.

---

## 🔍 Introduction to KMP Algorithm

The **Knuth–Morris–Pratt (KMP)** algorithm efficiently searches for a **pattern** inside a **text** by skipping unnecessary comparisons. Instead of restarting the match after a mismatch, it uses a precomputed helper array (LPS) to know where to resume in the pattern.

**Time Complexity**:

* **LPS Construction**: `O(m)`
* **Search Phase**: `O(n)`

(where `m` = pattern length, `n` = text length)

---

## 🧩 Prefix Table (LPS Array)

The **LPS (Longest Prefix Suffix)** array stores, for each index `i` in the pattern, the length of the longest proper prefix of `pattern[0..i]` that is also a suffix of this substring. It determines how far to shift the pattern on a mismatch without rechecking text characters.

---

## 🧠 Step 1: Build the LPS Array

**Pattern**: `"ababd"`
**Indexes**: `0 1 2 3 4`

| i | pattern\[i] | previous len | Match? | Action                        | LPS so far       |
| - | ----------- | ------------ | ------ | ----------------------------- | ---------------- |
| 0 | a           | —            | —      | Initialize `lps[0] = 0`       | \[0]             |
| 1 | b           | 0            | ❌      | `lps[1] = 0`, i++             | \[0, 0]          |
| 2 | a           | 0            | ✅      | `len = 1`, `lps[2] = 1`, i++  | \[0, 0, 1]       |
| 3 | b           | 1            | ✅      | `len = 2`, `lps[3] = 2`, i++  | \[0, 0, 1, 2]    |
| 4 | d           | 2            | ❌      | `len = lps[1] = 0` (fallback) | \[0, 0, 1, 2]    |
| 4 | d           | 0            | ❌      | `lps[4] = 0`, i++             | \[0, 0, 1, 2, 0] |

**Final LPS array:** ` [0, 0, 1, 2, 0]`

---

## 🔍 Step 2: Search Phase (Matching Loop)

**Text**:  `"ababcabcabababd"`
**Pattern**: `"ababd"`
**LPS**:    `[0, 0, 1, 2, 0]`

Maintain two indices: `i` for **text**, `j` for **pattern**.

| i  | text\[i] | pattern\[j] | Match? | Action                                                     | new i | new j |
| -- | -------- | ----------- | ------ | ---------------------------------------------------------- | ----- | ----- |
| 0  | a        | a           | ✅      | advance both                                               | 1     | 1     |
| 1  | b        | b           | ✅      | advance both                                               | 2     | 2     |
| 2  | a        | a           | ✅      | advance both                                               | 3     | 3     |
| 3  | b        | b           | ✅      | advance both                                               | 4     | 4     |
| 4  | c        | d           | ❌      | fallback `j = lps[3] = 2`                                  | 4     | 2     |
| 4  | c        | a           | ❌      | fallback `j = lps[1] = 0`                                  | 4     | 0     |
| 4  | c        | a           | ❌      | advance `i`                                                | 5     | 0     |
| 5  | a        | a           | ✅      | advance both                                               | 6     | 1     |
| 6  | b        | b           | ✅      | advance both                                               | 7     | 2     |
| 7  | c        | a           | ❌      | fallback `j = lps[1] = 0`                                  | 7     | 0     |
| 7  | c        | a           | ❌      | advance `i`                                                | 8     | 0     |
| 8  | a        | a           | ✅      | advance both                                               | 9     | 1     |
| 9  | b        | b           | ✅      | advance both                                               | 10    | 2     |
| 10 | a        | a           | ✅      | advance both                                               | 11    | 3     |
| 11 | b        | b           | ✅      | advance both                                               | 12    | 4     |
| 12 | a        | d           | ❌      | fallback `j = lps[3] = 2`                                  | 12    | 2     |
| 12 | a        | a           | ✅      | advance both                                               | 13    | 3     |
| 13 | b        | b           | ✅      | advance both                                               | 14    | 4     |
| 14 | d        | d           | ✅      | **match** → report at `i - j = 10`; reset `j = lps[4] = 0` | 15    | 0     |

**Result:** Pattern found at **index 10**.

---

## ✅ Why Each Step Matters

* **LPS** avoids redundant rechecks in the text.
* On mismatch, we resume in the pattern using **previously matched info**.
* After finding a match, we can continue to search for further occurrences.

---

## 🛠️ How KMP Works – Step-by-Step

1. **Preprocess** the pattern → build the **LPS array** in `O(m)`.
2. **Scan** the text with two pointers (`i`, `j`), using LPS to skip comparisons.
3. On **match**, increment both pointers.
4. On **mismatch**:

   * If `j > 0`, set `j = lps[j - 1]`.
   * Else, advance `i`.

Overall, the search runs in **O(n + m)** time.

---

## 💻 Java Implementation

```java
public class KMPAlgorithm {
    // Build LPS array
    public static int[] buildLPS(String pattern) {
        int m = pattern.length();
        int[] lps = new int[m];
        int len = 0, i = 1;

        while (i < m) {
            if (pattern.charAt(i) == pattern.charAt(len)) {
                lps[i++] = ++len;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        return lps;
    }

    // KMP search
    public static void search(String text, String pattern) {
        int[] lps = buildLPS(pattern);
        int i = 0, j = 0, n = text.length(), m = pattern.length();

        while (i < n) {
            if (text.charAt(i) == pattern.charAt(j)) {
                i++; j++;
            }
            if (j == m) {
                System.out.println("Pattern found at index " + (i - j));
                j = lps[j - 1];
            } else if (i < n && text.charAt(i) != pattern.charAt(j)) {
                j = (j > 0) ? lps[j - 1] : ++i;
            }
        }
    }

    public static void main(String[] args) {
        String text = "ababcabcabababd";
        String pattern = "ababd";
        search(text, pattern);
    }
}
```

---

## 🧪 Example Input & Output

**Input:**

```java
text = "ababcabcabababd";
pattern = "ababd";
```

**Output:**

```
Pattern found at index 10
```

---

## 📚 Summary

* KMP leverages the **LPS array** to skip unnecessary comparisons.
* Runs in **O(n + m)** time, making it efficient for large inputs.
* Ideal for patterns with repeated subparts and long texts.

---

