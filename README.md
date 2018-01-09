# helm-s3

[![CircleCI](https://circleci.com/gh/hypnoglow/helm-s3.svg?style=shield)](https://circleci.com/gh/hypnoglow/helm-s3)
[![License MIT](https://img.shields.io/badge/license-MIT-blue.svg?style=flat)](LICENSE)
[![GitHub release](https://img.shields.io/github/release/hypnoglow/helm-s3.svg)](https://github.com/hypnoglow/helm-s3/releases)

The Helm plugin that provides s3 protocol support. 

This allows you to have private Helm chart repositories hosted on Amazon S3.

## Install

The installation itself is simple as:

    $ helm plugin install https://github.com/hypnoglow/helm-s3.git

You can install a specific release version: 

    $ helm plugin install https://github.com/hypnoglow/helm-s3.git --version 0.4.0

To use the plugin, you do not need any special dependencies. The installer will
download versioned release with prebuilt binary from [github releases](https://github.com/hypnoglow/helm-s3/releases).
However, if you want to build the plugin from source, or you want to contribute
to the plugin, please see [these instructions](.github/CONTRIBUTING.md).

#### Note on AWS authentication

Because this plugin assumes private access to S3, you need to provide valid AWS credentials.
You can do this in [the same manner](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html) as for `AWS CLI` tool.

So, if you want to use the plugin and you are already using `AWS CLI` - you are 
good to go, no additional configuration required. Otherwise, follow [the official guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html) 
to set up credentials.

To minimize security issues, remember to configure your IAM user policies properly. 
As an example, a setup can provide only read access for users, and write access
for a CI that builds and pushes charts to your repository.

## Usage

For now let's omit the process of uploading repository index and charts to s3 and assume
you already have your repository `index.yaml` file on s3 under path `s3://bucket-name/charts/index.yaml`
and a chart archive `epicservice-0.5.1.tgz` under path `s3://bucket-name/charts/epicservice-0.5.1.tgz`.

Add your repository:

    $ helm repo add coolcharts s3://bucket-name/charts
    
Now you can use it as any other Helm chart repository.
Try:

    $ helm search coolcharts
    NAME                       	VERSION	  DESCRIPTION
    coolcharts/epicservice	    0.5.1     A Helm chart.
    
    $ helm install coolchart/epicservice --version "0.5.1"

Fetching also works:

    $ helm fetch s3://bucket-name/charts/epicservice-0.5.1.tgz

### Init

To create a new repository, use **init**:

    $ helm s3 init s3://bucket-name/charts

This command generates an empty **index.yaml** and uploads it to the S3 bucket 
under `/charts` key.

To work with this repo by it's name, first you need to add it using native helm command:

    $ helm repo add mynewrepo s3://bucket-name/charts

### Push

Now you can push your chart to this repo:

    $ helm s3 push ./epicservice-0.7.2.tgz mynewrepo

On push, remote repo index is automatically updated. To sync your local index, run:

    $ helm repo update

Now your pushed chart is available:

    $ helm search mynewrepo 
    NAME                    VERSION	 DESCRIPTION
    mynewrepo/epicservice   0.7.2    A Helm chart.

### Delete

To delete specific chart version from the repository:

    $ helm s3 delete epicservice --version 0.7.2 mynewrepo

As always, remote repo index updated automatically again. To sync local, run:

    $ helm repo update

The chart is deleted from the repo:

    $ helm search mynewrepo/epicservice 
    No results found

## Uninstall

    $ helm plugin remove s3

## Documentation

Additional documentation is available in the [docs](docs) directory. This currently includes:
- estimated [usage cost calculation](docs/usage-cost.md)
- [best practices](docs/best-practice.md)
for organizing your repositories.

## Contributing

Contributions are welcome. Please see [these instructions](.github/CONTRIBUTING.md)
that will help you to develop the plugin.
    
## License

[MIT](LICENSE)