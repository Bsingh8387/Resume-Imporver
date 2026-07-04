**EDI to CSV Parser**

A lightweight Python tool that parses raw ANSI X12 EDI files (e.g., 837, 834, 835 transaction sets) into a flat, structured CSV format. Built as a learning project to understand EDI segment/element structure using core Python (no external EDI libraries).

**Features**


Parses raw EDI text into segments using the segment terminator (~)
Breaks each segment into its ID and individual elements using the element separator (*)
Detects and reports the element separator type from the ISA header (*, |, ^, or non-standard)
Converts parsed segments into structured dictionaries (segment_id + elements)
Supports filtering/searching for specific segment IDs (e.g., NM1, CLM, DTP)
Exports parsed data into a timestamped CSV file (one row per element)


**Project Structure**

.
├── Function.py   # Core parsing logic (reusable functions)
├── Main.py        # Script entry point — reads, validates, parses, and exports
└── README.md

**Function.py**

FunctionPurposesplit_into_segment(raw_text, segment_terminator="~")Splits raw EDI text into a clean list of segments (strips whitespace, drops empties)get_segment_ids(segments, filter_ids=None)Extracts segment IDs from a list of segments, optionally filtered to a set of IDssegment_to_dict(seg, element_separator="*")Converts a single segment string into {"segment_id": ..., "elements": [...]}parse_segments(segments, element_separator="*")Applies segment_to_dict across a full list of segmentsfind_segments(parsed, segment_id)Returns all parsed segments matching a given segment ID

**Main.py**

FunctionPurposeread_edi_file(file_name)Reads the raw .edi file as textvalidate_edi(validate)Inspects the ISA segment's 4th character to identify the element separatormake_unique_csv_path(...)Builds a timestamped output path so each run produces a new CSVwrite_csv(parsed, output_path)Writes parsed segments to CSV with segment_id, element_position, value columns

**Requirements**


Python 3.7+
No external dependencies (uses only csv, os, and datetime from the standard library)


**Usage**


Update the hardcoded file paths in Main.py to point to your EDI file and desired output directory:


python   raw = read_edi_file(r"path/to/your/sample.edi")

and inside make_unique_csv_path(...), update output_dir.


Run the script:


bash   python Main.py


**The script will:**

Print the detected element separator
Parse the EDI file into segments and elements
Write a timestamped CSV (e.g., my_first_edi_output_20260704_153000.csv) to the output directory
Print the path of the generated CSV





Example Output CSV

segment_idelement_positionvalueISA100ISA2NM11ILNM121.........

Known Limitations


Hardcoded paths: input/output paths in Main.py are currently fixed to a local Windows directory and need to be edited directly in the script (no CLI arguments yet).
Basic separator detection: validate_edi only inspects the ISA segment's element separator character; it doesn't perform full ISA/GS/ST envelope validation.
Flat output only: the CSV output is a flat list of segment/element/value rows — it doesn't reconstruct hierarchical loop structures (e.g., linking a CLM segment to its related NM1/DTP segments).
No error handling: missing files, malformed segments, or unexpected separators will raise unhandled exceptions.


**Roadmap / Ideas**


Accept input/output paths as command-line arguments
Add loop-aware parsing (group related segments, e.g., by HL hierarchy)
Support common X12 transaction-specific segment maps (837, 834, 835)
Add unit tests for Function.py
Package as an installable CLI tool
