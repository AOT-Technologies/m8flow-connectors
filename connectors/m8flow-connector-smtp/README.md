# M8flow Connector SMTP

SMTP connector for **m8flow**: send plain-text and HTML email through an SMTP server, with optional authentication, STARTTLS, CC/BCC/Reply-To headers, and attachments.

## Supported actions

| Action     | Command         | Description                                         |
|------------|-----------------|-----------------------------------------------------|
| Send email | `SendHTMLEmail` | Send email with a plain-text body and optional HTML |

## Command

- **SendHTMLEmail** - Required: `smtp_host`, `smtp_port`, `email_subject`, `email_body`, `email_to`, `email_from`.
- Optional: `smtp_user`, `smtp_password`, `smtp_starttls`, `email_body_html`, `email_cc`, `email_bcc`, `email_reply_to`, `attachments`.

The m8flow connector proxy introspects each command's `__init__` parameters, so these become the configuration options in the workflow UI.

## Attachments

`attachments` accepts a list of objects in one of these formats:

1. File path:

   ```json
   {
     "filename": "report.pdf",
     "path": "/attachments/report.pdf",
     "content_type": "application/pdf"
   }
   ```

2. Base64 content:

   ```json
   {
     "filename": "report.pdf",
     "content_base64": "<BASE64>",
     "content_type": "application/pdf"
   }
   ```

Attachment behavior is controlled by these environment variables:

- `M8FLOW_CONNECTOR_SMTP_ATTACHMENTS_USER_ACCESS_DIR`: allowed root directory for path-based attachments. Defaults to `/attachments`.
- `M8FLOW_CONNECTOR_SMTP_ATTACHMENTS_LIMIT_IN_MB`: attachment size limit in MB. Defaults to `100`, with a hard maximum of `100`.
- `M8FLOW_CONNECTOR_SMTP_TIMEOUT_SECONDS`: SMTP connection and send timeout in seconds. Defaults to `30`.

Path-based attachments must be absolute paths under the configured attachments directory. Base64 attachments are size-checked before decoding.

## Security considerations

- Credentials should not be hardcoded in BPMN models.
- Sensitive data should be provided through secure environment variables or secrets management mechanisms.
- STARTTLS should be enabled whenever supported by the SMTP server.
- Path-based attachments are restricted to the configured attachment directory to prevent arbitrary filesystem access.

## Testing

```bash
pytest tests/
```

- Unit tests cover env parsing, recipient handling, attachment validation, and `SendHTMLEmail` success and error paths.
- Manual validation against a real SMTP server is still recommended.
