# Crypto

- [Cyber Chef](https://gchq.github.io/CyberChef/)
- [substitution cipher](https://www.guballa.de/substitution-solver)
- [vigenere](https://www.guballa.de/vigenere-solver)
- [ceasar](https://cryptii.com/pipes/caesar-cipher)
- ``hashid <hash>``: detect hash type

## Hash cracking
- Extract hash from password protected file with ``<proto/ext>2john`` e.g. ``zip2john`` and ``ssh2john``
- Crack unsalted hashes with [CrackStation](https://crackstation.net/)

```
john -w <wordlist> hash.txt

hashcat -a 0 -m <hash-type> hash.txt <wordlist>
hashcat -a 3 -m <hash-type> hash.txt <pattern>
```
