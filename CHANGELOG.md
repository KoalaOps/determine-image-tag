# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.2] - 2025-08-01

### Added
- New `branch-date` tag format that outputs `{branch}_{date}` without counter
- Support for underscore aliases for all tag formats (e.g., `branch_date_counter` works the same as `branch-date-counter`)

### Changed
- Updated documentation with examples of new format and underscore alias usage

## [1.0.1] - 2024-01-26

### Added
- Configurable `branch_separator` input to customize character used for normalizing branch names (default: `-`)

### Fixed
- Counter generation now produces correct 2-digit format (00, 01, 02) instead of 4-digit format (0000)
- Branch_ref input now correctly takes priority over PR ref when explicitly provided
- Test expectations updated for new hyphen default separator

### Changed
- Default branch separator changed from underscore (`_`) to hyphen (`-`) for better Kubernetes compatibility
- Simplified counter generation logic using `grep -c` for more reliable counting
- Removed example workflow that required AWS authentication

## [1.0.0] - 2024-01-15

### Added
- Initial release of the Determine Image Tag action
- Support for multiple tag formats:
  - `service-date-branch-counter` (default)
  - `branch-date-counter`
  - `date-branch`
- Automatic counter generation for duplicate prevention with precise matching
- Branch name normalization for Docker/Kubernetes compatibility (handles `/`, `:`, `@`, `#`)
- Custom tag override option
- Pull request branch detection
- Configurable maximum tag length with smart truncation
- Comprehensive outputs including tag, commit hash, and normalized branch name
- Fallback to local tags when remote tags are unavailable
- Input validation for max_length parameter
- Error handling for git operations
- Working directory configuration support