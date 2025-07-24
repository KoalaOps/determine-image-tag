# Determine Image Tag Action

A GitHub Action that generates consistent, unique image tags for container builds and releases. It supports multiple tag formats and handles duplicate prevention through automatic counters.

## Features

- **Multiple Tag Formats**: Choose from various tag formats to suit your workflow
- **Duplicate Prevention**: Automatically adds counters to ensure unique tags
- **Branch Normalization**: Converts branch names to valid tag formats
- **Length Limits**: Respects Kubernetes' 63-character limit for labels
- **Custom Tags**: Option to override with custom tag values
- **Pull Request Support**: Correctly handles PR branch names

## Usage

### Basic Usage

```yaml
- name: Determine Image Tag
  uses: koalaops/determine-image-tag@v1
  id: tag
  with:
    service_name: my-service

- name: Build and Push Docker Image
  run: |
    docker build -t ${{ steps.tag.outputs.tag }} .
    docker push ${{ steps.tag.outputs.tag }}
```

### Advanced Usage with Custom Format

```yaml
- name: Generate Tag for Production
  uses: koalaops/determine-image-tag@v1
  id: tag
  with:
    service_name: api-gateway
    tag_format: service-date-branch-counter
    max_length: 50
    include_counter: true
```

### Using Custom Tag

```yaml
- name: Use Custom Tag
  uses: koalaops/determine-image-tag@v1
  id: tag
  with:
    custom_tag: v1.2.3-release
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `service_name` | Service name to include in tag | No | `""` |
| `custom_tag` | Custom tag to use instead of auto-generation | No | `""` |
| `tag_format` | Tag format (see formats below) | No | `service-date-branch-counter` |
| `max_length` | Maximum tag length | No | `63` |
| `include_counter` | Whether to include counter for duplicates | No | `true` |
| `branch_ref` | Git branch reference | No | `github.ref` |
| `pull_request_ref` | Pull request head ref | No | `github.event.pull_request.head.ref` |
| `working_directory` | Working directory where the git repository is located | No | `.` |

### Tag Formats

- **`service-date-branch-counter`**: `{service}_{date}_{branch}_{counter}` (default)
  - Example: `api-gateway_2024-01-15_main_00`
- **`branch-date-counter`**: `{branch}_{date}_{counter}`
  - Example: `feature-login_2024-01-15_01`
- **`date-branch`**: `{date}_{branch}`
  - Example: `2024-01-15_develop`

## Outputs

| Output | Description |
|--------|-------------|
| `tag` | The generated image tag |
| `commit_hash` | The current git commit hash |
| `branch` | The normalized branch name |

## Examples

### Complete CI/CD Pipeline

```yaml
name: Build and Deploy

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Determine Image Tag
        uses: koalaops/determine-image-tag@v1
        id: tag
        with:
          service_name: my-service
          
      - name: Build Docker Image
        run: |
          docker build -t my-registry/${{ steps.tag.outputs.tag }} .
          
      - name: Push to Registry
        run: |
          docker push my-registry/${{ steps.tag.outputs.tag }}
          
      - name: Deploy
        run: |
          kubectl set image deployment/my-service \
            app=my-registry/${{ steps.tag.outputs.tag }}
```

### Multi-Environment Deployment

```yaml
- name: Generate Dev Tag
  uses: koalaops/determine-image-tag@v1
  id: dev-tag
  with:
    service_name: api
    tag_format: branch-date-counter
    
- name: Generate Prod Tag
  if: github.ref == 'refs/heads/main'
  uses: koalaops/determine-image-tag@v1
  id: prod-tag
  with:
    service_name: api
    custom_tag: ${{ github.event.release.tag_name }}
```

### Monorepo with Multiple Services

```yaml
strategy:
  matrix:
    service: [auth, api, worker]
    
steps:
  - name: Generate Tag for ${{ matrix.service }}
    uses: koalaops/determine-image-tag@v1
    id: tag
    with:
      service_name: ${{ matrix.service }}
      
  - name: Build ${{ matrix.service }}
    run: |
      docker build -t registry/${{ matrix.service }}:${{ steps.tag.outputs.tag }} \
        -f services/${{ matrix.service }}/Dockerfile .
```

### Custom Working Directory

```yaml
- name: Checkout to subdirectory
  uses: actions/checkout@v3
  with:
    path: my-repo

- name: Generate Tag
  uses: koalaops/determine-image-tag@v1
  id: tag
  with:
    service_name: my-service
    working_directory: my-repo
```

## How It Works

1. **Branch Detection**: Automatically detects the current branch from GitHub context
2. **Normalization**: Converts special characters in branch names to underscores
3. **Date Generation**: Uses current date in `YYYY-MM-DD` format
4. **Counter Logic**: 
   - Queries remote git tags to count existing tags with the same prefix
   - Falls back to local tags if remote is unavailable
   - Adds a zero-padded counter (00, 01, 02, etc.)
5. **Length Management**: Truncates tag if it exceeds the maximum length while preserving the counter

## Notes

- Tags are normalized to be compatible with Docker and Kubernetes naming requirements
- The action requires git to be available in the runner environment
- When using counters, the action needs access to git tags (either remote or local)
- By default, the action runs in the current directory (`.`) but you can specify a different `working_directory` if your repository is checked out elsewhere

## License

This action is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Support

For issues and feature requests, please use the [GitHub Issues](https://github.com/koalaops/determine-image-tag/issues) page.