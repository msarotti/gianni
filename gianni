#!/bin/sh

# Flexible HTTP client script with curl
# Usage: ./http_client.sh --url URL --method METHOD [OPTIONS]
#
# Required parameters:
#   --url URL           Target URL
#   --method METHOD     HTTP method (GET, POST, PUT, DELETE, etc.)
#
# Optional parameters:
#   --debug            Enable XDEBUG_SESSION=vscode
#   --verbose          Enable curl verbose output
#   --body FILE        File containing request body (or form data for multipart)
#   --content-type TYPE Content type: json, urlencoded, or multipart
#   --file FILE        File to upload (can be combined with --body for multipart)
#   --cookie FILE      File containing cookies to send with request

# Initialize variables
URL=""
METHOD=""
DEBUG_MODE=false
VERBOSE_MODE=false
BODY_FILE=""
CONTENT_TYPE=""
UPLOAD_FILE=""
COOKIE_FILE=""

# Function to show usage
show_usage() {
    echo "Usage: $0 --url URL --method METHOD [OPTIONS]"
    echo ""
    echo "Required:"
    echo "  --url URL           Target URL"
    echo "  --method METHOD     HTTP method (GET, POST, PUT, DELETE, etc.)"
    echo ""
    echo "Optional:"
    echo "  --debug            Enable XDEBUG_SESSION=vscode"
    echo "  --verbose          Enable curl verbose output"
    echo "  --body FILE        File containing request body (or form data for multipart)"
    echo "  --content-type TYPE Content type: json, urlencoded, or multipart"
    echo "  --file FILE        File to upload (can be combined with --body for multipart)"
    echo "  --cookie FILE      File containing cookies to send with request"
    echo ""
    echo "Examples:"
    echo "  $0 --url http://localhost:8080/api/test --method GET"
    echo "  $0 --url http://localhost:8080/api/data --method POST --body data.json --content-type json"
    echo "  $0 --url http://localhost:8080/upload --method POST --file document.pdf"
    echo "  $0 --url http://localhost:8080/upload --method POST --file document.pdf --body form_data.txt"
    echo "  $0 --url http://localhost:8080/api/secure --method GET --cookie cookies.txt"
    echo ""
    echo "Multipart scenarios:"
    echo "  - File only: --file document.pdf"
    echo "  - File + form data: --file document.pdf --body form_data.txt"
    echo "  - Form data only with multipart: --body form_fields.txt --content-type multipart"
}

# Parse command line arguments
while [ $# -gt 0 ]; do
    case $1 in
        --url)
            URL="$2"
            shift 2
            ;;
        --method)
            METHOD="$2"
            shift 2
            ;;
        --debug)
            DEBUG_MODE=true
            shift
            ;;
        --verbose)
            VERBOSE_MODE=true
            shift
            ;;
        --body)
            BODY_FILE="$2"
            shift 2
            ;;
        --content-type)
            CONTENT_TYPE="$2"
            shift 2
            ;;
        --file)
            UPLOAD_FILE="$2"
            shift 2
            ;;
        --cookie)
            COOKIE_FILE="$2"
            shift 2
            ;;
        -h|--help)
            show_usage
            exit 0
            ;;
        *)
            echo "Unknown parameter: $1"
            show_usage
            exit 1
            ;;
    esac
done

# Validate required parameters
if [ -z "$URL" ]; then
    echo "Error: --url parameter is required"
    show_usage
    exit 1
fi

if [ -z "$METHOD" ]; then
    echo "Error: --method parameter is required"
    show_usage
    exit 1
fi

# Build URL with debug parameter if needed
if [ "$DEBUG_MODE" = true ]; then
    if echo "$URL" | grep -q '?'; then
        FINAL_URL="${URL}&XDEBUG_SESSION=vscode"
    else
        FINAL_URL="${URL}?XDEBUG_SESSION=vscode"
    fi
    if [ "$VERBOSE_MODE" = true ]; then
        echo "Debug mode: enabled"
    fi
else
    FINAL_URL="$URL"
    if [ "$VERBOSE_MODE" = true ]; then
        echo "Debug mode: disabled"
    fi
fi

# Set verbose flag and build cookie option
if [ "$VERBOSE_MODE" = true ]; then
    CURL_VERBOSE="-v"
    echo "Verbose mode: enabled"
else
    CURL_VERBOSE=""
fi

# Build cookie option
if [ -n "$COOKIE_FILE" ]; then
    CURL_COOKIE="-b $COOKIE_FILE"
