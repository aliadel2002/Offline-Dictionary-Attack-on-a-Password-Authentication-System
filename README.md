# Offline Dictionary Attack on a Password Authentication System

ECE 1155: Information Security - Cyberattack Demonstration and Mitigation - Ali Morsy

Simulates a login system that stores password hashes in a user database, then runs
an offline dictionary attack against a stolen copy of that database. First with the
vulnerable configuration (unsalted MD5), then with the countermeasure applied
(salted bcrypt) and compares the results.

## Requirements

- Python 3.9+
- `pip install bcrypt matplotlib jupyter`

## Running the demo

```
jupyter notebook dictionary_attack_demo.ipynb
```

Run all cells top to bottom (Cell → Run All). Expect the run to take about
1–2 minutes total - the bcrypt sweep is deliberately slow (that's the point of the
countermeasure, see below). The notebook will:

1. Generate a mock database of user accounts with a mix of weak/medium/strong
   passwords.
2. Hash that database two ways (MD5 and bcrypt) from the same underlying passwords.
3. Sweep a dictionary attack across increasing wordlist sizes against each.
4. Plot % of accounts cracked vs. wordlist size, and attack time vs. hash algorithm.
5. Print a summary comparing MD5 and bcrypt.

## Files

- `dictionary_attack_demo.ipynb`: the simulation, attack, and plots.
- `wordlist.txt`: base list of common passwords used to seed both the mock
  database's weak/medium passwords and the attacker's dictionary.
- `README.md`: this file.

## Adjustable parameters

All in the "Parameters" cell near the top of the notebook:

- `num_accounts`: number of simulated user accounts.
- `password_strength`: weak/medium/strong distribution across those accounts.
- `wordlist_sizes`: candidate counts to sweep, per hash algorithm.
- `hash_algorithms`: which algorithms to run (`md5`, `bcrypt`).
- `bcrypt_cost`: bcrypt work factor (higher = slower per hash).

## Note on wordlist sizes

`wordlist_sizes["bcrypt"]` is intentionally much smaller than
`wordlist_sizes["md5"]` (hundreds vs. tens of thousands of candidates). This isn't a
shortcut, it's the point being demonstrated: MD5 has no salt, so the attacker can
build a `hash -> username` lookup table once and test each candidate in O(1), while
bcrypt embeds a random salt in every hash, forcing a slow, individual
`bcrypt.checkpw` comparison per candidate per account. Sweeping bcrypt to the same
sizes as MD5 would take hours instead of seconds. For the same reason,
`bcrypt_cost` defaults to 10 rather than the more typical production value of 12 —
raise it to see an even starker (but slower to run) gap.
