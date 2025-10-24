# CSE469 Homework 1 – Partition Table and Boot Sector Parser

**Student:** Faisal Alyousefi  
**Course:** CSE 469 – Computer and Network Forensics  
**Assignment:** Homework 1  
**Language:** Python 3.10  
**Environment:** Ubuntu 22.04.5 (UTM VM)

---

## 1. Overview

This project implements a Python tool called `boot_info.py` that parses raw disk images to extract partition table and boot sector information for both **MBR** (Master Boot Record) and **GPT** (GUID Partition Table) schemes.  

It can output data in two modes:
- **Human-readable text** (default)
- **Structured JSON** (`--json` flag), which is required by the autograder.

The script also generates MD5, SHA-256, and SHA-512 hashes of the provided image, saves them in the `hash_info/` directory, and outputs partition boot sector bytes when requested.

---

## 2. Features Implemented

✅ Detects and parses both MBR and GPT partition schemes.  
✅ Produces **JSON output** formatted exactly as required by the autograder.  
✅ Computes **MD5**, **SHA-256**, and **SHA-512** hashes with **no trailing newlines** in the files.  
✅ Writes hash files to `hash_info/` with filenames:
   - `MD5-<image>.txt`
   - `SHA-512-<image>.txt`  
✅ Extracts **boot sector bytes** for each MBR partition when offsets are provided (`-o`).  
✅ Uses **PartitionTypes.csv** to map MBR partition type codes to names.  
✅ For GPT partitions:
   - Outputs **raw 32-character hex GUIDs (no dashes)**.  
✅ For MBR partitions:
   - Stores boot byte data **nested under each partition**, not at the top level.

---

## 3. File Structure

| File | Description |
|------|--------------|
| `boot_info.py` | Main Python program that performs image analysis. |
| `Makefile` | Compiles the script into an executable `boot_info`. |
| `PartitionTypes.csv` | Maps MBR partition codes to descriptive names. |
| `mbr_sample.raw` | Sample MBR disk image for testing. |
| `gpt_sample.raw` | Sample GPT disk image for testing. |
| `hash_info/` | Output folder for hash text files. |

---

## 4. Build and Run Instructions

### **Compile**
```bash
make clean && make
