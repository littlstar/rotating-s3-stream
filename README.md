# rotating-s3-stream

This module creates a local write stream that periodically rotates and syncs to S3 based on file size and/or file duration.

## Example usage
```javascript
const RotatingS3Stream = require('rotating-s3-stream')

const streamOptions = {
  localPrefix: '/tmp/rotating-stream-logs', // Required. Used as the local prefix for write stream.
  s3Prefix: 's3://my-bucket/path/to/logs',  // Required. Used as the S3 prefix for syncing the write stream.
  maxFileSize: 1000000                      // Required. The max file size (bytes) the write stream can reach before rotating.
  maxFileAge: 3600                          // Required. The max file age (seconds) the write stream can be before rotating.
  createFileName: () => Date.now()          // Optional. You can provide your own file-naming function. Defaults to ISO 8601 date format.
}

const stream = new RotatingS3Stream(streamOptions)

const logData = {
  user: 1,
  action: 'log-in'
}

stream.write(JSON.stringify(logData))
```
