# Geometric Algebra from Scratch, Verified Against kingdon

A single Jupyter notebook that computes the geometric, inner and outer products of two fixed 3D vectors in plain Python, then reproduces the same operations with the [`kingdon`](https://github.com/tBuLi/kingdon) library and checks that the results agree.

It also verifies the fundamental identity for two vectors:

```
ab = a·b + a∧b
```

## The problem

Working in the 3D Euclidean algebra Cl(3,0), take

```
a = 2e₁ + 3e₂
b = e₁ − e₂ + 4e₃
```

and compute `ab`, `a·b` and `a∧b` two ways:

1. **From scratch** — plain Python, no libraries (not even NumPy).
2. **With kingdon** — using the library's `*`, `|` and `^` operators.

| Quantity | Value |
|----------|-------|
| `ab` | `−1 − 5e₁₂ + 8e₁₃ + 12e₂₃` |
| `a·b` | `−1` |
| `a∧b` | `−5e₁₂ + 8e₁₃ + 12e₂₃` |

Both implementations give these values. The inputs are integers, so the agreement is exact.

## How it works

The whole algebra follows from two rules for the basis vectors:

```
eᵢeᵢ = 1                    (Euclidean metric)
eᵢeⱼ = −eⱼeᵢ  for i ≠ j     (anti-commutativity)
```

The notebook is a linear walkthrough:

| Step | What | Notes |
|------|------|-------|
| 0 | Set up | Imports; kingdon is only used from Step 7 |
| 1 | Basis vectors | `e1`, `e2`, `e3` as plain lists |
| 2 | Define `a` and `b` | Built with two small helpers, `scale` and `add` |
| 3 | Multivectors | A dictionary with one coefficient per basis blade (8 in total) |
| 4 | Geometric product | `blade_product` multiplies two basis blades; `geometric_product` distributes over all pairs |
| 5 | Inner and outer products | Computed directly from the vector components, independently of Step 4 |
| 6 | Verify the identity | `ab = a·b + a∧b`, plus the symmetric / antisymmetric forms `(ab ± ba)/2` |
| 7 | Same operations in kingdon | The reference implementation |
| 8 | Automated comparison | `assert`-based checks against kingdon |

Two design choices are worth pointing out:

- **Step 5 does not reuse the geometric product.** `a·b` and `a∧b` come from the component formulas `Σ aᵢbᵢ` and `Σ (aᵢbⱼ − aⱼbᵢ) eᵢⱼ`. That makes Step 6 a real test of the identity rather than something true by construction.
- **Step 8 checks more than the two vectors.** Besides comparing `ab`, `a·b` and `a∧b`, it compares all 8 × 8 = 64 products of basis blades with kingdon, which covers the entire multiplication table.

## Scope

- The algebra is fixed to Cl(3,0).
- The geometric product works for any multivector in that algebra.
- The inner and outer products are implemented for two **vectors** only.
- The vectors `a` and `b` are hard-coded.

## A note on kingdon's blade keys

kingdon labels each basis blade with an integer bitmask, not a name: bit 0 is `e₁`, bit 1 is `e₂`, bit 2 is `e₃`.

| Key | Binary | Blade |
|-----|--------|-------|
| `0` | `000` | scalar |
| `1` | `001` | e₁ |
| `2` | `010` | e₂ |
| `3` | `011` | e₁₂ |
| `4` | `100` | e₃ |
| `5` | `101` | e₁₃ |
| `6` | `110` | e₂₃ |
| `7` | `111` | e₁₂₃ |

The notebook's `from_kingdon` function uses this to translate kingdon results into the dictionary format used by the from-scratch code.

## Running it

```bash
git clone https://github.com/ericridderstrom/geometric-algebra-from-scratch.git
cd geometric-algebra-from-scratch
pip install -r requirements.txt
jupyter lab geometric_algebra_verification.ipynb
```

Then choose **Run > Restart Kernel and Run All Cells**. Every check is an `assert`, so the notebook stops with an error if anything fails to match.

Tested with Python 3.13 and kingdon 3.0.0.

## Output

```
ab            = -1 -5 e12 +8 e13 +12 e23
a . b + a ^ b = -1 -5 e12 +8 e13 +12 e23
(ab + ba) / 2 = -1
(ab - ba) / 2 = -5 e12 +8 e13 +12 e23

Identity verified.

ab   : ours = -1 -5 e12 +8 e13 +12 e23         kingdon = -1 -5 e12 +8 e13 +12 e23         match
a . b: ours = -1                               kingdon = -1                               match
a ^ b: ours = -5 e12 +8 e13 +12 e23            kingdon = -5 e12 +8 e13 +12 e23            match

All 64 basis-blade products match kingdon.
```

## Files

```
geometric-algebra-from-scratch/
├── README.md
├── requirements.txt
├── .gitignore
└── geometric_algebra_verification.ipynb
```

## References

- kingdon — [github.com/tBuLi/kingdon](https://github.com/tBuLi/kingdon)
- Dorst, Fontijne, Mann — *Geometric Algebra for Computer Science* (2007)
- Hestenes, Sobczyk — *Clifford Algebra to Geometric Calculus* (1984)

## License

MIT
