# Obfuscation

### Obfuscating Binaries

The best way to prevent detection is to use custom payloads. To avoid detection with existing ones you can use a custom crypter that loads & decrypts shellcode.

Other than that there are some projects that aim at automating this process:

* [https://github.com/EgeBalci/sgn](https://github.com/EgeBalci/sgn)

### Obfuscation Powershell

#### [Powerob](https://github.com/cwolff411/powerob):

```text
python3 powerob.py obfuscate originalfile.ps1 obfuscatedfile.ps1
```

