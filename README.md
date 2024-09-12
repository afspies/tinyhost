# tinyhost

Have you ever wanted to host a webpage? Me too!

Except no one wants to host their own server these days. Too much trouble keeping that thing secure, updated, properly firewalled. It's overkill for hosting just a tiny simple page.

You could just upload a static page to S3, but for a backend you'd need to add serverless product like AWS Lambda functions. But even those would need some place to store your data...

Introducing, serverless-less web hosting with `tinyhost`

Installation instructions:

```
pip install tinyhost
```

Host your first page, with a simple backend:
```
tinyhost examples/checkbox.html
```

Then share the provided link with your trusted friends and coworkers!

tinyhost will put your static page on S3, and provide it with a simple backend, also hosted on S3! It's literally S3 all the way down.

## How it works

tinyhost adds two Javascript functions to your page before it gets uploaded to cloud storage:

```js
/**
 * Fetches the state from an S3-backed datastore using a presigned GET URL.
 *
 * @async
 * @function fetchDatastore
 * @returns {Promise<Object|null>} The JSON data from the datastore, or null if there was an error.
 */
async function fetchDatastore();

/**
 * Uploads data to an automatically configured S3-backed datastore using a presigned POST URL and fields.
 *
 * @async
 * @function putDatastore
 * @param {Object} data - The single Object to be written to the datastore as JSON
 * @returns {Promise<void>} Resolves if the data is successfully uploaded, or logs an error if the upload fails.
 */
async function putDatastore(data);
```

You can use these two functions in your code to save and retrieve a single Javascript Object (serialized via JSON). 

Your datastore is stored in your S3 bucket right next to your webpage, and we internally create a presigned URL so that your page can read/write to it securely. All of this is automatically added to your HTML so you don't even need to think about it.

The link to your site, and your datastore will work for 1 week. But don't worry, after that, the data is still saved, you can just run tinyhost again on the same file to get a fresh link.

### Security
tinyhost is meant for sharing tiny websites and prototypes with trusted friends and coworkers. Anyone with a link to your page can see your site, and write anything they want to your page's datastore.

Datastores are limited to a max of 2MB, and no other permissions in your S3 account are ever granted. You also don't need to make your S3 bucket public, and in fact no permissions are ever altered on any objects.

## Advanced Usage
```
Usage: tinyhost [OPTIONS] HTML_FILE

  Hosts your html_file on an S3 bucket, and gives back a signed URL.

  Assumes that you have AWS credentials in your environment. Run `aws
  configure` if not.

  If you don't pass in an S3 bucket, the script will prompt you to create one,
  if it's possible. Otherwise, it will use the specified bucket

Options:
  --bucket TEXT       S3 bucket on which to host your static site
  --prefix TEXT       S3 bucket prefix to use
  --reset             Reset the data store back to an empty object
  --duration INTEGER  Length of time in seconds that this resulting link will
                      work for. Default is 1 week. Max is also 1 week.
  --help              Show this message and exit.
```


## Motivation

Internally at AI2, we use tinyhost to share little websites automatically generated by other AI tools. For example, you may be training a neural network classifier, and you want to ask your team to help tag a little bit of data.

1. Ask ChatGPT or Claude to load your training set and output a simple webpage that contains 20 examples via Jinja2 template. 

2. Host the resulting page with tinyhost.

3. Get feedback and quickly iterate on your ideas.

It's useful even without the datastore as a quick way to share a pretty HTML page, without worrying about having to clean up cloud resources later.

## Team

<!-- start team -->

**tinyhost** is developed and maintained by the AllenNLP team, backed by [the Allen Institute for Artificial Intelligence (AI2)](https://allenai.org/).
AI2 is a non-profit institute with the mission to contribute to humanity through high-impact AI research and engineering.
To learn more about who specifically contributed to this codebase, see [our contributors](https://github.com/allenai/tinyhost/graphs/contributors) page.

<!-- end team -->

## License

<!-- start license -->

**tinyhost** is licensed under [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).
A full copy of the license can be found [on GitHub](https://github.com/allenai/tinyhost/blob/main/LICENSE).

<!-- end license -->
