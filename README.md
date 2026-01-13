# File Integrity Monitor (FIM) for Windows

A robust, real-time file integrity monitoring tool for Windows that detects unauthorized changes, additions, and deletions in your critical directories using SHA-256 hashing.

![Python Version](https://img.shields.io/badge/python-3.7%2B-blue)
![Platform](https://img.shields.io/badge/platform-Windows-lightgrey)
![License](https://img.shields.io/badge/license-MIT-green)

## 🔍 Features

- **Real-time Monitoring**: Continuous surveillance of directories with configurable scan intervals
- **SHA-256 Hashing**: Cryptographic hash verification for detecting even single-byte modifications
- **Baseline Management**: Create and maintain integrity baselines for comparison
- **Change Detection**: Identifies modified, added, and deleted files
- **Comprehensive Logging**: Dual logging to console and file (`fim.log`)
- **Ignore List**: Exclude specific files from monitoring
- **Security Hardened**: 
  - Path traversal prevention
  - File size limits (500MB default)
  - Permission validation
  - Hidden file exclusion
- **Error Resilient**: Graceful handling of permission errors, missing files, and corrupted data

## 📋 Requirements

- Python 3.7 or higher
- Windows OS
- No external dependencies (uses only Python standard library)

## 🚀 Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/file-integrity-monitor.git
cd file-integrity-monitor
```

2. No additional installation required! The tool uses only Python standard library.

## 📖 Usage

### Create a Baseline

Before monitoring, create a baseline snapshot of your directory:

```bash
python fim.py --create-baseline C:\Path\To\Monitor
```

This scans all files recursively and stores their hashes in `baseline.json`.

### One-Time Integrity Check

Perform a single integrity check against the baseline:

```bash
python fim.py --monitor C:\Path\To\Monitor
```

### Real-Time Monitoring

Start continuous monitoring with alerts:

```bash
python fim.py --monitor C:\Path\To\Monitor --realtime
```

Press `Ctrl+C` to stop monitoring.

### Advanced Options

**Custom scan interval** (default is 5 seconds):
```bash
python fim.py --monitor C:\Path\To\Monitor --realtime --interval 10
```

**Custom baseline file**:
```bash
python fim.py --baseline my_baseline.json --create-baseline C:\MyFolder
python fim.py --baseline my_baseline.json --monitor C:\MyFolder --realtime
```

**Ignore specific files**:
```bash
python fim.py --monitor C:\MyFolder --realtime --ignore temp.txt cache.db debug.log
```

**Auto-update baseline** (use with caution in development environments):
```bash
python fim.py --monitor C:\MyFolder --realtime --update-baseline
```

## 🎯 Use Cases

- **System Administrators**: Monitor critical system directories for unauthorized changes
- **Security Teams**: Detect malware or intrusion attempts by tracking file modifications
- **Compliance**: Maintain audit trails for regulatory requirements (PCI-DSS, HIPAA)
- **DevOps**: Monitor configuration files in production environments
- **Personal Use**: Protect important documents and sensitive files

## 📊 Example Output

### Creating Baseline
```
=== Creating Baseline ===
Scanning directory: C:\MyFolder
  Scanned: documents\report.docx
  Scanned: config\settings.ini
  Scanned: scripts\backup.py

Baseline created with 3 files
Baseline saved to: baseline.json
```

### Alert on Detection
```
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
⚠️  ALERT - INTEGRITY VIOLATION DETECTED!
Time: 2026-01-13 14:30:45
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

🔴 [MODIFIED FILES: 1]
  • config\settings.ini
    Old Hash: a3c7b8f92e45d61c...
    New Hash: b1d4e7a8c9f2e5b8...
    Size: 1024 → 1156 bytes

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```

## 🛡️ Security Features

### Path Traversal Prevention
All file paths are validated and sanitized to prevent directory traversal attacks.

### File Size Limits
Files larger than 500MB are automatically skipped to prevent memory exhaustion.

### Permission Handling
Gracefully handles files without read permissions without crashing.

### Hidden File Exclusion
Automatically skips hidden files and Windows system directories (`$RECYCLE.BIN`, `System Volume Information`).

### Baseline Validation
JSON structure and integrity verification before loading baselines.

## 📝 Baseline File Format

The baseline is stored as a JSON file with the following structure:

```json
{
  "path/to/file.txt": {
    "hash": "a3c7b8f92e45d61c8f3a2b1e9d4c5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e",
    "size": 1024,
    "modified": 1704672345.123456,
    "created": 1704585945.123456
  }
}
```

## 🔧 Command-Line Options

```
usage: fim.py [-h] [--create-baseline DIR] [--monitor DIR] [--baseline BASELINE]
              [--realtime] [--interval INTERVAL] [--update-baseline]
              [--ignore FILE [FILE ...]]

File Integrity Monitor for Windows

optional arguments:
  -h, --help            show this help message and exit
  --create-baseline DIR
                        Create a baseline for the specified directory
  --monitor DIR         Monitor the specified directory for changes
  --baseline BASELINE   Baseline file name (default: baseline.json)
  --realtime            Enable real-time continuous monitoring
  --interval INTERVAL   Scan interval in seconds for real-time monitoring (default: 5)
  --update-baseline     Automatically update baseline after detecting changes (use with caution)
  --ignore FILE [FILE ...]
                        File(s) to ignore during monitoring
```

## 📁 Project Structure

```
file-integrity-monitor/
│
├── fim.py              # Main script
├── baseline.json       # Generated baseline file (after first run)
├── fim.log            # Log file (auto-generated)
├── README.md          # This file
├── LICENSE            # MIT License
└── .gitignore         # Git ignore file
```

## 🔄 Workflow Example

```bash
# Step 1: Create baseline for your Documents folder
python fim.py --create-baseline C:\Users\YourName\Documents

# Step 2: Start real-time monitoring
python fim.py --monitor C:\Users\YourName\Documents --realtime --interval 10

# Step 3: Any changes will trigger immediate alerts
# Press Ctrl+C to stop when done
```

## 📊 Logging

All operations are logged to `fim.log` with timestamps:

```
2026-01-13 14:30:45,123 - INFO - Baseline loaded successfully: 42 files
2026-01-13 14:30:45,456 - INFO - Real-time monitoring started for C:\MyFolder
2026-01-13 14:31:15,789 - WARNING - Modified: config\settings.ini
```

## ⚠️ Important Notes

1. **Baseline Security**: Store your baseline file securely. If an attacker modifies both your files and the baseline, changes won't be detected.

2. **Performance**: Scanning large directories with many files may take time. Adjust the `--interval` parameter based on your needs.

3. **Auto-Update Baseline**: Use `--update-baseline` only in development environments where you expect legitimate changes. In production, review changes before updating the baseline.

4. **Large Files**: Files over 500MB are skipped by default. Modify `max_file_size` in the code if needed.

5. **Windows-Specific**: This tool is designed for Windows. Path handling and system directory exclusions are Windows-specific.

## 🐛 Troubleshooting

### "Permission denied" errors
Run the script with appropriate permissions or exclude restricted directories.

### Baseline file corrupted
Delete `baseline.json` and recreate with `--create-baseline`.

### Too many alerts
Use `--ignore` to exclude frequently changing files like logs or caches.

### High CPU usage
Increase the `--interval` value to reduce scan frequency.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Inspired by OSSEC and Tripwire file integrity monitoring systems
- Built with Python's robust standard library
- Thanks to the security community for best practices and guidance

## 📧 Contact

For questions, suggestions, or issues:
- Open an issue on GitHub
- Email: your.email@example.com

## 🗺️ Roadmap

- [ ] Email notifications on alerts
- [ ] Windows Event Log integration
- [ ] GUI interface
- [ ] Multi-platform support (Linux, macOS)
- [ ] Database backend for baseline storage
- [ ] Performance optimizations for very large directories
- [ ] Exclusion patterns (wildcards, regex)
- [ ] Scheduled scanning via Windows Task Scheduler integration

---

**⚡ Stay secure! Monitor your files, detect intrusions early.**
