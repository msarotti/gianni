# Flexible HTTP Client Script

A flexible HTTP client wrapper around curl with support for various request types and debugging features.

## Usage

```bash
./gianni.sh --url URL --method METHOD [OPTIONS]
```
## Required Parameters

| Parameter | Description |
|-----------|-------------|
| `--url URL` | Target URL |
| `--method METHOD` | HTTP method (GET, POST, PUT, DELETE, etc.) |

## Optional Parameters

| Parameter | Description |
|-----------|-------------|
| `--debug` | Enable XDEBUG_SESSION=vscode |
| `--verbose` | Enable curl verbose output |
| `--body FILE` | File containing request body (or form data for multipart) |
| `--content-type TYPE` | Content type: json, urlencoded, or multipart |
| `--file FILE` | File to upload (can be combined with --body for multipart) |
| `--cookie FILE` | File containing cookies to send with request |

## Examples

### Basic GET Request
```bash
./gianni.sh --url http://localhost:8080/api/test --method GET
```
### POST with JSON
```bash
./gianni.sh --url http://localhost:8080/api/data --method POST --body data.json --content-type json
```
### File Upload
```bash
./gianni.sh --url http://localhost:8080/upload --method POST --file document.pdf
```
### File Upload with Form Data
```bash
./gianni.sh --url http://localhost:8080/upload --method POST --file document.pdf --body form_data.txt
```
### Authenticated Request with Cookies
```bash
./gianni.sh --url http://localhost:8080/api/secure --method GET --cookie cookies.txt
```
### Debug Mode
```bash
./gianni.sh --url http://localhost:8080/api/data --method POST --body data.json --content-type json --debug
```
### Verbose Output
```bash
./http_client.sh --url http://localhost:8080/api/data --method POST --body data.json --content-type json --verbose
```
