# rotating-s3-stream

This module creates a local write stream that periodically syncs and rotates to S3 based on file size and/or file duration. A write stream is created locally, then when the file exceeds `maxFileSize` or `maxFileDuration`, it is copied to S3, deleted locally, and a new write stream is created. By default, the files are `ISO 8601` DateTime format, but you can also supply a custom naming function with the `createFileName` option.

## Install
`rotating-s3-stream` has only been tested on MacOS and Linux. For maximum efficienty, it uses `child_process` along with the native AWS CLI, so that must be installed and configured separately. Instructions on how to do that can be found [here](http://docs.aws.amazon.com/cli/latest/userguide/installing.html).

## Stream Options
- **localPrefix** - {String} Required. Used as the local prefix for write stream.
- **s3Prefix** - {String} Required. Used as the S3 prefix for syncing the write stream.
- **maxFileSize** - {Number} Required. The max file size (bytes) the write stream can reach before rotating.
- **maxFileAge** - {Number} Required. The max file age (seconds) the write stream can be before rotating.
- **createFileName** - {Function} Optional. You can provide your own file-naming function. Defaults to ISO 8601.
- **checkRotationSchedule** - {String} Optional. Provide a cron-style string that is the schedule for checking if a sync is necessary. Defaults to `* * * * *`

## Example usage
```javascript
const RotatingS3Stream = require('rotating-s3-stream')

const streamOptions = {
  localPrefix: '/tmp/rotating-stream-logs',
  s3Prefix: 's3://my-bucket/logs',
  maxFileSize: 1000000,                       // 1MB
  maxFileAge: 3600,                           // 1 hour
  createFileName: () => Date.now(),           // Use unix timestamps
  checkRotationSchedule: '* * * * * */5'      // Every 5 seconds
}

const stream = new RotatingS3Stream(streamOptions)

const logData = {
  user: 1,
  action: 'log-in'
}

// Will appear locally at: /tmp/rotating-stream-logs/<Current timestamp>
// And will be synced to S3://my-bucket/logs/<Current timestamp>
stream.write(JSON.stringify(logData))
```
