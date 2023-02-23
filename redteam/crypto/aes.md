# AES

### AES-GCM

AES-GCM is insecure when called twice with the same nonce on distinct messages. This can leak plaintext data and reveal the authentication key GMAC uses.

