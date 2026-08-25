# STIR/SHAKEN Certificate Dataset

This repository contains the STIR/SHAKEN (S/S) X.509 certificates collected as part of our longitudinal measurement study:

**Characterizing the Deployment of the STIR/SHAKEN Telephone Network PKI**  
ACM Internet Measurement Conference (IMC), 2026.

## What is STIR/SHAKEN?

STIR/SHAKEN is the caller authentication framework deployed in the U.S. telephone network to authenticate caller identity information.

When originating a call, an **Originating Service Provider (OSP)** creates a cryptographically signed token called a **PASSporT**. The PASSporT contains information about the call, including:

- the originating telephone number,
- the destination telephone number,
- the attestation level (`A`, `B`, or `C`),
- the issuance timestamp, and
- an origination identifier (`origid`).

The signed PASSporT is carried in the SIP `Identity` header as the call traverses the telephone network.

At the receiving end, a **Terminating Service Provider (TSP)** verifies the PASSporT before completing the call.

## Where Do Certificates Fit?

STIR/SHAKEN relies on a Public Key Infrastructure (PKI).

Each OSP signs its PASSporTs using a private key. The corresponding public key is distributed through an **X.509 certificate** issued by a STIR/SHAKEN Certificate Authority (CA).

The PASSporT identifies the certificate through the `x5u` field in its header:

```json
{
  "alg": "ES256",
  "ppt": "shaken",
  "typ": "passport",
  "x5u": "https://certificates.example.com/shaken.pem"
}
```

During verification, the TSP:

1. extracts the certificate URI from the PASSporT,
2. retrieves the X.509 certificate,
3. validates the certificate and its trust chain, and
4. uses the public key in the certificate to verify the PASSporT signature.

Certificates are therefore a central component of the STIR/SHAKEN trust infrastructure.

## Dataset

Our study analyzed **7,749,934 calls** collected by two telephone honeypot systems between **November 2021 and February 2026**.

Across our measurement, we collected **13,861 unique STIR/SHAKEN X.509 certificates** observed through certificate URLs associated with calls in our dataset.

For the Robocall Observatory, we began retrieving certificates during live call processing in **March 2025**. We also processed historical calls by retrieving certificates from previously observed certificate URLs during retrospective analysis.

Certificate URLs are not necessarily persistent. Providers may rotate, replace, or remove certificates over time. As a result, the certificate currently available at a URL may differ from the certificate that was available when a historical call was received.

This repository preserves the certificates observed during our measurement.

## Repository Contents

The collected certificates are available under:

```text
certificates/
├── ...
└── ...
```

Each file represents an X.509 certificate observed through the STIR/SHAKEN infrastructure during our measurement.

## Inspecting a Certificate

The contents of a PEM-encoded certificate can be inspected using OpenSSL:

```bash
openssl x509 -in certificate.pem -text -noout
```

For a DER-encoded certificate:

```bash
openssl x509 -inform DER -in certificate.der -text -noout
```

The decoded output includes information such as:

- certificate version and serial number,
- signature algorithm,
- issuer,
- validity period,
- subject,
- public key,
- X.509 extensions,
- STIR/SHAKEN-specific authorization information, and
- certificate signature.

Example output:

```text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: ...
        Signature Algorithm: ...
        Issuer: ...
        Validity
            Not Before: ...
            Not After : ...
        Subject: ...
        Subject Public Key Info:
            ...
        X509v3 extensions:
            ...
```

## Data Scope

This repository contains **certificates only**. It does not include the underlying call signaling, telephone numbers, PASSporTs, call audio, or other call-level data used in our study.

The certificates were publicly retrievable through certificate URLs exposed by STIR/SHAKEN participants as part of normal call verification.

## Associated Paper

**Characterizing the Deployment of the STIR/SHAKEN Telephone Network PKI**  
ACM Internet Measurement Conference (IMC), 2026.
