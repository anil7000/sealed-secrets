# SealedSecret troubleshooting and recovery checklist

Use this guide when a SealedSecret is accepted by Kubernetes but the expected
Secret is absent or cannot be decrypted.

## Check scope and certificate first

Read the [README](README.md) sections on scopes, certificates and validation.

| Check | Why it matters |
| --- | --- |
| Controller cluster and namespace | A different controller may have different sealing keys |
| Sealing certificate | An offline certificate must belong to the intended controller |
| Secret name and namespace | Default strict scope binds ciphertext to both |
| Explicit broader scope | Namespace-wide or cluster-wide scope changes the trust boundary |
| Controller status and events | Admission success is not proof of successful decryption |

Do not solve a strict-scope mismatch by editing metadata or widening scope
without reviewing the security impact. Reseal for the intended destination.

## Validate without exposing plaintext

With kubeseal installed, the correct kubeconfig context selected, and an existing
encrypted file named sealedsecret.yaml:

```sh
kubeseal --validate < sealedsecret.yaml
```

This Bash/WSL example contacts the controller; it is not an offline check.
Configure controller name/namespace flags for non-default installations.
Success checks decryptability, not whether the application will accept the
credential. Do not print the resulting Kubernetes Secret to prove success.

## Recovery preparation

- Keep sealing-key backups in an approved restricted backup system, not Git.
- Test recovery in an isolated environment before relying on the backup.
- Treat losing private sealing keys as a recovery incident.
- Distinguish sealing-key renewal from rotating an application's credential.
- If a credential is exposed, rotate the actual credential; resealing the same
  value does not revoke it.

Keep only encrypted manifests and non-sensitive metadata in the repository.
Scrub events and logs before sharing externally.

## Attribution

Upstream code,
licenses and contributor attribution remain unchanged.
