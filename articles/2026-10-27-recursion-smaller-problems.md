# Recursion: Turning Repeated Work into a Smaller Problem

A directory contains thousands of measurement files, nested into folders by site and year. You need a single count of all the records, no matter how deeply they are nested. A loop can walk the folders, but the structure itself suggests another approach: count the files in this folder, then ask the same question about each subfolder. That self-similar move is recursion.

Recursion is a way to express repetition by having a function call itself on a smaller or simpler input. It appears in algorithms for trees, nested data, divide-and-conquer methods, and mathematical sequences. The important word is not “itself,” though. It is “smaller.” A recursive call needs a route toward a stopping condition, or the computation can continue until it exhausts resources.

## The shape of a recursive computation

A recursive function needs an input, a decision about what to do with that input, and a result available when the computation ends. Usually, its definition has two parts:

1. **A base case** gives an answer directly, without another recursive call.
2. **A recursive case** transforms the input and calls the function again.

For example, to sum a list of numbers, one possible definition is “the sum of an empty list is zero; otherwise, add its first value to the sum of the remaining list.” In pseudocode:

```text
sum([]) = 0
sum([first, ...rest]) = first + sum(rest)
```

The recursive case reduces the list by one element. Eventually, the remaining list is empty, and the base case returns zero. The function calls do not all execute at once. Each call waits for its smaller subproblem to return, then uses that result.

This pattern clarifies the distinction between recursion and iteration. An iterative algorithm repeats work with a loop. A recursive algorithm repeats work with function calls. Both can solve the same problem. Recursion is particularly natural when the input is recursively structured, such as a tree whose branches contain more trees. Iteration is often preferable when it makes the work or memory use simpler to control.

## A worked example: summing a small tree

Suppose a monitoring system groups daily readings in a binary tree. Each node contains a number and may have a left and right child. For this example, the tree is:

```text
        4
       / \
      2   7
     / \   \
    1   3   6
```

Define `tree_sum(node)` as follows:

- If `node` is empty, return 0.
- Otherwise, return the node's value plus the sum of its left subtree plus the sum of its right subtree.

In mathematical notation, for a nonempty node $v$ with value $x_v$ and children $L$ and $R$:

$$
S(v) = x_v + S(L) + S(R), \qquad S(\varnothing)=0.
$$

Now evaluate it from the leaves upward. The leaves have no children, so:

$$
S(1)=1+0+0=1, \quad S(3)=3, \quad S(6)=6.
$$

The node with value 2 has children 1 and 3:

$$
S(2)=2+S(1)+S(3)=2+1+3=6.
$$

The node with value 7 has left child empty and right child 6:

$$
S(7)=7+0+6=13.
$$

At the root:

$$
S(4)=4+S(2)+S(7)=4+6+13=23.
$$

A direct check adds the six stored values: $1+2+3+4+6+7=23$. The two calculations agree. The empty-tree base case matters even though the pictured tree has a missing child: it gives that missing branch a defined contribution of zero. Without it, the function would have no answer for incomplete branches.

This example also shows why the result is available only after the smaller computations finish. The root cannot be summed until its children’s subtree totals are known. A typical implementation might look like this:

```python
def tree_sum(node):
    if node is None:
        return 0
    return node.value + tree_sum(node.left) + tree_sum(node.right)
```

This code assumes that the structure is a finite tree, each node has the attributes shown, and values support addition. It does not handle cycles, malformed objects, or numeric overflow in fixed-width numeric types. Those are engineering properties to consider rather than details that the recurrence makes disappear.

## Progress, termination, and resource use

A base case alone does not guarantee termination. Every recursive path must eventually reach one. In the tree example, each recursive call moves to a child. In a finite tree, repeatedly moving downward must end at a missing child. That gives a simple progress argument: calls descend through a finite structure, and no path can descend forever.

For a numerical function, progress might mean reducing a nonnegative integer. For a list, it might mean shortening the list. A useful review question is: “What quantity gets closer to the base case on every recursive call?” If the answer is unclear, the function may repeat the same input or move away from termination.

