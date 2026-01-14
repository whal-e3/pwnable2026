---
From: KHU CTF (PWNLAB wargame website)
Type: reversing
Files: "`baby-re`"
Dare date: 2026-01-13
Solve date: 2026-01-14
Idea: linear algebra (13 unknown, 13 equations)	`np.linalg.solve`
---

```bash
baby-re: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=5d5783d23d78bf70b80d658bccbce365f7448693, not stripped
```

# Solution
---
- `calc.py`
```python
#!/usr/bin/env python3
import numpy as np

def solve():
    """
    Solve the system of 13 linear equations to find a1[0] through a1[12].
    Each variable is an unsigned int (4 bytes, range 0 to 2^32-1).
    """
    # Coefficient matrix (13x13) - each row is an equation, each column is a variable a1[0] to a1[12]
    A = np.array([
        [37485, -21621, -1874, -46273, 50633, 43166, 29554, 16388, 57693, 14626, 21090, 39342, 54757],
        [50936, 4809, -6019, 38962, 14794, 22599, -837, -36727, -50592, -11829, -20046, -9256, 53228],
        [-38730, 52943, -16882, 26907, -44446, -18601, -65221, -47543, 17702, -33910, 42654, 5371, 11469],
        [57747, -23889, -26016, -25170, 54317, -32337, 10649, 34805, -9171, -22855, 8621, -634, -11864],
        [-14005, 16323, 43964, 34670, 54889, -6141, -35427, -61977, 28134, 43186, -59676, 15578, 50082],
        [-40760, -22014, 13608, -4946, -26750, -31708, 39603, 13602, -59055, -32738, 29341, 10305, -15650],
        [-47499, 57856, 13477, -10219, -5032, -21039, -29607, 55241, -6065, 16047, -4554, -2262, 18903],
        [-65419, 17175, -9410, -22514, -52377, -9235, 53309, 47909, -59111, -41289, -24422, 41178, -23447],
        [1805, 4135, -16900, 33381, 46767, 58551, -34118, -44920, -11933, -20530, 15699, -36597, 18231],
        [-42941, 61056, -45169, 41284, -1722, -26423, 47052, 42363, 15033, 18975, 10788, -33319, 63680],
        [-37085, -51590, -17798, -10127, -52388, 12746, 12587, 58786, -8269, 22613, 30753, -20853, 32216],
        [36650, 47566, -33282, -59180, 65196, 9228, -59599, -62888, 48719, 47348, -37592, 57612, 40510],
        [51735, 35879, -63890, 4102, 59511, -21386, -20769, 26517, 28153, 25252, -43789, 25633, 7314]
    ], dtype=np.int64)
    
    # Constant vector (right-hand side of equations)
    results = np.array([
        21399379,   # calc1
        1453872,    # calc2
        -5074020,   # calc3
        -5467933,   # calc4
        7787144,    # calc5
        -8863847,   # calc6
        -747805,    # calc7
        -11379056,  # calc8
        -166140,    # calc9
        9010363,    # calc10
        -4169825,   # calc11
        4081505,    # calc12
        1788229     # calc13
    ], dtype=np.int64)
    
    # Solve the system: A * x = results
    solution = np.linalg.solve(A, results)
    a1 = []
    for val in solution:
        a1.append(int(round(val)))
    return a1


if __name__ == "__main__":
    solution = solve()
    if solution:
        print("Solution found!\n")

        for i in range(13):
            print(f"a1[{i}] = {solution[i]} (0x{solution[i]:08x})")
        print(f"\nSolution as list: {solution}")
        
    else:
        print("Failed to find solution...")
```

- `solver.py`
```python
#!/usr/bin/env python3
from calc import solve
from pwn import *
import sys

solution = solve()
if solution is None or len(solution) != 13:
    print("Failed to solve equations (expected 13 integers).")
    sys.exit(1)

# Start the process (run from khu-ctf-ogj/)
r = process("./baby-re")

# Interact with the binary
for i in range(13):
    # Wait for the prompt "Var[i]: "
    prompt = f"Var[{i}]: ".encode()
    try:
        r.recvuntil(prompt)
    except Exception:
        print(f"Error: Did not receive expected prompt 'Var[{i}]: '")
        print(f"Received: {r.recvall().decode()}")
        r.close()
        sys.exit(1)
    
    # Send the corresponding number from solution list
    num = solution[i]
    r.sendline(str(num).encode())
    print(f"Sent Var[{i}] = {num}")

# Receive and print the final output
final_output = r.recvall(timeout=2)
print("\nFinal output from binary:")
print(final_output.decode(errors="replace"))

r.close()
```

# 🚩flag
---
Math is hard!