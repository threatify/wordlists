# wordlists

[![updated](https://img.shields.io/github/last-commit/threatify/wordlists?label=updated&style=flat-square)](https://github.com/threatify/wordlists/commits/main)
[![license](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)

Subdomain wordlists ranked by real-world use. Every entry is a single DNS label (the `api` in
`api.example.com`), taken from more than 6.5 billion observed subdomains and ordered by how many
different domains use it. Single labels only, with no dotted multi-level strings, and nothing
seen on fewer than 10 domains.

Made for subdomain brute-forcing with [puredns](https://github.com/d3mondev/puredns),
[shuffledns](https://github.com/projectdiscovery/shuffledns), [massdns](https://github.com/blechschmidt/massdns)
and similar tools. Pair them with fresh resolvers from [threatify/resolvers](https://github.com/threatify/resolvers).

## Files

| File | Labels | Use it for |
|------|-------:|------------|
| [`threatify_subdomains_10k.txt`](threatify_subdomains_10k.txt) | 10,000 | Quick checks, many targets |
| [`threatify_subdomains_100k.txt`](threatify_subdomains_100k.txt) | 100,000 | Everyday brute-forcing: the best yield per query |
| [`threatify_subdomains_500k.txt`](threatify_subdomains_500k.txt) | 500,000 | Thorough scans |
| [`threatify_subdomains_1m.txt`](threatify_subdomains_1m.txt) | 940,010 | Everything seen on at least 10 domains |
| [`threatify_subdomains_counts.tsv`](threatify_subdomains_counts.tsv) | 940,010 | `rank`, `label`, `domains`: how many domains use each label, to cut your own size |

Each list is the top of the next one, so the 100k list is the first 100,000 lines of the 500k list.

## Usage

```sh
curl -sO https://raw.githubusercontent.com/threatify/wordlists/main/threatify_subdomains_100k.txt
curl -sO https://raw.githubusercontent.com/threatify/resolvers/main/resolvers.txt
curl -sO https://raw.githubusercontent.com/threatify/resolvers/main/resolvers-trusted.txt

puredns bruteforce threatify_subdomains_100k.txt example.com -r resolvers.txt --resolvers-trusted resolvers-trusted.txt
```

## How well they find new subdomains

We built the lists on 2026-11-01. Our scanners then visited 49,970 domains over the next 11
hours, and we collected every subdomain on them that was first seen after the build. That gave
34,702 names on 7,741 domains, at most 20 per domain, all one level below the domain (what a
brute-force guesses).

They were found by passive enumeration only (Certificate Transparency logs and other public
sources). No wordlist was involved in finding them.

The table shows the share each list contains, ±1.2 points at 95% confidence. The n0kovo lists
are cut to the same sizes.

| List size | threatify | n0kovo large | n0kovo huge |
|---|---:|---:|---:|
| 10k | **48.4%** | 42.4% | 43.8% |
| 100k | **57.3%** | 49.0% | 52.3% |
| 500k | **62.4%** | 56.4% | 57.7% |
| 940k | **64.3%** | 57.8% | 59.1% |

- **Per domain:** with each domain counted once, the 100k list covers 74.0%, against 66.2%
  (n0kovo large) and 69.5% (n0kovo huge) at the same size.
- **Size:** the 100k list covers about as much as n0kovo large at 940k (57.3% vs 57.8%, within
  the margin), with about a tenth of the DNS queries.
- **What this does not measure:** names that appear in no public source and that only a
  brute-force can find. The test shows how well each list predicts real labels.

## How the lists are built

- **Source:** more than 6.5 billion subdomains collected from Certificate Transparency logs and
  other public sources (November 2026).
- **Ranking:** each label is ranked by how many different domains use it (the `domains` column of
  `threatify_subdomains_counts.tsv`). Labels used by fewer than 10 domains are left out.
- **Clean entries:** single labels only, with no dots. IP-like and hex labels are rare
  (under 0.02%) and stay in only because 10 or more domains use them.
- **Sizes:** the 10k, 100k and 500k lists are cuts of the same ranking.

The lists are rebuilt from new data from time to time; the commit history shows when.

## Disclaimer

These lists are provided "as is", without warranty of any kind, under the [MIT License](LICENSE).
They are meant for security testing that you are authorized to do, such as your own assets or a
bug bounty program's scope. You are responsible for how you use them. The authors are not
liable for any misuse or damage.
