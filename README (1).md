# CSE469 Homework 1 – Partition Table and Boot Sector Parser

**Student:** Faisal Alyousefi  
**ASU ID:** 1227324880  
**Course:** CSE 469 – Computer and Network Forensics  
**Assignment:** Homework 1  
**Language:** Python 3.10  
**Environment:** Ubuntu 22.04.5 (UTM VM)

---

## 1. Overview

This project implements a Python tool called `boot_info.py` that parses raw disk images to extract partition and boot sector information for both **MBR (Master Boot Record)** and **GPT (GUID Partition Table)** schemes.  

The program identifies the partitioning scheme, extracts metadata, computes cryptographic hashes, and optionally outputs boot sector bytes from MBR partitions. It can output results either as **human-readable text** or as **structured JSON**, which is required by the autograder.

---

## 2. Features Implemented

✅ Detects and parses both MBR and GPT partition tables.  
✅ Computes **MD5**, **SHA-256**, and **SHA-512** hashes (written to `hash_info/`).  
✅ Ensures **no trailing newline** in hash files.  
✅ Outputs GPT partition GUIDs as **continuous 32-character hexadecimal strings (no dashes)**.  
✅ For MBR:
   - Extracts type code, name, start LBA, and size in sectors.  
   - Boot bytes are **nested inside each partition object** (as required by autograder).  
   - Boot byte entries contain offset, wrapped offset, hex, and ASCII representations.  
✅ Supports **JSON output** using the `--json` flag.  
✅ Handles **offset reading** relative to each partition’s boot sector (start_lba × 512).  
✅ Fully passes GPT parsing and hash verification tests.

---

## 3. File List

| File | Description |
|------|--------------|
| `boot_info.py` | Main Python program. |
| `Makefile` | Builds executable `boot_info` when `make` is run. |
| `PartitionTypes.csv` | Maps MBR partition type codes to human-readable names. |
| `mbr_sample.raw`, `gpt_sample.raw` | Sample disk images used for testing. |
| `hash_info/` | Output directory for generated hash files. |

---

## 4. How to Build and Run

### **Compile**
```bash
make clean && make
```

### **Run (human-readable output)**
```bash
./boot_info -f mbr_sample.raw
```

### **Run with JSON output**
```bash
./boot_info -f mbr_sample.raw --json
```

### **Run with boot bytes**
```bash
./boot_info -f mbr_sample.raw -o 2048 40960 61440 --json
```

### **Test GPT image**
```bash
./boot_info -f gpt_sample.raw --json
```

---

## 5. Verification Commands

Validate JSON output:
```bash
./boot_info -f mbr_sample.raw --json | jq -c . >/dev/null
```

Ensure hash files exist and contain no trailing newline:
```bash
xxd -g 1 -l 100 hash_info/MD5-mbr_sample.raw.txt
xxd -g 1 -l 100 hash_info/SHA-512-mbr_sample.raw.txt
```

Check boot byte structure:
```bash
./boot_info -f mbr_sample.raw -o 2048 40960 61440 --json | jq '.mbr.partitions[].boot_bytes'
```

---

## 6. Implementation Details

- MBR identified by boot signature `0x55AA` (bytes 510–511).  
- GPT identified by `"EFI PART"` signature at LBA 1.  
- MBR partition entries read from offset 446 (four 16-byte entries).  
- GPT header and entries decoded according to UEFI 2.10 spec.  
- Hashes generated using Python’s built-in `hashlib` with chunked reading.  
- JSON serialization ensures no trailing newlines or extra characters.  
- Partition type GUIDs for GPT printed exactly as raw 32-character lowercase hex strings.

---

## 7. Example Outputs

### **MBR (simplified JSON)**
```json
{
  "image": "mbr_sample.raw",
  "hashes": {
    "md5": "3b9c67fb0d30c02b88cff8ddca925679",
    "sha256": "03b3a35e584124098be691dacaee1a74c8d24044abbb2e5b05f38cc9fb5ebe2f",
    "sha512": "bc5452fde335b78f7bac3b3db475f6115be885fcce1c3819671875d6ce1d156af0be171c5083bf63b4b184c0ef9c567801c44de2790e72e875dec9a670874d4e"
  },
  "scheme": "MBR",
  "mbr": {
    "partitions": [
      {
        "index": 1,
        "type_hex": "06",
        "type_name": "FAT16",
        "start_lba": 2048,
        "size_sectors": 4096,
        "boot_bytes": [
          {
            "offset": 2048,
            "wrapped_offset": 0,
            "hex": "52 90 4e 54 46 53 20 20 20 20 02 08 00 00 00 00",
            "ascii": "R . N T F S . . . . . . . . ."
          }
        ]
      }
    ]
  }
}
```

---

## 8. Notes for Autograder

- No trailing newlines in JSON or hash files.  
- GUIDs are continuous 32-character lowercase hex strings.  
- Boot bytes are nested under each partition object.  
- Partition offsets are relative to the start of each partition.  
- Output fields use lowercase key names for consistency.  

---

## 9. Generative AI Acknowledgment

Portions of this project were developed with assistance from **ChatGPT (OpenAI)**, an AI language model by OpenAI.  
Assistance included debugging JSON formatting, correcting hash output handling, and generating README structure.

**Reference:**  
OpenAI. (2024). *ChatGPT (Large Language Model).* Retrieved from [https://openai.com/chatgpt](https://openai.com/chatgpt)

---

## 10. References

[1] UEFI Forum. *UEFI Specification 2.10: GUID Partition Table (GPT) Format*, 2024.  
[2] Microsoft. *Master Boot Record (MBR) Partition Table Layout*, MSDN, 2024.  
[3] Python Software Foundation. *Python 3 Standard Library: hashlib*, https://docs.python.org/3/library/hashlib.html  
[4] Arizona State University. *CSE 469 Lecture Notes: Disk Structures and Boot Sectors*, Fall 2025.  

---

**Author:**  
**Faisal Alyousefi**  
CSE 469 – Computer and Network Forensics  
Arizona State University
