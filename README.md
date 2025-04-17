# SnapRAIDer

## Description
SnapRAIDer is a powerful, customizable bash script that dynamically generates SnapRAID configuration files for your storage array. It simplifies the setup and maintenance of SnapRAID by automatically detecting your drives and creating an optimized configuration based on your specific needs.

![SnapRAID Logo](https://www.snapraid.it/images/title.png)

## Table of Contents
* [Key Features](#key-features)
* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Usage](#usage)
* [Configuration](#configuration)
* [Advanced Options](#advanced-options)
* [How It Works](#how-it-works)
* [FAQ](#faq)
* [Credits](#credits)
* [License](#license)
* [Contributing](#contributing)

## Key Features
- **Automatic Configuration:** Dynamically generates snapraid.conf by detecting your storage drives
- **Tmux Integration:** Runs in a tmux session for reliability and monitoring
- **Smart Regeneration:** Checks modification dates to avoid unnecessary regeneration
- **Flexible Drive Layouts:** Supports multiple data pools with configurable naming
- **Content File Distribution:** Creates redundant content files across drives for better reliability
- **Comprehensive Options:** Supports all SnapRAID configuration options in an easy-to-use format
- **Path Customization:** Fully customizable paths for both content and data
- **Sync Integration:** Optionally runs snapraid sync after configuration updates

## Prerequisites
- SnapRAID installed on your system
- Tmux (for session management)
- Bash 4.0 or newer
- Basic understanding of SnapRAID concepts

## Installation
1. Download the snapraider.sh script to your preferred location:
   ```bash
   wget https://raw.githubusercontent.com/Nillows/snapraider/main/snapraider.sh
   ```

2. Make the script executable:
   ```bash
   chmod +x snapraider.sh
   ```

3. Edit the script to customize the configuration section for your environment:
   ```bash
   nano snapraider.sh
   ```

4. Set up your desired directory structure (or use the script to automatically detect existing drives)

## Usage
Run the script with the following command:
```bash
./snapraider.sh
```

For scheduled updates, add it to your crontab:
```bash
# Run daily at 1am
0 1 * * * /path/to/snapraider.sh > /var/log/snapraider.log 2>&1
```

The script will:
1. Check for modifications to your drive structure (if CHECK_DATE=true)
2. Generate a new snapraid.conf file if needed
3. Optionally run snapraid sync (if SYNC_AFTER_GENERATION=true)

## Configuration
Edit the USER CONFIG SECTION at the top of the script to customize behavior:

### Basic Configuration
```bash
# Basic settings
FILENAME="snapraid.conf"  # Name of the configuration file to generate
DEST_DIR="/etc"           # Directory to store the configuration file
MEDIA_ROOT="/media/data"  # Base directory containing all your media drives
```

### Drive Pool Setup
```bash
# Define your storage pools
POOL_DEFINITIONS=(
    "Parity Drives:parity:parity"
    "TV Mount points:tv:tvshows"
    "Movies Mount points:movies:movies"
    # Add more as needed with format: "Name:prefix:path"
)
```

### Content File Options
```bash
# Content file configuration
PRIMARY_CONTENT_PATH="/var/snapraid.content"  # Primary content file location
CONTENT_FILE_PER_DRIVE=true                  # Add content files on each drive
RELATIVE_PATH_PER_DRIVE="."                  # Path within each drive for content files
```

## Advanced Options
The script supports all SnapRAID's advanced features, each with detailed documentation:

| Feature | Description |
|---------|-------------|
| SMART Monitoring | Monitor drive health with smartctl integration |
| Disk Scrubbing | Configure automatic data validation |
| CPU Affinity | Control which CPU cores SnapRAID uses |
| I/O Controls | Set cache size and bandwidth limits |
| Hash Algorithms | Select specific hash algorithms for checksums |
| Logging | Configure detailed operation logging |
| Pool Feature | Enable unified directory views of your content |

See the script's configuration section for complete documentation on each option.

## How It Works
The script follows this workflow:

1. **Initial Checks:**
   - Verifies if configuration regeneration is needed based on drive modifications
   - Terminates any existing tmux sessions with the same name

2. **Script Generation:**
   - Creates a separate script for the tmux session with all configuration variables
   - Uses path templating to handle variable substitution safely

3. **Drive Detection:**
   - Scans each defined pool location for drives matching the pattern
   - Sorts and numbers drives to ensure consistent naming

4. **Configuration Building:**
   - Generates proper SnapRAID configuration directives for parity and data drives
   - Creates content file entries according to your settings
   - Adds all specified advanced options

5. **Synchronization:**
   - Optionally executes snapraid sync to apply the configuration

## FAQ

### What is the advantage of using this script over a static configuration?
SnapRAIDer automatically adapts to changes in your drive pool, ensuring your configuration is always up-to-date without manual editing. It also enables easy regeneration if the config file is lost.

### How does the date checking feature work?
The script compares the modification timestamps of your HDD directories against the timestamp of your existing snapraid.conf file. It only regenerates the config if drives have been modified since the last generation.

### Why use tmux for this script?
Tmux ensures the generation process can complete even if your SSH session disconnects or the terminal closes. It also provides a window to monitor long-running sync operations.

### How can I add more drive pools?
Simply add new entries to the POOL_DEFINITIONS array following the format "Friendly Name:prefix:relative_path".

## Credits
- Original concept by Thomas Wollin
- Inspired by the SnapRAID project: https://www.snapraid.it/

## License
This project is licensed under the MIT License - see the LICENSE file for details.

## Contributing
Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
