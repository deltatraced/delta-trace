![Pasted image 20250629030032.png](../../../../../../../../../../attachments/Pasted%20image%2020250629030032.png)

# 1 Journal

2025-06-29 Wk 26 Sun - 03:47

````sh
# /home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/crypto-numerology
ls -al               
total 500
drwxrwxr-x 3 lan lan   4096 Jun 29 03:47 .
drwxrwxr-x 3 lan lan   4096 Jun 29 03:47 ..
-rw-r--r-- 1 lan lan 484368 Dec 31  1979 ctf_challenge_package.json
-rw-r--r-- 1 lan lan     62 Dec 31  1979 flag.txt
-rw-r--r-- 1 lan lan   1180 Dec 31  1979 init.sh
drwxrwxr-x 2 lan lan   4096 Jun 29 03:47 __pycache__
-rw-r--r-- 1 lan lan    135 Dec 31  1979 readme.md
````

[blog on reading markdown in terminal](https://opensource.com/article/20/3/markdown-apps-linux-command-line).

````sh
snap install mdless
````

````sh
cat readme.md | mdless

# output
I have created a bunch of message/ciphertext pairs with this new technique.

I gave you the "key" to decrypt the flag, is that enough?
````

<a name="readme" />^readme

````sh
chmod +x init.sh
./init.sh             

# output
ERROR: secrets.env not found. Please run set_secrets.sh first.
````

````sh
ls -al __pycache__                                   
total 28
drwxrwxr-x 2 lan lan  4096 Jun 29 03:57 .
drwxrwxr-x 3 lan lan  4096 Jun 29 03:54 ..
-rw-r--r-- 1 lan lan 18108 Dec 31  1979 crypto_numerology.cpython-312.pyc
````

## 1.1 Checking for clues on ChaCha20

2025-06-29 Wk 26 Sun - 16:14

My teammate said they saw this somewhere here...

Let's check the clues again...

The [^readme](003%20NUMEROLOGY.md#readme) clues that this is a "new technique".

````sh
cat init.sh

# output
# Example for your script that runs init_cryptanalysis.py
# (Ensure set_secrets.sh has run and created secrets.env)

if [ ! -f "secrets.env" ]; then
    echo "ERROR: secrets.env not found. Please run set_secrets.sh first."
    exit 1
fi
source secrets.env # Load SECRET_TARGET_NONCE_HEX, SECRET_TARGET_COUNTER_INT, KNOWN_KEY_ACTIVE_MATERIAL_HEX, KNOWN_KEY_STRUCTURE

if [ ! -f "flag.txt" ]; then
    echo "ERROR: flag.txt not found."
    exit 1
fi
FLAG_STRING=$(cat flag.txt)

OUTPUT_PKG_FILE="./ctf_challenge_package.json" # This is what your linear_cryptanalysis.sh reads
ROUNDS=1
MESSAGE_SIZE=64
NUM_NONCE_VARS=32
NUM_COUNTER_VARS=32

echo "Running init_cryptanalysis.py to generate challenge package..."
python3 crypto_numerology.py \
    --output_file "${OUTPUT_PKG_FILE}" \
    --flag_string "${FLAG_STRING}" \
    --rounds ${ROUNDS} \
    --message_size_bytes ${MESSAGE_SIZE} \
    --known_key_active_material_hex "${KNOWN_KEY_ACTIVE_MATERIAL_HEX}" \
    --secret_target_nonce_hex "${SECRET_TARGET_NONCE_HEX}" \
    --secret_target_counter_int ${SECRET_TARGET_COUNTER_INT} \
    --num_nonce_variations ${NUM_NONCE_VARS} \
    --num_counter_variations ${NUM_COUNTER_VARS} \
````

The message size is 64 bytes... consistent with our [diagnostics](003%20NUMEROLOGY.md#json-schema)

There are 32 samples as well...

And what looks like a discrepancy of `crypto_numerology.py` \<-> `init_cryptoanalysis.py` \<-> `linear_cryptanalysis.sh`

We have confirmation that this uses CHACHA within the puzzle itself, as my teammate said:

````sh
strings __pycache__/crypto_numerology.cpython-312.pyc | less

# output
[...]
CHACHA_CONSTANTSr
[...]
````

# 2 Investigations

## 2.1 What can be done with a pycache file?

https://docs.kanaries.net/topics/Python/pycache

(LLM chatgpt-4o)

````sh
pip install uncompyle6
uncompyle6 __pycache__/example.cpython-38.pyc
````

https://pypi.org/project/pyhidra/

(/LLM chatgpt-4o)

Ghidra is not able to load the pyc file

````sh
uncompyle6 __pycache__/crypto_numerology.cpython-312.pyc                                                                                  

# output
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
Traceback (most recent call last):
  File "/home/lan/miniconda3/bin/uncompyle6", line 5, in <module>
    from uncompyle6.bin.uncompile import main_bin
  File "/home/lan/miniconda3/lib/python3.13/site-packages/uncompyle6/bin/uncompile.py", line 19, in <module>
    from uncompyle6.verify import VerifyCmpError
  File "/home/lan/miniconda3/lib/python3.13/site-packages/uncompyle6/verify.py", line 24, in <module>
    import xdis.std as dis
  File "/home/lan/miniconda3/lib/python3.13/site-packages/xdis/std.py", line 281, in <module>
    _std_api = make_std_api()
  File "/home/lan/miniconda3/lib/python3.13/site-packages/xdis/std.py", line 278, in make_std_api
    return _StdApi(python_version, variant)
  File "/home/lan/miniconda3/lib/python3.13/site-packages/xdis/std.py", line 80, in __init__
    self.opc = opc = get_opcode_module(python_version, variant)
                     ~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/home/lan/miniconda3/lib/python3.13/site-packages/xdis/op_imports.py", line 212, in get_opcode_module
    return op_imports[canonic_python_version[vers_str]]
                      ~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^
KeyError: '3.13'
````

To install pyhidra for ghidra,

````sh
GHIDRA_INSTALL_DIR=/home/lan/Downloads/ghidra_11.4_PUBLIC pyhidra
````

restart ghidra

![Pasted image 20250629041710.png](../../../../../../../../../../attachments/Pasted%20image%2020250629041710.png)

![Pasted image 20250629041725.png](../../../../../../../../../../attachments/Pasted%20image%2020250629041725.png)

Still can't load the pyc.

2025-06-29 Wk 26 Sun - 04:19

https://stackoverflow.com/questions/5287253/is-it-possible-to-decompile-a-compiled-pyc-file-into-a-py-file

[decompyle3](https://github.com/rocky/python-decompile3) i

````
pip install decompyle3
````

````sh
decompyle3 __pycache__/crypto_numerology.cpython-312.pyc 
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
I don't know about Python version '3.13' yet.
Python versions 3.9 and greater are not supported.
/home/lan/miniconda3/lib/python3.13/site-packages/click/core.py:1193: UserWarning: The parameter --version is used more than once. Remove its duplicate as parameters should be unique.
  parser = self.make_parser(ctx)
/home/lan/miniconda3/lib/python3.13/site-packages/click/core.py:1186: UserWarning: The parameter --version is used more than once. Remove its duplicate as parameters should be unique.
  self.parse_args(ctx, args)
# decompyle3 version 3.9.2
# Python bytecode version base 3.12.0 (3531)
# Decompiled from: Python 3.13.2 | packaged by Anaconda, Inc. | (main, Feb  6 2025, 18:56:02) [GCC 11.2.0]
# Embedded file name: ./crypto_numerology.py
# Compiled at: 2025-06-06 19:54:22
# Size of source mod 2**32: 15944 bytes

Unsupported Python version, 3.12.0, for decompilation


# Unsupported bytecode in file __pycache__/crypto_numerology.cpython-312.pyc
# Unsupported Python version, 3.12.0, for decompilation

````

## 2.2 Format of challenge_package.json

````
{
    "cipher_parameters": {
        "key": "000000005c5470020000000031f4727bf7d4923400000000e7bbb1c900000000",
        "common_plaintext": "9de16236ae1521cffe67ab68fd1325951b2a1b11b75bec946325faca2a8db02a013b9c18ddd31f168e6dd472ebccdfc7d92c85e96546e822dd7002a2691f9392"
    },
    "learning_dataset_for_player": [
        {
            "sample_id": "sample_n0_c0",
            "plaintext_hex": "9de16236ae1521cffe67ab68fd1325951b2a1b11b75bec946325faca2a8db02a013b9c18ddd31f168e6dd472ebccdfc7d92c85e96546e822dd7002a2691f9392",
            "ciphertext_hex": "d4922d0bae1521cffe67ab68fd132595c4942d350ff30c906325faca486555dc2ac805afddd31f16401ab7e1ebccdfc7e765f6a76746e822dd7002a2691f9392",
            "nonce_hex": "010000000000000000000000",
            "counter_int": 1
        },
        {
            "sample_id": "sample_n0_c1",
            "plaintext_hex": "9de16236ae1521cffe67ab68fd1325951b2a1b11b75bec946325faca2a8db02a013b9c18ddd31f168e6dd472ebccdfc7d92c85e96546e822dd7002a2691f9392",
            "ciphertext_hex": "d4922d7bae1521cffe67ab68fd132595fcacadb20ff30c906325faca486555dc3ac806aeddd31f16401ab7e1ebccdfc79665f6a46746e822dd7002a2691f9392",
            "nonce_hex": "010000000000000000000000",
            "counter_int": 2
        },
...
    ],
    "flag_ciphertext": "692f09e677335f6152655f67304e6e40141fa702e7e5b95b46756e63298d80a9bcbbd95465795f21ef0a"
}

````

2025-06-29 Wk 26 Sun - 11:39

````json
{
	"sample_id": "sample_n31_c30",
	"plaintext_hex": "9de16236ae1521cffe67ab68fd1325951b2a1b11b75bec946325faca2a8db02a013b9c18ddd31f168e6dd472ebccdfc7d92c85e96546e822dd7002a2691f9392",
	"ciphertext_hex": "d4922d07ae1521cffe67ab68fd132595c29a8d550ff30c906325faca486555dc1e08c5a0ddd31f16401ab7e1ebccdfc7e865b6666546e822dd7002a2691f9392",
	"nonce_hex": "000000800000000000000000",
	"counter_int": 1073741824
},
````

What do the different fields mean? `nonce_hex` is just basically the same for everything.

We have the plaintext, the ciphertext, and the key

````
"key": "000000005c5470020000000031f4727bf7d4923400000000e7bbb1c900000000",
````

---

2025-06-29 Wk 26 Sun - 11:42

(LLM chatgpt-4o)

|JSON field|what it literally is|why it’s interesting|
|----------|--------------------|--------------------|
|`sample_id`|just a label (`sample_n31_c30`)|n-value and c-value are just bookkeeping; you can ignore it once you know which row you’re looking at|
|`plaintext_hex`|the original data **before** encryption, written in hex|if you XOR this with the ciphertext you recover the cipher’s keystream for *this one block*|
|`ciphertext_hex`|the encrypted output for that same block|lets you verify the cipher or extract the keystream|
|`nonce_hex`|a 12-byte “number used once” (`00000080 00000000 00000000`)|ChaCha20 needs a nonce; all samples that share the same value are guaranteed to be in the **same stream**|
|`counter_int`|a 32-bit block counter (here `1 073 741 824`, which is `0x40000000`)|tells the cipher **which 64-byte block** of keystream was used|

* \`plaintext ⊕ keystream = ciphertext

ChaCha20 is a **stream cipher**. That means:

* It doesn’t encrypt in blocks like AES.
* Instead, it **generates a stream of pseudorandom bytes** called a **keystream**.
* You don’t need to hide the algorithm — all the security comes from the **secret key**.

````
# encrypt
Plaintext  ⊕  Keystream  =  Ciphertext
# decrypt
Ciphertext  ⊕  Keystream  =  Plaintext
````

Procedure

1. **Ask ChaCha20 to generate the keystream**, using the key, nonce, and counter.
1. **XOR the plaintext with the keystream.**
1. If the result = ciphertext → then yes, it’s ChaCha20.

ChaCha20 is **deterministic** — that means:

 > 
 > Same key + same nonce + same counter → always produces the same 64-byte output.

(/LLM chatgpt-4o)

(LLM chatgpt-4o)

On figuring out the algorithm from zero knowledge

You’re given some structured data (JSON) that looks like:

* Some kind of hex blobs labeled `plaintext` and `ciphertext`.
* A key — also in hex, 64 characters (32 bytes).
* A `nonce` — also hex, always 12 bytes.
* A number called `counter`.

To show schema:

````sh
jq 'def s: type as $t | if $t == "object" then with_entries(.value |= s) elif $t == "array" then map(s) | unique else $t end; s' ctf_challenge_package.json
````

Get Sizes of parameters
(/LLM chatgpt-4o)

Schema:

````sh
jq 'def s: type as $t | if $t == "object" then with_entries(.value |= s) elif $t == "array" then map(s) | unique else $t end; s' ctf_challenge_package.json
````

````json
{
  "cipher_parameters": {
    "key": "string",
    "common_plaintext": "string"
  },
  "learning_dataset_for_player": [
    {
      "sample_id": "string",
      "plaintext_hex": "string",
      "ciphertext_hex": "string",
      "nonce_hex": "string",
      "counter_int": "number"
    }
  ],
  "flag_ciphertext": "string"
}
````

<a name="json-schema" />^json-schema

Size of the key:

````sh
# no need to tr -d '\n', output invariant.
cat ctf_challenge_package.json | jq .cipher_parameters.key | xargs echo -n | wc -c

# output
64
````

Size of the nonce:

````sh
cat ctf_challenge_package.json | jq ".learning_dataset_for_player.[0].nonce_hex" | xargs echo | tr -d '\n' | wc -c

# output
24
````

Size of plaintext

````sh
cat ctf_challenge_package.json | jq ".learning_dataset_for_player.[0].plaintext_hex" | xargs echo | tr -d '\n' |  wc -c

# output
128
````

Size of ciphertext:

````sh
cat ctf_challenge_package.json | jq ".learning_dataset_for_player.[0].ciphertext_hex" | xargs echo | tr -d '\n' | wc -c

# output
128
````

Size of flag ciphertext:

````sh
cat ctf_challenge_package.json | jq ".flag_ciphertext" | xargs echo | tr -d '\n' | wc -c

# output
84
````

Size of common plaintext:

````sh
cat ctf_challenge_package.json | jq ".cipher_parameters.common_plaintext" | xargs echo | tr -d '\n' | wc -c

# output
128
````

2025-06-29 Wk 26 Sun - 12:16

The counts are in characters. Each byte would make two characters, so 64 characters is a 32-bit token.

So we have an encryption scheme with a

* 32-byte key 12-byte nonce 64-byte text

DDGing "encryption with 32-byte key 12-byte nonce 64-byte text", you do find in the third result, `ChaCha20`.

![Pasted image 20250629122424.png](../../../../../../../../../../attachments/Pasted%20image%2020250629122424.png)

## 2.3 On the ChaCha20 Encryption Algorithm

## 2.4 On Endianness of byte streams

2025-06-29 Wk 26 Sun - 15:38

byte strings can be little endian, or big endian.

Definition:

 > 
 > Little Endian Order means the **least significant byte** is **stored first** in memory (at the lowest address).
 > Big Endian Order means the **most significant byte** is **stored first** in memory (at the lowest address).

Depending on our choice, how we interpret byte strings changes.

Let's say we have a `u32`: `0xDEADFEED`.

In little endian, this would encode the following byte string:

````
ED FE AD DE
````

On big endian, this would encode the following byte string:

````
DE AD FE ED
````

It could be thought of as writing the byte strings in reverse order. So endianness can be tested for in systems by reversing byte strings.

Reversing in the shell:

````sh
echo deadfeed | fold -w2 | tac | tr -d '\n'

# output
edfeadde
````

<a name="ex-fold-tac" />^ex-fold-tac

(LLM chatgpt-4o)

* Suggestion of `fold -w2` and `tac` combination
  (/LLM chatgpt-4o)

---

(LLM chatgpt-4o)

|what you’ll use it for|tool / package|why it matters|
|----------------------|--------------|--------------|
|**pull values out of the JSON quickly**|`jq`|one-liner to grab the key, nonce or counter without writing Python|
|**turn hex strings ↔︎ raw bytes**|`xxd` *(or)* `hexdump`|converts the JSON’s hex fields to real bytes and back again ([man7.org](https://man7.org/linux/man-pages/man1/hexdump.1.html?utm_source=chatgpt.com "hexdump(1) - Linux manual page - man7.org"))|
|**run the actual cipher**|`openssl` (≥ 1.1.1 / 3.x)|has a built-in **`enc -chacha20`** and **`enc -chacha20-poly1305`** target so you can encrypt/decrypt or just generate keystream blocks from the shell ([docs.openssl.org](https://docs.openssl.org/3.3/man1/openssl-enc/?utm_source=chatgpt.com "openssl-enc"))|
||`hpenc` (GitHub, package name usually the same)|a tiny high-speed CLI that supports ChaCha20-Poly1305 and AES-GCM with simple flags — handy when you want authenticated encryption without OpenSSL’s quirks ([github.com](https://github.com/vstakhov/hpenc?utm_source=chatgpt.com "vstakhov/hpenc - fast encryption command line tool - GitHub"))|
||`age` (Filippo Valsorda’s *age*)|modern file-encryption tool that uses **XChaCha20-Poly1305** under the hood; good for sanity-checking that your key/nonce sizes look right, even if you don’t use it to decrypt the challenge ([reddit.com](https://www.reddit.com/r/crypto/comments/nbjq71/chachapoly_from_command_line/?utm_source=chatgpt.com "ChaCha-Poly from command line : r/crypto - Reddit"))|
||`libsodium-utils` *(sometimes packaged as `sodium-cli`)*|command-line front-end to the libsodium library; exposes raw **crypto_stream_chacha20** and AEAD functions, so you can generate or XOR keystream bytes without writing C ([doc.libsodium.org](https://doc.libsodium.org/?utm_source=chatgpt.com "Introduction | libsodium"))|
|**quick XORs and checksum work**|`xor` (from the *moreutils* package) or even `openssl xor -inkey…`|lets you combine plaintext & ciphertext blocks to see the keystream directly|
|**optional niceties**|`base64`, `od`, `tr`|common helpers when inputs are in other encodings or you need pretty printing|

* [ ] Not Fact checked

**`openssl enc -chacha20`** (or \*\*`hpenc` / libsodium’s CLI) to push data through the cipher or generate keystream.

(/LLM chatgpt-4o)

# 3 Diagnostics

## 3.1 Prove that the algorithm we have is ChaCha20

(LLM chatgpt-4o)

We’ll test this statement:

 > 
 > “If I run ChaCha20 using the given key, nonce, and counter, and encrypt the known plaintext, I should get exactly the ciphertext from the sample.”

 > 
 > We’ll use Python + PyCryptodome because it allows you to set the **counter directly** (OpenSSL can’t do that easily for ChaCha20).

* [ ] Not Fact Checked

````sh
pip install pycryptodome
````

in `scripts/verify_chacha20.py`:

````python
#!/usr/bin/env python3
"""
verify_chacha20.py

Check that   ChaCha20_IETF(key, nonce, counter, plaintext)
produces the expected ciphertext.

Exit status 0  ⟶  match
Exit status 1  ⟶  mismatch or parse error
"""

import argparse, sys
from binascii import unhexlify, Error as HexError
from Crypto.Cipher import ChaCha20   # pip install pycryptodome

import json

def to_obj_json(obj, indent=2):
    def safe(o):
        try:
            return o.__dict__
        except AttributeError:
            return repr(o)  # fallback: just string representation

    return json.dumps(obj, default=safe, indent=indent, sort_keys=True)




def parse_hex(label: str, s: str, expected_len: int | None = None) -> bytes:
    try:
        b = unhexlify(s)
    except HexError as e:
        sys.exit(f"[error] {label}: {e}")
    if expected_len is not None and len(b) != expected_len:
        sys.exit(f"[error] {label}: expecting {expected_len} bytes, got {len(b)}")
    return b

def main():
    ap = argparse.ArgumentParser(
        description="Verify a single ChaCha20‐IETF block")
    ap.add_argument("--key",         required=True, help="32-byte hex string")
    ap.add_argument("--nonce",       required=True, help="12-byte hex string")
    ap.add_argument("--counter",     required=True, type=int,
                    help="32-bit block counter")
    ap.add_argument("--plaintext",   required=True, help="64-byte hex string")
    ap.add_argument("--ciphertext",  required=True, help="64-byte hex string")
    args = ap.parse_args()

    key        = parse_hex("key",        args.key,       32)
    nonce      = parse_hex("nonce",      args.nonce,     12)
    plaintext  = parse_hex("plaintext",  args.plaintext, 64)
    ciphertext = parse_hex("ciphertext", args.ciphertext,64)

    print("key", key)
    print("nonce", nonce)
    print("plaintext", plaintext)
    print("ciphertext", ciphertext)
    print("counter", args.counter)

    cipher = ChaCha20.new(key=key, nonce=nonce)
    cipher.seek(args.counter * 64)
    test_ct = cipher.encrypt(plaintext)

    print("cipher", to_obj_json(cipher))
    print("test_ct", test_ct)

    if test_ct == ciphertext:
        print("✅  Match: parameters reproduce ciphertext (ChaCha20 confirmed)")
        sys.exit(0)
    else:
        print("❌  Mismatch: parameters do NOT reproduce ciphertext")
        sys.exit(1)

if __name__ == "__main__":
    main()
````

<a name="script-match-check-1" />^script-match-check-1

````sh
python -c "import Crypto, sys; print(Crypto.__version__)"

# output
3.23.0
````

* Use `jq -r` to remove whitespace, etc just in case. Instead of `| xargs echo | tr -d '\n'`

(/LLM chatgpt-4o)

````sh
chmod +x ~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/verify_chacha20.py
````

````sh
~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/verify_chacha20.py \
	--key "$(cat ctf_challenge_package.json | jq -r .cipher_parameters.key)" \
	--nonce "$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[0].nonce_hex")" \
	--counter "$(cat ctf_challenge_package.json | jq -r  ".learning_dataset_for_player.[0].counter_int")" \
	--plaintext "$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[0].plaintext_hex")" \
	--ciphertext "$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[0].ciphertext_hex")"
````

This fails.

The [C source](https://github.com/Legrandin/pycryptodome/blob/master/src/chacha20.c) for ChaCha20 encryption for [pycryptodome](https://github.com/Legrandin/pycryptodome). Here is the [python](https://github.com/Legrandin/pycryptodome/blob/master/lib/Crypto/Cipher/ChaCha20.py).

(LLM chatgpt-4o)
Your “ground-truth” code shows PyCryptodome’s ChaCha20 uses the **standard** 20-round core and the IETF 12-byte nonce layout. Since that *doesn’t* match your sample, the encryption in the JSON is either:

* a **different ChaCha variant**, or
* **not ChaCha at all**.

Exploring those two branches with the XOR-keystream method above will tell you which—*and that* is the rigorous way to move from “guess” to “proof.”
(/LLM chatgpt-4o)

* [ ] Not Fact checked

2025-06-29 Wk 26 Sun - 13:10

I should do something about obsidian, adding tabs instead of spaces and python not being happy.

2025-06-29 Wk 26 Sun - 13:34

(LLM chatgpt-4o)
For XOR,

````sh
sudo apt install moreutils
````

xor.py:

````python
#!/usr/bin/env python3
"""
xor.py  –  XOR the contents of two files/streams.
usage: xor.py FILE1 FILE2         (use "-" for stdin)
prints raw XOR bytes to stdout
"""

import sys, os

def read_all(source):
    if source == "-":                      # read from stdin
        return sys.stdin.buffer.read()
    with open(source, "rb") as fh:        # read from named file/pipe
        return fh.read()

def main():
    if len(sys.argv) != 3:
        sys.stderr.write("Usage: xor.py FILE1 FILE2   (use - for stdin)\n")
        sys.exit(1)

    a = read_all(sys.argv[1])
    b = read_all(sys.argv[2])

    out = bytes(x ^ y for x, y in zip(a, b))
    sys.stdout.buffer.write(out)

if __name__ == "__main__":
    main()
````

Will drop into `~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py`:

````sh
chmod +x ~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py
````

Example usage:

````sh
~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py <(echo 02000000 | xxd -r -p) <(echo 00005000 | xxd -r -p) | xxd -p

# output
02005000
````

(/LLM chatgpt-4o)

(LLM chatgpt-4o)

Because XOR is its own inverse, the three algebraic identities are always true:

````
plaintext   ⊕  ciphertext   = keystream
plaintext   ⊕  keystream    = ciphertext
ciphertext  ⊕  keystream    = plaintext
````

* [ ] Prove mathematically

The process for the ChaCha20 cipher:

````rust
// ChaCha20 is a keystream generator
let keystream = cipher(key, nonce, counter)
let plaintext = xor(ciphertext, keystream)
````

````sh
# pull the two hex blobs straight out of the JSON
pt_hex=$(jq -r '.learning_dataset_for_player[0].plaintext_hex'   ctf_challenge_package.json)
ct_hex=$(jq -r '.learning_dataset_for_player[0].ciphertext_hex'  ctf_challenge_package.json)

# compute keystream and show it as hex – all in a single pipeline
~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$pt_hex" | xxd -r -p) \
    <(echo "$ct_hex" | xxd -r -p) \
| xxd -p
````

````
49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8
e5f62bf399b700000000ce776393000000003e49734e0200000000000000
00000000
````

 > 
 > “If any cipher, with the given key/nonce/counter, produces this exact byte sequence — that’s the cipher.”

(/LLM chatgpt-4o)

2025-06-29 Wk 26 Sun - 13:56

So let's rewrite the earlier [script](003%20NUMEROLOGY.md#script-match-check-1) but this time just for producing the keystream:

We will put this in `~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/chacha20.py`

(LLM chatgpt-4o)
Because ChaCha20 is a **keystream cipher**, encrypting an all-zero input gives you the raw keystream bytes

````
ciphertext = plaintext XOR keystream
⟹
keystream  = ciphertext XOR plaintext
⟹
keystream  = cipher.encrypt(0x00…00)
````

````python
keystream = cipher.encrypt(b"\x00" * len(plaintext))
````

(/LLM chatgpt-4o)

````python
#!/usr/bin/env python3
"""
chacha20.py

Produces the keystream given the cipher parameters
"""

import argparse, sys
from binascii import unhexlify, Error as HexError
from Crypto.Cipher import ChaCha20   # pip install pycryptodome

import json

def to_obj_json(obj, indent=2):
    def safe(o):
        try:
            return o.__dict__
        except AttributeError:
            return repr(o)  # fallback: just string representation

    return json.dumps(obj, default=safe, indent=indent, sort_keys=True)


def parse_hex(label: str, s: str, expected_len: int | None = None) -> bytes:
    try:
        b = unhexlify(s)
    except HexError as e:
        sys.exit(f"[error] {label}: {e}")
    if expected_len is not None and len(b) != expected_len:
        sys.exit(f"[error] {label}: expecting {expected_len} bytes, got {len(b)}")
    return b


def main():
    ap = argparse.ArgumentParser(
        description="Verify a single ChaCha20‐IETF block")

    ap.add_argument("--key",         required=True, help="32-byte hex string")
    ap.add_argument("--nonce",       required=True, help="12-byte hex string")
    ap.add_argument("--counter",     required=True, type=int,
                                                    help="32-bit block counter")
    ap.add_argument("--plaintext",   required=True, help="64-byte hex string")
    ap.add_argument("--ciphertext",  required=True, help="64-byte hex string")
    args = ap.parse_args()

    key        = parse_hex("key",        args.key,       32)
    nonce      = parse_hex("nonce",      args.nonce,     12)
    plaintext  = parse_hex("plaintext",  args.plaintext, 64)
    ciphertext = parse_hex("ciphertext", args.ciphertext,64)

    cipher = ChaCha20.new(key=key, nonce=nonce)
    cipher.seek(args.counter * 64)

    # test_ct = cipher.encrypt(plaintext)

    keystream = cipher.encrypt(b"\x00" * len(plaintext))
    print(keystream.hex())


if __name__ == "__main__":
    main()
````

````sh
chmod +x /home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/chacha20.py
````

Now let's use it.

````sh
~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/chacha20.py \
	--key "$(cat ctf_challenge_package.json | jq -r .cipher_parameters.key)" \
	--nonce "$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[0].nonce_hex")" \
	--counter "$(cat ctf_challenge_package.json | jq -r  ".learning_dataset_for_player.[0].counter_int")" \
	--plaintext "$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[0].plaintext_hex")" \
	--ciphertext "$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[0].ciphertext_hex")"
````

````
0f7db32985dc8cf1295d0aac306b97d1921299baf84b38b990be4f5c0b4338a0bc920b1cf89c0628a5b89765cafe514e246704348b1d38dbc33ba724298ba6fc
````

This should be the keystream.

We now can check:

````
plaintext   ⊕  keystream    = ciphertext
ciphertext  ⊕  keystream    = plaintext
````

````sh
idx=0
key="$(cat ctf_challenge_package.json | jq -r .cipher_parameters.key)"
nonce="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].nonce_hex")"
counter="$(cat ctf_challenge_package.json | jq -r  ".learning_dataset_for_player.[$idx].counter_int")"
plaintext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].plaintext_hex")"
ciphertext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].ciphertext_hex")"

function test_s() {
	if [[ "$1" = "$2" ]]; then echo "[OK]"; else echo "[ER]"; fi
}

keystream=$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/chacha20.py \
	--key $key \
	--nonce $nonce \
	--counter $counter \
	--plaintext $plaintext \
	--ciphertext $ciphertext)

xor1="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$plaintext" | xxd -r -p) \
    <(echo "$keystream" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"

xor2="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$ciphertext" | xxd -r -p) \
    <(echo "$keystream" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"

echo ciphertext
echo $ciphertext
echo

echo plaintext
echo $plaintext
echo

echo keystream
echo $keystream
echo

echo expected ciphertext $(test_s $xor1 $ciphertext)
echo $xor1
echo

echo expected plaintext $(test_s $xor2 $plaintext)
echo $xor2
echo

echo sizes of xors
echo $xor1 | tr -d '\n' | wc -c
echo $xor2 | tr -d '\n' | wc -c
````

````
ciphertext
d4922d0bae1521cffe67ab68fd132595c4942d350ff30c906325faca486555dc2ac805afddd31f16401ab7e1ebccdfc7e765f6a76746e822dd7002a2691f9392

plaintext
9de16236ae1521cffe67ab68fd1325951b2a1b11b75bec946325faca2a8db02a013b9c18ddd31f168e6dd472ebccdfc7d92c85e96546e822dd7002a2691f9392

keystream
0f7db32985dc8cf1295d0aac306b97d1921299baf84b38b990be4f5c0b4338a0bc920b1cf89c0628a5b89765cafe514e246704348b1d38dbc33ba724298ba6fc

expected ciphertext [ER]
929cd11f2bc9ad3ed73aa1c4cd78b244893882ab4f10d42df39bb59621ce888abda99704254f193e2bd5431721328e89fd4b81ddee5bd0f91e4ba5864094356e

expected plaintext [ER]
dbef9e222bc9ad3ed73aa1c4cd78b2445686b48ff7b83429f39bb59643266d7c965a0eb3254f193ee5a2208421328e89c302f293ec5bd0f91e4ba5864094356e

sizes of xors
128
128
````

So we are still unable to match expectations.

(LLM chatgpt-4o)

LLM Suggests few issues with `chacha20.py`:

* JSON counter is 1-based, but we should be 0-based.
* `keystream = cipher.encrypt(b"\x00" * len(plaintext))` is unnecessarily referencing `plaintext`. We know the keystream is 64 bytes.
* We no longer take `plaintext` or `ciphertext` as input

````python
#!/usr/bin/env python3
"""
chacha20.py – emit the raw 64-byte ChaCha20-IETF keystream block
for the given *1-based* counter from the JSON samples.
"""

import argparse, sys
from binascii import unhexlify, hexlify, Error as HexError
from Crypto.Cipher import ChaCha20      # pip install pycryptodome


def hx(label, s, want_len):
    try:
        b = unhexlify(s)
    except HexError as e:
        sys.exit(f"[error] {label}: {e}")
    if len(b) != want_len:
        sys.exit(f"[error] {label}: need {want_len} bytes, got {len(b)}")
    return b


def main():
    p = argparse.ArgumentParser()
    p.add_argument("--key",     required=True)           # 64-hex
    p.add_argument("--nonce",   required=True)           # 24-hex
    p.add_argument("--counter", required=True, type=int) # 1-based
    a = p.parse_args()

    key   = hx("key",   a.key,   32)
    nonce = hx("nonce", a.nonce, 12)

    cipher = ChaCha20.new(key=key, nonce=nonce)
    cipher.seek((a.counter - 1) * 64)      # ← JSON counters start at 1

    ks = cipher.encrypt(b"\x00" * 64)      # pure keystream block
    print(hexlify(ks).decode())            # single 128-hex-char line


if __name__ == "__main__":
    main()

````

(/LLM chatgpt-4o)

````sh
idx=0
key="$(cat ctf_challenge_package.json | jq -r .cipher_parameters.key)"
nonce="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].nonce_hex")"
counter="$(cat ctf_challenge_package.json | jq -r  ".learning_dataset_for_player.[$idx].counter_int")"
plaintext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].plaintext_hex")"
ciphertext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].ciphertext_hex")"

function test_s() {
	if [[ "$1" = "$2" ]]; then echo "[OK]"; else echo "[ER]"; fi
}

keystream=$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/chacha20.py \
	--key $key \
	--nonce $nonce \
	--counter $counter)

xor1="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$plaintext" | xxd -r -p) \
    <(echo "$keystream" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"

xor2="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$ciphertext" | xxd -r -p) \
    <(echo "$keystream" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"

xor3="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$plaintext" | xxd -r -p) \
    <(echo "$ciphertext" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"


echo ciphertext
echo $ciphertext
echo

echo plaintext
echo $plaintext
echo

echo chacha20 processed keystream
echo $keystream
echo

echo expected ciphertext $(test_s $xor1 $ciphertext)
echo $xor1
echo

echo expected plaintext $(test_s $xor2 $plaintext)
echo $xor2
echo

echo xor inferred keystream $(test_s $xor3 $keystream)
echo $xor3
echo

echo sizes of xors
echo $xor1 | tr -d '\n' | wc -c
echo $xor2 | tr -d '\n' | wc -c
````

We do subtract by 1:

````python
cipher.seek((a.counter - 1) * 64)      # ← JSON counters start at 1
````

````
ciphertext
d4922d0bae1521cffe67ab68fd132595c4942d350ff30c906325faca486555dc2ac805afddd31f16401ab7e1ebccdfc7e765f6a76746e822dd7002a2691f9392

plaintext
9de16236ae1521cffe67ab68fd1325951b2a1b11b75bec946325faca2a8db02a013b9c18ddd31f168e6dd472ebccdfc7d92c85e96546e822dd7002a2691f9392

chacha20 processed keystream
1925006087686af7ca24f15b1ecb6525e2db922e79a8484915f826a2d15b020b52f96790ceeca3987e1d7c6d3e35db63cdb8ee701b44a09aa0d9f3ebe5844604

expected ciphertext [ER]
84c46256297d4b3834435a33e3d840b0f9f1893fcef3a4dd76dddc68fbd6b22153c2fb88133fbc8ef070a81fd5f904a414946b997e0248b87da9f1498c9bd596

expected plaintext [ER]
cdb72d6b297d4b3834435a33e3d840b0264fbf1b765b44d976dddc68993e57d77831623f133fbc8e3e07cb8cd5f904a42add18d77c0248b87da9f1498c9bd596

xor inferred keystream [ER]
49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8e5f62bf399b700000000ce776393000000003e49734e020000000000000000000000

sizes of xors
128
128
````

2025-06-29 Wk 26 Sun - 14:39

The keystream changed now that we generate 64 byte zeros and subtract 1 based on the 1-indexing assumption.

If we do not subtract 1:

````python
cipher.seek((a.counter) * 64)
````

````
ciphertext
d4922d0bae1521cffe67ab68fd132595c4942d350ff30c906325faca486555dc2ac805afddd31f16401ab7e1ebccdfc7e765f6a76746e822dd7002a2691f9392

plaintext
9de16236ae1521cffe67ab68fd1325951b2a1b11b75bec946325faca2a8db02a013b9c18ddd31f168e6dd472ebccdfc7d92c85e96546e822dd7002a2691f9392

chacha20 processed keystream
0f7db32985dc8cf1295d0aac306b97d1921299baf84b38b990be4f5c0b4338a0bc920b1cf89c0628a5b89765cafe514e246704348b1d38dbc33ba724298ba6fc

expected ciphertext [ER]
929cd11f2bc9ad3ed73aa1c4cd78b244893882ab4f10d42df39bb59621ce888abda99704254f193e2bd5431721328e89fd4b81ddee5bd0f91e4ba5864094356e

expected plaintext [ER]
dbef9e222bc9ad3ed73aa1c4cd78b2445686b48ff7b83429f39bb59643266d7c965a0eb3254f193ee5a2208421328e89c302f293ec5bd0f91e4ba5864094356e

xor inferred keystream [ER]
49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8e5f62bf399b700000000ce776393000000003e49734e020000000000000000000000

sizes of xors
128
128
````

2025-06-29 Wk 26 Sun - 14:46

Including also check against xor inferred keystream

2025-06-29 Wk 26 Sun - 14:56

LLM Suggests [RFC 8439](https://www.rfc-editor.org/rfc/rfc8439). This is for the ChaCha20 cipher, but it may be that it is custom or different.

### 3.1.1 Try to invert endianness

Originally, we are not able to reproduce the keystream:

````sh
idx=0
key="$(cat ctf_challenge_package.json | jq -r .cipher_parameters.key)"
nonce="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].nonce_hex")"
counter="$(cat ctf_challenge_package.json | jq -r  ".learning_dataset_for_player.[$idx].counter_int")"
plaintext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].plaintext_hex")"
ciphertext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].ciphertext_hex")"

function test_s() {
	if [[ "$1" = "$2" ]]; then echo "[OK]"; else echo "[ER]"; fi
}

keystream=$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/chacha20.py \
	--key $key \
	--nonce $nonce \
	--counter $counter)

keystream_xor="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$plaintext" | xxd -r -p) \
    <(echo "$ciphertext" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"

echo counter: $counter
echo

echo chacha20 processed keystream
echo $keystream
echo

echo xor inferred keystream $(test_s $xor3 $keystream)
echo $keystream_xor
echo
````

<a name="script-check-keystream" />^script-check-keystream

````
counter: 1

chacha20 processed keystream
0f7db32985dc8cf1295d0aac306b97d1921299baf84b38b990be4f5c0b4338a0bc920b1cf89c0628a5b89765cafe514e246704348b1d38dbc33ba724298ba6fc

xor inferred keystream [ER]
49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8e5f62bf399b700000000ce776393000000003e49734e020000000000000000000000
````

2025-06-29 Wk 26 Sun - 16:05

Let's try by [changing the endian order](003%20NUMEROLOGY.md#24-on-endianness-of-byte-streams) of the tokens.

````sh
idx=0
key="$(cat ctf_challenge_package.json | jq -r .cipher_parameters.key | fold -w2 | tac | tr -d '\n')"
nonce="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].nonce_hex")"
counter="$(cat ctf_challenge_package.json | jq -r  ".learning_dataset_for_player.[$idx].counter_int")"

function test_s() {
	if [[ "$1" = "$2" ]]; then echo "[OK]"; else echo "[ER]"; fi
}

keystream=$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/chacha20.py \
	--key $key \
	--nonce $nonce \
	--counter $counter)

keystream_xor="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$plaintext" | xxd -r -p) \
    <(echo "$ciphertext" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"

echo counter: $counter
echo

echo chacha20 processed keystream
echo $keystream
echo

echo xor inferred keystream $(test_s $xor3 $keystream)
echo $keystream_xor
echo
````

````
counter: 1

chacha20 processed keystream
89c02e407424091a6f9712bd6de5e04feeac00cef87162e4700a6308d28b6505abba710165c4df8f3d4a230bf2b6a8297b97c97951e72d67634fd0c0c5fc3ad2

xor inferred keystream [ER]
49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8e5f62bf399b700000000ce776393000000003e49734e020000000000000000000000
````

### 3.1.2 Try using openssl

2025-06-29 Wk 26 Sun - 15:26

## 3.2 Check keystreams of all samples

2025-06-29 Wk 26 Sun - 14:59

If this matches ChaCha20, I expect that all samples have the same keystream.

````sh
key="$(cat ctf_challenge_package.json | jq -r .cipher_parameters.key)"

for idx in $(seq 0 31); do
	plaintext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].plaintext_hex")"
    ciphertext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].ciphertext_hex")"
	
    keystream_xor="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
        <(echo "$plaintext" | xxd -r -p) \
        <(echo "$ciphertext" | xxd -r -p) \
    | xxd -p | xargs | tr -d ' ')"
	
    echo "keystream_xor[$idx] (plaintext xor ciphertext)"
	echo $keystream_xor
done
````

<a name="script-keystream-all-1" />^script-keystream-all-1

````
keystream_xor[0] (plaintext xor ciphertext)
49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8e5f62bf399b700000000ce776393000000003e49734e020000000000000000000000
keystream_xor[1] (plaintext xor ciphertext)
49734f4d000000000000000000000000e786b6a3b8a8e0040000000062e8e5f63bf39ab600000000ce776393000000004f49734d020000000000000000000000
keystream_xor[2] (plaintext xor ciphertext)
49734f6d000000000000000000000000f696b6a2b8a8e0040000000062e8e5f65bf39cb400000000ce776393000000007149734b020000000000000000000000
keystream_xor[3] (plaintext xor ciphertext)
49734fad000000000000000000000000e8f6b6a0b8a8e0040000000062e8e5f69bf3a0b000000000ce77639300000000b5497347020000000000000000000000
[...]
keystream_xor[31] (plaintext xor ciphertext)
49734f35000000000000000000000000d8b2f6e4b8a8e0040000000062e8e5f6237319b900000000ce776393000000003549f3cf020000000000000000000000
````

It's all different...

I guess we expect the identities

````
plaintext   ⊕  ciphertext   = keystream
plaintext   ⊕  keystream    = ciphertext
ciphertext  ⊕  keystream    = plaintext
````

To only hold per sample.

So more accurately,

````
plaintext   ⊕  ciphertext   = keystream
plaintext   ⊕  keystream    = ciphertext
ciphertext  ⊕  keystream    = plaintext

where
	keystream: generate_keystream(key, nonce, counter)
````

The objective is to figure out the encryption algorithm, generate the keystream, and XOR plaintext and ciphertext to get the expected keystream from the example.

So far we have failed with ChaCha20.

Earlier [script](003%20NUMEROLOGY.md#script-keystream-all-1) with more prints:

````sh
key="$(cat ctf_challenge_package.json | jq -r .cipher_parameters.key)"

for idx in $(seq 0 31); do
	nonce="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].nonce_hex")"
	counter="$(cat ctf_challenge_package.json | jq -r  ".learning_dataset_for_player.[$idx].counter_int")"
	plaintext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].plaintext_hex")"
    ciphertext="$(cat ctf_challenge_package.json | jq -r ".learning_dataset_for_player.[$idx].ciphertext_hex")"
	
    keystream_xor="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
        <(echo "$plaintext" | xxd -r -p) \
        <(echo "$ciphertext" | xxd -r -p) \
    | xxd -p | xargs | tr -d ' ')"
	
    echo "keystream_xor[$idx] (plaintext xor ciphertext)"
	echo $keystream_xor
	
	echo "nonce[$idx]"
	echo $nonce
	echo

	echo "counter[$idx]"
	echo $counter
	echo

	echo "plaintext[$idx]"
	echo $plaintext
	echo

	echo "ciphertext[$idx]"
	echo $ciphertext
	echo
done

````

````sh

function test_s() {
	if [[ "$1" = "$2" ]]; then echo "[OK]"; else echo "[ER]"; fi
}

keystream=$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/chacha20.py \
	--key $key \
	--nonce $nonce \
	--counter $counter)

xor1="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$plaintext" | xxd -r -p) \
    <(echo "$keystream" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"

xor2="$(~/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/scripts/xor.py \
    <(echo "$ciphertext" | xxd -r -p) \
    <(echo "$keystream" | xxd -r -p) \
| xxd -p | xargs | tr -d ' ')"


echo ciphertext
echo $ciphertext
echo

echo plaintext
echo $plaintext
echo

echo chacha20 processed keystream
echo $keystream
echo

echo expected ciphertext $(test_s $xor1 $ciphertext)
echo $xor1
echo

echo expected plaintext $(test_s $xor2 $plaintext)
echo $xor2
echo

echo xor inferred keystream $(test_s $xor3 $keystream)
echo $xor3
echo

echo sizes of xors
echo $xor1 | tr -d '\n' | wc -c
echo $xor2 | tr -d '\n' | wc -c
````

## 3.3 LLM Pattern check on pyc file

2025-06-29 Wk 26 Sun - 16:25

````sh
strings __pycache__/crypto_numerology.cpython-312.pyc | nvim --
````

(LLM chatgpt-4o)

|clue in the dump|why it matters|
|----------------|--------------|
|**`CHACHA_CONSTANTS`**|Confirms the author started from a ChaCha/Salsa code-base.|
|**`iexpaind 3i2-byite kc`**|That is a *corrupted* version of ChaCha’s ASCII constant “`expand 32-byte k`”. Whatever they finally compiled had a **mutated 16-byte constant block** (the first four 32-bit words of the state).|
|**`rounds_to_execute must be between 1 and 8 for this modified version.`**|Standard ChaCha20 always does 20 rounds; this variant deliberately runs **only 1-8 rounds** (extremely weak).|
|**`mix_bits`, `qr_operations_sequence`**|The quarter-round is still there, but they expose it so they can run only part of the round sequence.|
|**`make_block(key_bytes, nonce_bytes, counter_int, current_constants_tuple, rounds_to_execute)`**|Signature of a helper that builds **one 64-byte keystream block** while letting the caller pick *which* constant tuple and *how many* rounds.|
|**`construct_structured_key`** with “pattern_a/pattern_b” and *“active material”*|The key given to players is mostly zeros: only four 32-bit words (“active material”) are non-zero. That explains the long **runs of `00 00 00 00`** you saw in every XOR-derived keystream block.|
|**`secret_target_nonce_hex must be 24 hex chars and start with "0000"`**|Every nonce is 12 bytes, but the first two bytes are **forced to 0**. Again: easy to brute-force.|
|**`Warning: counter for nonce … wrapped around to 0`**|The engine lets the 32-bit counter overflow — so samples explore very high block numbers (e.g., `0x40000000`).|
|**`selected_constants`** / **`current_constants_tuple`**|There are *multiple* 16-byte constant blocks in play; the generator chooses one per sample row.|
|**`Error: rounds_to_execute must be between 1 and 8`**|Reinforces that **`num_double_rounds` is hard-coded to 1** (i.e., one ChaCha “double round”) and `rounds_to_execute` ∈ {1…8} picks how many of those quarter-round operations actually happen.|
|**`rotl32`, `add32`, `bytes_to_words`, `words_to_bytes`**|The arithmetic is straight ChaCha; only the inputs (constants/round-count) differ.|

* [ ] Not Fact Checked

(/LLM chatgpt-4o)

2025-06-29 Wk 26 Sun - 16:33

So the big hint is that we should spawn up our own ChaCha20 project and vary some parameters.

````
zMActual number of rounds to execute (1-8, default: 2 for a very weak variant).z
--message_size_bytesrU
z:Size of P_common in learning dataset (bytes, default: 64).z
--known_key_active_material_hexz3Hex string for the non-zero part of the known key. z
--secret_target_nonce_hexz
SECRET nonce (hex, 24 chars, first 4 hex chars/2 bytes must be '0000') to be recovered by player. Typically from set_secrets.sh.z
````

This seems like a reliable signal for setting up our own ChaCha20: [gh RustCrypto chacha20](https://github.com/RustCrypto/stream-ciphers/tree/master/chacha20) [^1](003%20NUMEROLOGY.md#1)

## 3.4 Experimenting with chacha20 rust impl

2025-06-29 Wk 26 Sun - 17:13

Project is setup \[\[\#4.1 Setup [gh RustCrypto chacha20](<https //github.com/RustCrypto/stream-ciphers/tree/master/chacha20>) 1|here\]\].

<img src="https://raw.githubusercontent.com/RustCrypto/media/8f1a9894/img/stream-ciphers/chacha20.png" width="300px">

Tangent, but this image is referenced in the implementation. Also I can paste it this way that it could probably be seen from github! See [problem entry](https://github.com/LanHikari22/lan-setup-notes/blob/main/lan/llm/weekly/Wk%2026%20007%20Github%20compatible%20screenshot%20links%20in%20Obsidian.md).

2025-06-29 Wk 26 Sun - 18:15

2025-06-29 Wk 26 Sun - 18:38

![Pasted image 20250629183846.png](../../../../../../../../../../attachments/Pasted%20image%2020250629183846.png)

Not much time is left... Only 2 hours...

2025-06-29 Wk 26 Sun - 18:43

In this line in [lib.rs](https://github.com/RustCrypto/stream-ciphers/blob/07ee501ac9067abe0679a596aa771a575baec68e/chacha20/src/lib.rs#L148),

````rust
/// State initialization constant ("expand 32-byte k")
const CONSTANTS: [u32; 4] = [0x6170_7865, 0x3320_646e, 0x7962_2d32, 0x6b20_6574];
````

This is a big hint. Exactly as in the strings:

````sh
strings __pycache__/crypto_numerology.cpython-312.pyc | less

# output
[...]
iexpaind 3i2-byite kc
[...]
CHACHA_CONSTANTSr
[...]
````

````sh
xxd __pycache__/crypto_numerology.cpython-312.pyc | less

00002cc0: 4143 4841 5f43 4f4e 5354 414e 5453 7218  ACHA_CONSTANTSr.
00002cd0: 0000 00da 057a 6572 6f73 725c 0000 0072  .....zerosr\...r
````

After `CONSTANTS` we see the byte stream

````
18 00 00 00 da 05
````

Which ends just before "zeros"...

Though unsure if that is relevant.

Other parameters in the project:

````rust
/// Number of 32-bit words in the ChaCha state
const STATE_WORDS: usize = 16;


/// 8-rounds
#[derive(Copy, Clone)]
pub struct R8;

impl Rounds for R8 {
    const COUNT: usize = 4;
}

/// 12-rounds
#[derive(Copy, Clone)]
pub struct R12;

impl Rounds for R12 {
    const COUNT: usize = 6;
}

/// 20-rounds
#[derive(Copy, Clone)]
pub struct R20;

impl Rounds for R20 {
    const COUNT: usize = 10;
}

````

(LLM chatgpt-4o)

* Suggestion for fast experimentation, expose `const_${rounds}` in Cargo features to set `COUNT` at compile-time.

We already [established](003%20NUMEROLOGY.md#script-check-keystream):

````
xor inferred keystream
49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8e5f62bf399b700000000ce776393000000003e49734e020000000000000000000000

# First 4 bytes

49 73 4f 3d

read as a little endian u32, is 0x3d4f7349.
````

````rust
const CONSTANTS: [u32; 4] = [
    0x3d4f7349,      // from sample’s keystream
    0x00000000,      // word1 (set zero for now)
    0x00000000,      // word2
    0x00000000,      // word3
];
````

The constants are:

````sh
"expa" 0x61707865  
"nd 3" 0x3320646e  
"2-by" 0x79622d32  
"te k" 0x6b206574
````

Together they spell:

````C
"expand 32-byte k"
````

(/LLM chatgpt-4o)

2025-06-29 Wk 26 Sun - 19:04

Really?

````sh
echo "expand 32-byte k" | xxd -p | fold -w4 | xargs

# output
6578 7061 6e64 2033 322d 6279 7465 206b 0a
````

````
65 78 70 61 6e 64 20 33 32 2d 62 79 74 65 20 6b 0a
e- x- p- a- n- d-  - 3- 2- -  b- y- t- e-  - k- \n
````

OK. Let's try the string we got then... "iexpaind 3i2-byite k".  It's more than 16 characters. So something must be missing.

What if we try to print the string we got from the keystream?

````sh
$ echo "49 73 4f 3d" | tr -d ' ' | xxd -r -p

IsO=%

$ echo "49 73 4f 3d" | tr -d ' ' | fold -w2 | tac | tr -d '\n' | xxd -r -p
=OsI
````

Nothing seems to be similar to the string they have there.

LLM suggests that expecting the first constant to be string is not a good approach.

2025-06-29 Wk 26 Sun - 19:21

I need to understand what these constants mean in the spec.

2025-06-29 Wk 26 Sun - 20:22

37 minutes. Just going in circles

````sh
$ echo "49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8e5f62bf399b700000000ce776393000000003e49734e020000000000000000000000" | fold -w2 | cat | xargs

49 73 4f 3d 00 00 00 00 00 00 00 00 00 00 00 00 df be 36 24 b8 a8 e0 04 00 00 00 00 62 e8 e5 f6 2b f3 99 b7 00 00 00 00 ce 77 63 93 00 00 00 00 3e 49 73 4e 02 00 00 00 00 00 00 00 00 00 00 00

$ echo "49734f3d000000000000000000000000dfbe3624b8a8e0040000000062e8e5f62bf399b700000000ce776393000000003e49734e020000000000000000000000" | fold -w2 | tac | xargs

00 00 00 00 00 00 00 00 00 00 00 02 4e 73 49 3e 00 00 00 00 93 63 77 ce 00 00 00 00 b7 99 f3 2b f6 e5 e8 62 00 00 00 00 04 e0 a8 b8 24 36 be df 00 00 00 00 00 00 00 00 00 00 00 00 3d 4f 73 49
````

````sh
0x3d4f7349, 0x00000000, 0x2436bedf, 0x04e0a8b8,

00 00 00 00 00 00 00 00 00 00 00 02 4e 73 49 3e 00 00 00 00 93 63 77 ce 00 00 00 00 b7 99 f3 2b f6 e5 e8 62 00 00 00 00 [04 e0 a8 b8] [24 36 be df] 00 00 00 00 00 00 00 00 [00 00 00 00] [3d 4f 73 49]

0-3
4-7
16-9
20-23
````

# 4 Tooling

## 4.1 Setup [gh RustCrypto chacha20](https://github.com/RustCrypto/stream-ciphers/tree/master/chacha20) [^1](003%20NUMEROLOGY.md#1)

2025-06-29 Wk 26 Sun - 16:52

````sh
mkdir -p /home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/cloned/gh/RustCrypto
cd /home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/cloned/gh/RustCrypto
git clone git@github.com:RustCrypto/stream-ciphers.git
cd stream-ciphers/chacha20/
````

This is a library. We also need to setup a rust project here to use it!

````sh
mkdir -p /home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/repos/cust_chacha
cd /home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/repos/cust_chacha
cargo init
````

````toml
# Cargo.toml

[package]
name = "cust_chacha"
version = "0.1.0"
edition = "2024"

[dependencies]

chacha20 = { path = "/home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/cloned/gh/RustCrypto/stream-ciphers/chacha20", version = "0.10.0-rc.0" }
````

````sh
# in /home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/repos/cust_chacha
cargo build --release
````

2025-06-29 Wk 26 Sun - 17:02

OK. We are able to build chacha20. Now let's use the example they have in the [docs](https://docs.rs/chacha20/latest/chacha20/#example),

Some missing crates the example uses:

````sh
cargo add hex_literal
````

````rust
/// main.rs

fn main() {
    println!("Hello, world!");
}

#[cfg(test)]
mod tests {
    // use super::*;

    use chacha20::ChaCha20;
    // Import relevant traits
    use chacha20::cipher::{KeyIvInit, StreamCipher, StreamCipherSeek};
    use hex_literal::hex;

    #[test]
    fn test_docs_example() {
        let key = [0x42; 32];
        let nonce = [0x24; 12];
        let plaintext = hex!("00010203 04050607 08090A0B 0C0D0E0F");
        let ciphertext = hex!("e405626e 4f1236b3 670ee428 332ea20e");

        // Key and IV must be references to the `GenericArray` type.
        // Here we use the `Into` trait to convert arrays into it.
        let mut cipher = ChaCha20::new(&key.into(), &nonce.into());

        let mut buffer = plaintext.clone();

        // apply keystream (encrypt)
        cipher.apply_keystream(&mut buffer);
        assert_eq!(buffer, ciphertext);

        let ciphertext = buffer.clone();

        // ChaCha ciphers support seeking
        cipher.seek(0u32);

        // decrypt ciphertext by applying keystream again
        cipher.apply_keystream(&mut buffer);
        assert_eq!(buffer, plaintext);

        // stream ciphers can be used with streaming messages
        cipher.seek(0u32);
        for chunk in buffer.chunks_mut(3) {
            cipher.apply_keystream(chunk);
        }
        assert_eq!(buffer, ciphertext);

    }
}
````

````sh
cargo test           

# output
   Compiling typenum v1.18.0
   Compiling cpufeatures v0.2.17
   Compiling cfg-if v1.0.1
   Compiling hex-literal v1.0.0
   Compiling hybrid-array v0.3.1
   Compiling crypto-common v0.2.0-rc.3
   Compiling inout v0.2.0-rc.5
   Compiling cipher v0.5.0-rc.0
   Compiling chacha20 v0.10.0-rc.0 (/home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/cloned/gh/RustCrypto/stream-ciphers/chacha20)
   Compiling cust_chacha v0.1.0 (/home/lan/src/exp/puzzles/ctf/2025-06/NUMEROLOGY/repos/cust_chacha)
    Finished `test` profile [unoptimized + debuginfo] target(s) in 3.15s
     Running unittests src/main.rs (target/debug/deps/cust_chacha-b9a2fd24e40c19ac)

running 1 test
test tests::test_docs_example ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
````

OK

# 5 References

1. [gh RustCrypto chacha20](https://github.com/RustCrypto/stream-ciphers/tree/master/chacha20) ^1

**Curious**

1. https://github.com/Wind-River/crypto-detector
1. https://github.com/RadjahDri/identify-crypto-algorithm
1. https://github.com/salvacorts/smashcrack
1. https://github.com/j4ik2i5x0/Encryption-Algorithm-Detector

**Search**

1. https://datatracker.ietf.org/doc/html/rfc5116
1. https://www.iana.org/assignments/aead-parameters/aead-parameters.xhtml
