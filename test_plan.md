Software Test Plan (STP) 
Project:file compression (zip file basic implementation)
Version: 1.0 
Authors:team5
Date: 16-09-2026
Status: Draft 
1. Introduction 
Purpose: This document defines the test plan for the File Compression Tool v1.0. It outlines the objectives, scope, strategy, resources, schedule, and responsibilities for testing the compression and decompression functionality built on Huffman coding. 
Scope:Test scope includes the entire process of CLI tool usage: file reading, frequency analysis, Huffman tree creation, codes creation, bits string creation, metadata/header storing, compressed file creation, file decompression, header integrity, and exact reproduction of original file. GUI testing, Zip file format compatibility, and behavior on servers/networks are not part of test scope since it is a CLI utility without these features.
References: File Compression Tool SRS v1.0, design specifications (DS-IO, DS-HUFF, DS-ENC, DS-FMT, DS-DEC, DS-ERR, DS-SEC series), RTM v1.0.
Definitions: SRS (Software requirements specification), RTM (requirements traceability matrix), CLI (Command Line Interface), NFR (Non-functional requirement), TC (Test Case).
2. Test Items 
- File I/O Module
- Frequency Analysis Module
- Huffman Tree / Code Generation Module
- Encoder Module (bitstream + header writing)
- File Format Module (metadata/header structure)
- Decoder Module (header validation, tree reconstruction, bitstream decoding)
- Error Handling Module
- CLI / UI Module (prompts, messages, statistics display)


3. Features to be Tested 
Features mapped to SRS requirement IDs: 
- FCT-F-001 / FCT-F-002: Accept and read input file (binary, unaltered) — TC-COMP-01, TC-COMP-02
- FCT-F-003: Frequency calculation — TC-HUFF-01
- FCT-F-004 / FCT-F-005: Huffman tree construction and prefix-free code generation — TC-HUFF-02, TC-HUFF-03
- FCT-F-006 / FCT-F-007 / FCT-F-008: Bitstream encoding, header write, output file creation — TC-COMP-03, TC-FMT-01, TC-COMP-04
- FCT-F-009: Compression statistics display — TC-UI-01
- FCT-F-010 / FCT-F-011: Accept and validate compressed file — TC-DECOMP-01, TC-DECOMP-02
- FCT-F-012 / FCT-F-013: Reconstruct Huffman structure and decode bitstream — TC-HUFF-04, TC-DECOMP-03
- FCT-F-014: Write decompressed output file — TC-DECOMP-04
- FCT-F-015 / FCT-F-016: Error handling and overwrite protection — TC-ERR-01, TC-ERR-02
- FCT-NF-001: Byte-for-byte data integrity — TC-NF-01
- FCT-NF-002 / FCT-NF-003: Processing performance and bounded memory use — TC-PERF-01, TC-PERF-02
- FCT-NF-004: CLI usability — TC-UX-01
- FCT-NF-005: Portability (Windows/Linux build) — TC-PORT-01
- FCT-NF-006: No silent data corruption — TC-REL-01
- FCT-SR-001 through FCT-SR-005: Metadata validation, access-failure reporting, overwrite prevention, output-path validation, corrupted-header rejection — TC-SEC-01 through TC-SEC-05

4. Features Not to be Tested 
- Any graphical user interface (the tool is CLI-only)
- External server, network, or database behavior (none exists in this system)
- Compiler/toolchain internals (GCC/G++/MSVC correctness is assumed, not tested)

5. Test Approach / Strategy 
Levels: 
- Module-level unit tests: frequency counter, tree builder, code generator, bit-level reader/writer, independently of other components
- Integration testing: complete pipeline of encode/decode + write/read operations within the tool
- System level tests: end-to-end command-line compression/decompression on files
- Acceptance tests: validation against SRS §7 criteria of successful completion, approved by the course evaluator

Types: 
- Functional Testing (core functionalities of compression/decompression)
- Regression Testing (rerunning complete set of tests after every fix)
- Performance Testing (time and memory utilization with respect to file size, FCT-NF-002/003)
- Reliability/Integrity Testing (byte-to-byte comparison of original file and decompressed file)
- Usability Testing (prompt/message of command line interface, FCT-NF-004)
- Security Testing (handling corrupted/input handling)

Entry Criteria: Stable build is compiled free of errors; test files (empty, single symbol, small text, binary, large file) are available; test environment is set up.

Exit Criteria: All high priority test cases planned are executed; no critical bugs found (any data integrity or corruption bug is a critical bug); all acceptance criteria mentioned in SRS Chapter 7 are met; RTM completed with Pass/Fail status of all 27 requirements.

5.1 Security Validation 
- Verify rejection of malformed/corrupt compressed-file header 
- Ensure false "success" is not reported when reads/writes fail 
- Ensure overwrite protection messages work as expected and cannot be accidentally circumvented 
- Test with invalid/inaccessible output directories (nonexistent/unaccessible directory
- Simple fuzzing of the compressed-file header using random/defective byte sequences to ensure that there is no crash or undefined behavior
