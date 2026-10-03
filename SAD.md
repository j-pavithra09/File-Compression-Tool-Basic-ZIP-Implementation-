# Software Architecture and Design Specification

**Project:** File Compression Tool (Basic ZIP Implementation)
**Version:** 1.0
**Authors:** Team 05
**Date:** 03-10-2026
**Status:** Completed

---

## Revision History

| Version | Date | Author | Change Summary |
|---------|------|--------|----------------|
| 1.0 | 03-10-2026 | TEAM 5 | Initial release of Software Architecture and Design Specification. |

---

## Approvals

| Role | Name | Signature / Email | Date |
|------|------|-------------------|------|
| QA Lead / Team Lead | Karthik | Karthik | 03/10/2026 |
| Developer |Vaishnavi | Vaishnavi | 03/10/2026 |
| Test Engineer | Kavan | Kavan | 03/10/2026 |
| Test Engineer | Pavithra | Pavithra | 03/10/2026 |

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Document Overview](#2-document-overview)
3. [Architecture](#3-architecture)
4. [Design](#4-design)
5. [Appendices](#5-appendices)

---

## 1. Introduction

### 1.1 Purpose

This document specifies the architecture and design of the File Compression Tool using Huffman Coding.

### 1.2 Scope

Covers file compression, Huffman code generation, and decompression.

### 1.3 Audience

Developers, testers, instructors, and maintenance teams.

### 1.4 Definitions

| Term | Definition |
|------|------------|
| Huffman Coding | A lossless compression algorithm that assigns shorter codes to more frequent symbols. |
| Compression | Reducing the number of bits required to represent file data. |
| Decompression | Reconstructing the original file from compressed data. |
| Huffman Tree | A binary tree used to generate variable-length prefix-free codes. |
| Prefix-Free Code | A code in which no codeword is a prefix of another. |

---

## 2. Document Overview

### 2.1 How to Use This Document

Describes the system architecture, UML diagrams, component design, API design, and error handling.

### 2.2 Related Documents

- SRS v1.0 — File Compression Tool, TEAM 5, 06-09-2026
- STP v1.0 — Software Test Plan, TEAM 5
- RTM v1.0 — Requirements Traceability Matrix

---

## 3. Architecture

### 3.1 Goals & Constraints

**Goals:**
- Lossless compression — decompressed output must be byte-for-byte identical to the original.
- Reliable decompression from any valid compressed file produced by the tool.
- Reduced file size through Huffman-based variable-length coding.

**Constraints:**
- Memory limitations of the host machine.
- File size handling must avoid unbounded in-memory copies.
- Processing time must be acceptable for normal desktop hardware.

### 3.2 Stakeholders & Concerns

| Stakeholder | Primary Concerns |
|-------------|-----------------|
| Users | File integrity and ease of CLI use |
| Developers | Modularity and maintainability |
| Testers | Correct compression and decompression behaviour |
| Project Team | Reliability and performance |

### 3.3 Component (UML) Diagram

> See the component diagram image in the repository (`/docs/component_diagram.png`).

**Components and relationships:**

- **Main Program** — coordinates all modules; handles CLI and overall execution flow.
- **File Handling Module** — reads input files, writes compressed and decompressed output files.
- **Frequency Analysis Module** — receives file data, calculates byte frequencies, returns frequency table.
- **Huffman Tree Construction Module** — receives frequency table, builds Huffman tree, returns root node.
- **Huffman Code Generation Module** — receives Huffman tree, generates prefix-free codes, returns code table.
- **Compression Module** — receives code table and file data, encodes bitstream, writes compressed output.
- **Decompression Module** — reads compressed file, validates header, reconstructs tree, decodes bitstream, writes original file.
- **Error Handling Module** — receives error notifications from all modules, logs messages, provides user-friendly output.

### 3.4 Component Descriptions

**Frequency Analysis Module**
Calculates the frequency of each byte in the input file and generates a frequency table.

**Huffman Tree Construction Module**
Constructs a binary Huffman tree using the byte frequencies and a min-priority queue.

**Huffman Code Generation Module**
Traverses the Huffman tree and generates prefix-free binary codes for each byte.

**Compression Module**
Uses the generated Huffman codes to encode the input data into a compressed bitstream.

**Decompression Module**
Decodes the compressed bitstream using the Huffman tree or equivalent decoding information to reconstruct the original data.

**File Handling Module**
Reads input files and writes compressed and decompressed output files.

**Main Program**
Coordinates the modules and manages the command-line interface and overall execution.

**Error Handling Module**
Logs errors, provides user-friendly messages to stderr, and handles exceptions across all modules.

### 3.5 Chosen Architecture Pattern and Rationale

**Pattern:** Modular Architecture

Modular architecture is chosen to separate the concerns of frequency analysis, Huffman tree construction, code generation, compression, decompression, and file handling. Each module has a well-defined interface and single responsibility, making the system easier to:
- **Test** — each module can be unit-tested independently.
- **Maintain** — changes in one module do not affect others.
- **Extend** — additional compression algorithms can be added as new modules without disrupting existing ones.

### 3.6 Technology Stack & Data Stores

| Item | Details |
|------|---------|
| Programming Language | C / C++ (C++17 or later recommended) |
| Compression Algorithm | Huffman Coding |
| Input | Text or binary files (any file type) |
| Output | Compressed `.huff` file; decompressed original file |
| Data Storage | Local file system only |
| Build Tools | GCC/G++ (Linux), MSVC / MinGW-w64 (Windows) |
| IDE | Visual Studio Code |

### 3.7 Risks & Mitigations

| Risk | Mitigation |
|------|------------|
| Data loss during compression | Preserve the original input file; never overwrite it during compression. |
| Corrupted compressed files | Validate the compressed file header and metadata before attempting decompression. |
| Large file memory exhaustion | Use read/write buffers; avoid loading the entire file into memory at once. |
| Invalid or unexpected input | Display appropriate error messages via the Error Handling Module; exit with non-zero status. |

### 3.8 Traceability to Requirements

| SRS Requirement | Mapped Module |
|----------------|---------------|
| FCT-F-001, FCT-F-002 | File Handling Module (`readFile`) |
| FCT-F-003 | Frequency Analysis Module (`calculateFrequency`) |
| FCT-F-004 | Huffman Tree Construction Module (`buildHuffmanTree`) |
| FCT-F-005 | Huffman Code Generation Module (`generateCodes`) |
| FCT-F-006, FCT-F-007, FCT-F-008 | Compression Module (`compressFile`) |
| FCT-F-009 | Main Program (CLI statistics display) |
| FCT-F-010 – FCT-F-014 | Decompression Module (`decompressFile`) |
| FCT-F-015, FCT-F-016 | Error Handling Module (`handleError`) |

### 3.9 Security Architecture

**Threat Modelling (STRIDE):**

| Threat | Mitigation |
|--------|------------|
| Spoofing | Validate input file paths and compressed file headers before processing. |
| Tampering | Perform file integrity checks; validate metadata before decoding. |
| Information Disclosure | Avoid logging file contents; log only error codes and context messages. |
| Denial of Service (DoS) | Enforce file size checks; use buffered I/O to prevent memory exhaustion. |
| Elevation of Privilege | Restrict file access to the user's own permissions; do not request elevated OS privileges. |

---

## 4. Design

### 4.1 Design Overview

The File Compression Tool uses modular components for separation of concerns and easier maintenance. Each component communicates through well-defined function interfaces, with the Main Program coordinating the overall execution flow.

### 4.2 UML Sequence Diagrams

#### Sequence Diagram 1: Huffman Code Generation

> See `docs/sequence_diagram_1.png`

**Flow:**
1. User starts compression (selects input file).
2. Main Program → Frequency Analysis Module: read file and calculate byte frequencies.
3. Frequency Analysis Module → Main Program: return frequency table.
4. Main Program → Huffman Tree Construction Module: build Huffman tree using frequency table.
5. Huffman Tree Construction Module → Main Program: return Huffman tree.
6. Main Program → Huffman Code Generation Module: generate prefix-free codes from Huffman tree.
7. Huffman Code Generation Module → Main Program: return code table.
8. Main Program proceeds to compression.

---

#### Sequence Diagram 2: Huffman Tree Construction

> See `docs/sequence_diagram_2.png`

**Flow:**
1. Main Program → Frequency Analysis Module: request byte frequencies from input file.
2. Frequency Analysis Module → Main Program: return frequency table.
3. Main Program → Huffman Tree Construction Module: build Huffman tree using frequency table.
4. Huffman Tree Construction Module → Main Program: return constructed Huffman tree.

---

#### Sequence Diagram 3: File Decompression Workflow

> See `docs/decompression_sequence_diagram.png`

**Flow:**
1. User starts decompression (selects compressed file).
2. Main Program → File Handling Module: read compressed file (binary mode).
3. File Handling Module → Main Program: return compressed file data.
4. Main Program → Decoder Module: validate header / metadata.
5. Decoder Module → Huffman Tree Module: reconstruct Huffman tree from stored metadata.
6. Huffman Tree Module → Decoder Module: return reconstructed Huffman tree.
7. Decoder Module: decode bitstream using Huffman tree.
8. Decoder Module → Main Program: return original byte sequence.
9. Main Program → File Handling Module: write decompressed output file.
10. File Handling Module → Main Program: file write confirmed.
11. Main Program → CLI/UI Module: display status / error message.
12. CLI/UI Module → User: show decompression result.

---

### 4.3 API Design

#### 1. Frequency Analysis Module

- **Function:** `calculateFrequency`
- **Input:** Input file (binary stream)
- **Output:** Byte frequency table (`map<uint8_t, int>`)
- **Errors:** File not found, file read error.

#### 2. Huffman Tree Construction Module

- **Function:** `buildHuffmanTree`
- **Input:** Byte frequency table
- **Output:** Constructed Huffman tree (root node pointer)
- **Errors:** Empty frequency table, memory allocation failure.

#### 3. Huffman Code Generation Module

- **Function:** `generateCodes`
- **Input:** Constructed Huffman tree (root node pointer)
- **Output:** Code table (`unordered_map<uint8_t, string>`)
- **Errors:** Null/empty tree passed in, memory allocation failure during traversal.

#### 4. Compression Module (Encoder)

- **Function:** `compressFile`
- **Input:** Input file path, output file path, code table (from `generateCodes`)
- **Output:** Compressed `.huff` file (header + encoded bitstream)
- **Errors:** Input file not found or unreadable, output path invalid or unwritable, output file already exists and overwrite not confirmed, code table empty.

#### 5. Decompression Module (Decoder)

- **Function:** `decompressFile`
- **Input:** Compressed `.huff` file path, output file path
- **Output:** Decompressed file — byte-for-byte identical to original
- **Errors:** Compressed file not found or unreadable, invalid/missing/corrupted header, incomplete or inconsistent metadata, output path invalid or unwritable, output file already exists and overwrite not confirmed, bitstream ends unexpectedly.

#### 6. File Handling Module

- **Function:** `readFile`
  - **Input:** File path (string), read mode (binary)
  - **Output:** Raw byte buffer / stream of file contents
  - **Errors:** File not found, insufficient read permissions, file is empty.

- **Function:** `writeFile`
  - **Input:** File path (string), byte buffer / data to write, write mode (binary)
  - **Output:** File written successfully to disk
  - **Errors:** Output path does not exist, insufficient write permissions, disk full, output file already exists (triggers overwrite check).

#### 7. Error Handling Module

- **Function:** `handleError`
- **Input:** Error code (enum), context message (string), optional: file path involved
- **Output:** Formatted error message printed to `stderr`; exits with non-zero status for critical errors, returns control to caller for recoverable ones.
- **Errors:** N/A — this module is the error handler itself; it does not throw, it reports.

### 4.4 Error Handling, Logging & Monitoring

- Standardized error messages for all file access and compression/decompression failures.
- Logs record error codes and context messages without logging sensitive file contents.
- Monitoring covers: compression failure rate, successful file processing count, and file write confirmations.

### 4.5 UX Design

The command-line interface provides:
- Clear command syntax for compression and decompression operations.
- Readable success and error messages via `stdout` / `stderr`.
- Basic compression statistics after successful compression (original size, compressed size, ratio).
- Overwrite confirmation prompt when the output file already exists.

### 4.6 Open Issues & Next Steps

| Item | Description |
|------|-------------|
| Additional algorithms | Support for other compression algorithms (e.g. LZ77, RLE) as future modules. |
| Compression efficiency | Improved bit-packing and header format to reduce compressed file size further. |
| GUI | Optional graphical user interface for non-CLI users in a future version. |

---

## 5. Appendices

### 5.1 Glossary

| Term | Definition |
|------|------------|
| Huffman Coding | A lossless data compression algorithm using a binary tree to assign shorter codes to more frequent symbols. |
| Huffman Tree | A binary tree constructed from symbol frequencies, used to derive prefix-free variable-length codes. |
| Prefix-Free Code | A code in which no codeword is a prefix of another, ensuring unambiguous decoding. |
| Bitstream | A sequence of bits representing the Huffman-encoded data. |
| Header / Metadata | Information stored at the start of a compressed file required for decompression. |
| CLI | Command-Line Interface. |
| SAD | Software Architecture and Design Specification (this document). |
| SRS | Software Requirements Specification. |
| STP | Software Test Plan. |
| RTM | Requirements Traceability Matrix. |

### 5.2 References

- SRS v1.0 — File Compression Tool, TEAM 5, 06-09-2026.
- STP v1.0 — Software Test Plan, TEAM 5.
- C/C++ Standard Library documentation.

### 5.3 Tools Used

| Tool | Purpose |
|------|---------|
| C / C++ | Implementation language |
| Visual Studio Code | Code editor and development environment |
| GCC / G++ | Compiler (Linux) |
| draw.io | UML diagram creation |
| GitHub | Version control and issue tracking |

---

