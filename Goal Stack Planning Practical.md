# Practical Report: Goal Stack Planning in Blocks World

**Course:** Artificial Intelligence Lab  
**Topic:** Goal Stack Planning & Blocks World Domain  

---

## 1. Aim
To implement Goal Stack Planning for solving the Classic Blocks World problem and transition a set of blocks from an initial state to a specified goal configuration using automated planning operators.

---

## 2. Objectives
* Understand the core concepts of **Goal Stack Planning** (a classical STRIPS-style planning approach using a stack of goals, subgoals, and operators).
* Model the **Blocks World** domain, including state representation, clearing dependencies, and primitive actions (`PICKUP`, `PUTDOWN`, `STACK`, `UNSTACK`).
* Implement recursive precondition resolution (the `clear` function) to ensure blocks are free before being manipulated.
* Trace and verify the step-by-step execution path required to achieve a complex goal arrangement.

---

## 3. Theory & Background

### The Blocks World Domain
The Blocks World consists of a flat table surface and a set of identifiable blocks (e.g., `A`, `B`, `C`). Blocks can either rest directly on the table or be stacked on top of another block. A block can only be moved if it is **clear** (i.e., no other block is currently resting on top of it).

### Goal Stack Planning
Goal Stack Planning uses a stack to store goals, subgoals, and actions. When a goal is not satisfied, the planner pushes the necessary subgoals and operators onto the stack in reverse order of execution. In this simplified procedural implementation:
1. **`clear(x)`**: Recursively checks and un-stacks any block resting on top of block $x$, placing it on the table until $x$ is free.
2. **`move(x, y)`**: Prepares both block $x$ and target $y$ by clearing them, picks up or un-stacks $x$, and stacks it onto $y$.

---

## 4. Python Implementation

```python
# Initial State Representation: C is on A; A and B are on the table.
# state['C'] = 'A' means C is on top of A.
# state['A'] = 'TABLE' means A is directly on the table.
state = {'A': 'TABLE', 'B': 'TABLE', 'C': 'A'}

def clear(x):
    """Recursively clear block x by removing any block sitting on top of it."""
    for b, s in list(state.items()):
        if s == x:
            clear(b)
            print(f"UNSTACK {b} FROM {x}")
            print(f"PUTDOWN {b}")
            state[b] = 'TABLE'

def move(x, y):
    """Move block x onto block y (or TABLE), clearing prerequisites first."""
    clear(x)
    clear(y)

    if state[x] == 'TABLE':
        print(f"PICKUP {x}")
    else:
        print(f"UNSTACK {x} FROM {state[x]}")

    print(f"STACK {x} ON {y}")
    state[x] = y

# Execution: Transitioning to the goal configuration
print("--- Starting Planning Execution ---")
move('B', 'A')
move('C', 'B')

print("\nFinal State:", state)
print("Goal Achieved Successfully!")
```

---

## 5. Execution Output & Trace

When you run the script, the planner evaluates the preconditions and outputs the exact sequence of primitive robot actions:

```text
--- Starting Planning Execution ---
UNSTACK C FROM A
PUTDOWN C
PICKUP B
STACK B ON A
PICKUP C
STACK C ON B

Final State: {'A': 'TABLE', 'B': 'A', 'C': 'B'}
Goal Achieved Successfully!
```

### Action Breakdown:
1. **`UNSTACK C FROM A` & `PUTDOWN C`**: Block `C` is on top of `A`, blocking `A`. To move `B` onto `A`, `A` must first be clear. Thus, `C` is placed on the table.
2. **`PICKUP B` & `STACK B ON A`**: Block `B` is picked up from the table and stacked onto `A`.
3. **`PICKUP C` & `STACK C ON B`**: Block `C` is picked up from the table and stacked onto `B`, resulting in the final stack configuration where `C` is on `B`, and `B` is on `A` ($A \leftarrow B \leftarrow C$).

---

## 6. Conclusion
This practical successfully demonstrates the application of Goal Stack Planning to solve the Blocks World problem. By utilizing recursive state checking (`clear`) and deterministic operator application (`move`), the system successfully resolved preconditions and reached the target configuration without violating physical constraints.