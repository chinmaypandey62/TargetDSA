# What is Monotonic Stack
Monotonic Stack is a specialized form of stack. The Monotonic Stack works similar to its naming. It maintains the property of strictly increasing or decreasing values to be stored in the stack.

## Key Concepts
1. **Monotonically Increasing Stack**:
   - **Introduction**: Keeps uniformly increasing values.
   - **Use Case**: Helpful when we need to find out the immediate predecessor (smaller value).
 
2. **Monotonically Decreasing Stack**:
   - **Introduction**: Keeps uniformly decreasing values.
   - **Use Case**: Helpful when we need to find out the immediate successor (greater value).

## When To Use
Monotonic Stack is helpful in scenarios when we need to find the immediate successor (greater value) or predecessor (smaller value) tackled till the current position.

## How To Use
### 1. Monotonically Increasing Stack
- **Condition 1**: When `stack.top < currentValue`
  - Simply push the value, as it is automatically increasing in nature.

- **Condition 2**: When `stack.top >= currentValue`
  - Now we need to find out the correct position of `currentValue` in the stack.
  - Keep popping the top values till the condition fails.
  - Push the `currentValue`.

- **Tip**:
  - While popping, track the count of greater or equal values tackled till the next smaller value and push it along with the `currentValue`.

- **Why it can be beneficial**:
  - This keeps track of the number of greater or equal values removed from the stack, providing additional insights into the elements processed before the current state.

### 2. Monotonically Decreasing Stack
- **Condition 1**: When `stack.top > currentValue`
  - Simply push the value, as it is automatically decreasing in nature.

- **Condition 2**: When `stack.top <= currentValue`
  - Now we need to find out the correct position of `currentValue` in the stack.
  - Keep popping the top values till the condition fails.
  - Push the `currentValue`.

- **Tip**:
  - While popping, track the count of smaller or equal values tackled till the next greater value and push it along with the `currentValue`.

- **Why it can be beneficial**:
  - This keeps track of the number of smaller or equal values removed from the stack, providing additional insights into the elements processed before the current state.