The tree-sum algorithm visits every node once, so its running time is $O(n)$ for $n$ nodes. Its recursive call stack uses $O(h)$ space, where $h$ is the tree height. A balanced tree has height proportional to $\log n$, while a highly skewed tree can have height proportional to $n$. In a language with a limited call stack, a deeply skewed tree can therefore fail even though the total work is linear. An explicit stack and a loop can avoid relying on function-call depth.

These bounds describe the stated algorithm under ordinary assumptions: each node is visited once, field access and addition take constant time, and the structure is a tree rather than a graph with shared subtrees or cycles. Shared references can cause repeated work unless handled deliberately. A cycle can cause unending traversal unless the algorithm tracks visited nodes or rejects cyclic input.

## Recursion in data work

Recursive thinking is useful wherever data is nested or a large task can be split into smaller tasks.

- **Nested records and folders:** Visit each child container and combine its summary with the current container’s summary.
- **Tree-based indexes:** Search or aggregate along branches, following only the parts relevant to the query.
- **Divide and conquer:** Split a problem into smaller instances, solve each, and combine the answers. Merge sort, for example, sorts smaller portions before merging them.
- **Recursive definitions in statistics and computing:** A sequence may be specified using earlier terms. Implementations must still choose an evaluation strategy and account for repeated calculations.
- **Parsing structured data:** A parser can process a nested expression by processing the expressions inside it, subject to limits on depth and valid syntax.

The source section introduces direct recursion, where a function calls itself, and distinguishes mutual recursion, where two functions call each other, from a longer indirect cycle of calls. These are call-structure descriptions. For example, an `is_even` function can delegate to `is_odd`, which delegates back with a smaller absolute integer. Such definitions need carefully chosen base cases and progress rules too. “Two functions call each other” is not by itself a termination argument.

A recursive mathematical definition also does not prescribe a single programming language or implementation. The section’s examples use a stack-oriented notation, but the underlying idea transfers to ordinary functions, loops, and data structures. Likewise, an iterative and recursive formulation may return the same result while differing in performance, memory use, and failure modes.

## Pitfalls to check before trusting the result

**Missing or incorrect base cases.** An empty list, missing tree child, or smallest valid number must have defined behavior. A base case that returns the wrong identity value can quietly corrupt an aggregate. For a sum, zero is the identity because $x+0=x$.

**No progress.** A recursive call that receives the same input may repeat indefinitely. Confirm that the input becomes measurably smaller or simpler, and test boundary inputs.

**Depth and stack limits.** A valid input can still be too deep for the runtime’s call stack. Consider iterative traversal for untrusted or highly unbalanced structures.

**Repeated subproblems.** Some recurrences recompute the same result many times. Memoization or a bottom-up iterative method may reduce work, but adds storage or implementation choices. Do not assume recursion is automatically efficient.

**Data assumptions hidden in the definition.** The tree-sum example presumes a finite tree with no cycles. Real data can violate such assumptions. Validate the structure, handle invalid values explicitly, and document whether missing measurements are excluded, treated as zero, or cause an error. Those choices change the meaning of a summary.

**Confusing a computed summary with evidence.** A correct recursive total is still a descriptive calculation. It does not establish statistical significance, explain why values differ, or show that one variable caused another to change. Those claims require suitable study design and analysis beyond the traversal itself.

## Small exercises, with answers

1. What should `sum([])` return if the function sums numbers? **Answer:** Zero, the additive identity.
2. A recursive function processes a list by calling itself on the same list. What is the concern? **Answer:** It has no evident progress toward a base case and may not terminate.
3. For a tree with 100 nodes, how many times does the worked `tree_sum` visit a node, assuming a proper tree? **Answer:** 100 node visits, plus calls on empty child references. Each actual node is processed once.
4. Does an accurate sum of measurements show that a treatment caused an increase? **Answer:** No. A sum is descriptive; causal conclusions require an appropriate design and analysis.

Recursion is most useful when its structure mirrors the problem’s structure. State the base case, make progress explicit, and account for depth and repeated work. Then verify a small example by hand, just as we checked the tree total of 23. That combination of definition, termination reasoning, and independent checking makes recursive code easier to trust.

---

Published 2026-10-27.

**Source:** Discrete Mathematics for Data Science -- Jack Pope; -- 2026 -- CRC Press, section **11.1 Recursion**.

This is an original explanatory note; the source book is not redistributed.