else
    CURL_COOKIE=""
fi

# Validate files exist
if [ -n "$BODY_FILE" ] && [ ! -f "$BODY_FILE" ]; then
    echo "Error: Body file '$BODY_FILE' not found"
    exit 1
fi

if [ -n "$UPLOAD_FILE" ] && [ ! -f "$UPLOAD_FILE" ]; then
    echo "Error: Upload file '$UPLOAD_FILE' not found"
    exit 1
fi

if [ -n "$COOKIE_FILE" ] && [ ! -f "$COOKIE_FILE" ]; then
    echo "Error: Cookie file '$COOKIE_FILE' not found"
    exit 1
fi

# Validate content type
if [ -n "$CONTENT_TYPE" ]; then
    case "$CONTENT_TYPE" in
        json|urlencoded|multipart)
            ;;
        *)
            echo "Error: Invalid content-type '$CONTENT_TYPE'. Use 'json', 'urlencoded', or 'multipart'"
            exit 1
            ;;
    esac
fi

# Display configuration (only in verbose mode)
if [ "$VERBOSE_MODE" = true ]; then
    echo "Method: $METHOD"
    echo "URL: $FINAL_URL"
    if [ -n "$BODY_FILE" ]; then
        echo "Body file: $BODY_FILE"
    fi
    if [ -n "$UPLOAD_FILE" ]; then
        echo "Upload file: $UPLOAD_FILE"
    fi
    if [ -n "$COOKIE_FILE" ]; then
        echo "Cookie file: $COOKIE_FILE"
    fi

    # Determine request type and content-type
    if [ -n "$UPLOAD_FILE" ] && [ -n "$BODY_FILE" ]; then
        echo "Content-Type: multipart/form-data (file + form data)"
    elif [ -n "$UPLOAD_FILE" ]; then
        echo "Content-Type: multipart/form-data (file only)"
    elif [ -n "$BODY_FILE" ]; then
        case "$CONTENT_TYPE" in
            json)
                echo "Content-Type: application/json"
                ;;
            urlencoded)
                echo "Content-Type: application/x-www-form-urlencoded"
                ;;
            multipart)
                echo "Content-Type: multipart/form-data (form data only)"
                ;;
            "")
                echo "Content-Type: auto-detect"
                ;;
        esac
    fi

    echo ""
    echo "Executing curl command..."
fi

# Execute the command based on the scenario
if [ -n "$UPLOAD_FILE" ] && [ -n "$BODY_FILE" ]; then
    # Scenario: File upload + form data (multipart)
    if [ "$VERBOSE_MODE" = true ]; then
        echo "Sending multipart request with file and form data..."
    fi
    curl -X "$METHOD" $CURL_VERBOSE $CURL_COOKIE \
        -F "file=@$UPLOAD_FILE" \
        -F "data=<$BODY_FILE" \
        "$FINAL_URL"
        
elif [ -n "$UPLOAD_FILE" ]; then
    # Scenario: File upload only (multipart)
    if [ "$VERBOSE_MODE" = true ]; then
        echo "Sending file upload..."
    fi
    curl -X "$METHOD" $CURL_VERBOSE $CURL_COOKIE \
        -F "file=@$UPLOAD_FILE" \
        "$FINAL_URL"
        
elif [ -n "$BODY_FILE" ]; then
    # Scenario: Body data only
    case "$CONTENT_TYPE" in
        json)
            curl -X "$METHOD" $CURL_VERBOSE $CURL_COOKIE \
                --header "Content-Type: application/json" \
                --data @"$BODY_FILE" \
                "$FINAL_URL"
            ;;
        urlencoded)
            curl -X "$METHOD" $CURL_VERBOSE $CURL_COOKIE \
                --header "Content-Type: application/x-www-form-urlencoded" \
                --data @"$BODY_FILE" \
                "$FINAL_URL"
            ;;
        multipart)
            # Send body file as multipart form data
            curl -X "$METHOD" $CURL_VERBOSE $CURL_COOKIE \
                --form @"$BODY_FILE" \
                "$FINAL_URL"
            ;;
        *)
            # Auto-detect content type
            curl -X "$METHOD" $CURL_VERBOSE $CURL_COOKIE \
                --data @"$BODY_FILE" \
                "$FINAL_URL"
            ;;
    esac
else
    # Scenario: No body or file
    curl -X "$METHOD" $CURL_VERBOSE $CURL_COOKIE "$FINAL_URL"
fi

# echo # Add newline after curl output
