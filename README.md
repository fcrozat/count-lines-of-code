# Count Lines of Code

A collection of scripts to count lines of code and patch changes in source RPMs (`.src.rpm`) or package source directories. This toolset is particularly useful for analyzing large sets of packages, such as those in a Linux distribution.

## Features

- Counts code lines, comments, and blank lines using [tokei](https://github.com/XAMPPRocky/tokei).
- Analyzes patches (`.patch`, `.diff`, `.dif`) to count additions and deletions.
- Recursively processes tarballs within source RPMs.
- Support for parallel processing to speed up analysis of multiple packages.
- Language-specific breakdown (optional).
- Post-processing script to deduplicate results from different RPM flavors.

## Requirements

### System Dependencies
- `bsdtar` (from `libarchive`)
- `tokei`

### Python Dependencies
- `rpmfile`
- `sh`
- `unidiff`

You can install the Python dependencies via pip:
```bash
pip install rpmfile sh unidiff
```

## Usage

### Single-threaded Analysis

Use `count.py` to analyze a single package or a directory of packages.

```bash
# Analyze a single RPM or directory
python3 count.py --file package.src.rpm

# Analyze all packages in a directory
python3 count.py --dir /path/to/packages

# Enable language breakdown
python3 count.py --lang --file package.src.rpm
```

### Parallel Analysis

Use `count-parallel.py` for faster processing of large directories.

```bash
python3 count-parallel.py --dir /path/to/packages --proc 8
```

### Post-processing (Deduplication)

If you have multiple flavors of the same package (e.g., different kernel builds), `crunch.pl` can help deduplicate the results.

```bash
python3 count-parallel.py --dir /path/to/packages | sort | perl crunch.pl
```

## How it works

1. **`count.py` / `count-parallel.py`**:
   - Extracts source RPMs or looks into source directories.
   - Identifies tarballs and patches.
   - For each tarball:
     - Extracts it to a temporary directory.
     - Runs `tokei` to get line counts.
     - If the tarball contains patches, it processes them individually.
   - For each patch:
     - Uses `unidiff` to count added and removed lines.
   - Aggregates and prints the results.

2. **`crunch.pl`**:
   - Reads the output of the counting scripts.
   - Uses a heuristic based on package naming and line counts to identify and skip duplicate sources (e.g., `package-flavor-1.2.3` vs `package-1.2.3`).

## License

This project is licensed under the [GPL-2.0-only](LICENSE).
