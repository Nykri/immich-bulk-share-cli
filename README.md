# immich-bulk-share-cli
Python script for managing album sharing permissions via Immich API. Supports listing current album permissions and bulk updates through CSV files.

## Features

- List all albums with their sharing permissions
- Export album details including first photo date, last photo date, and photo count
- Bulk update sharing permissions from CSV
- Automatic credential storage
- Dry-run mode for safe testing
- Support for both comma and semicolon delimited CSV files

## Requirements

- Python 3.6+
- `requests` library

## Installation

```bash
pip install requests
```

## Usage

### First Time Setup

Save your API credentials (they will be stored securely in `~/.config/immich-bulk-share`):

```bash
python album_processor.py login --url https://your-immich-server --api-key YOUR_API_KEY
```

Test connection without saving credentials:
```bash
python album_processor.py login --url https://your-immich-server --api-key YOUR_API_KEY --dry-run
```

### List Albums and Permissions

Export all albums and their sharing permissions to CSV:

```bash
python album_processor.py list-all [--output albums.csv] [--dry-run]
```

If no output file is specified, creates `albums_YYYYMMDD_HHMMSS.csv`

Preview what would be exported:
```bash
python album_processor.py list-all --dry-run
```

### Update Sharing Permissions

Update album sharing permissions from CSV:

```bash
python album_processor.py share-albums --input albums.csv [--dry-run]
```

Preview sharing changes without applying them:
```bash
python album_processor.py share-albums --input albums.csv --dry-run
```

## CSV Format

### Structure
Required columns:
- AlbumName
- AlbumId
- Role
- User columns (User 1, User 2, etc.)

Additional information columns:
- FirstPhoto (date of the first photo in album)
- LastPhoto (date of the last photo in album)
- Photos (number of photos in album)

Example:
```csv
AlbumName;AlbumId;FirstPhoto;LastPhoto;Photos;Role;User 1;User 2
Vacation 2023;abc123;2023-01-01 12:00;2023-01-07 18:30;150;viewer;user1@example.com;user2@example.com
Holiday Photos;def456;2023-12-24 10:00;2023-12-26 22:15;75;editor;user3@example.com;
```

### Processing Rules
- Users will be added to albums with specified roles
- Existing users' roles will be updated if different
- Users not in CSV will be removed from album
- Empty cells are ignored
- Albums are automatically sorted by first photo date (newest first)

## Error Handling

The script handles:
- Network connectivity issues
- Server timeouts
- Invalid CSV formats
- Missing users
- Invalid URLs
- Invalid credentials

Operation results include:
- Number of processed albums
- Successful/failed updates
- Removed users
- List of users not found

## Security

- Credentials are stored in `~/.config/immich-bulk-share/config.json`
- Config file permissions are set to 600 (user read/write only)
- Supports HTTPS for secure communication
- Dry-run mode available for safe testing

## Exit Codes
- 0: Success
- 1: Error (file not found, network error, invalid credentials, etc.)

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License

[MIT](LICENSE)

