yeoman-s3-example
=================

This is a sample app used for training on another way to host static websites.

The procedure for creating this application yourself is as follows:

### Create a Yeoman webapp

  * npm install -g yo bower grunt-cli generator-webapp
  * mkdir static-site && cd static-site
  * yo webapp

You're basic Yeoman webapp should be working now. Start up grunt to see if it's working.

    $ grunt server

Thanks to the [yeoman team](http://yeoman.io/) for sharing this great web development workflow.
 
### Install `grunt-s3`

    $ npm install grunt-s3 --save-dev

Thanks to [pifantastic](https://github.com/pifantastic/grunt-s3) for the grunt-s3 task.
  
### Add `grunt-s3` config to `Gruntfile`

```javascript
aws: grunt.file.readJSON('./aws.json'),
s3: {
    options: {
        key: '<%= aws.key %>',
        secret: '<%= aws.secret %>',
        bucket: '<%= aws.bucket %>',
        access: 'public-read',
        headers: {
            // Two Year cache policy (1000 * 60 * 60 * 24 * 730)
            'Cache-Control': 'max-age=630720000, public',
            'Expires': new Date(Date.now() + 63072000000).toUTCString()
        }
    },
    dist: {
        options: {
            encodePaths: true,
            maxOperations: 20
        },
        upload: [{
            src: 'dist/**',
            dest: '',
            rel: 'dist',
            options: { gzip: true }
        }]
    }
}
```

### Add `s3` to your `build` task

```javascript
grunt.registerTask('build', [
    'clean:dist',
    '<...snip...>',
    'usemin',
    's3'
]);
```

### Add `s3` credentials in `aws.json` file

```javascript
{
  "key": "AWS_ACCESS_KEY_ID",
  "secret": "AWS_SECRET_ACCESS_KEY",
  "bucket": "yeoman-s3"
}
```

### Build and watch it deploy!

    $ grunt build
  
Navigate to your [yeoman-s3 bucket website](http://yeoman-s3.s3-website-us-east-1.amazonaws.com/) and see your work.
