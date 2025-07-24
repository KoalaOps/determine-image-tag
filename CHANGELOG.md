# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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